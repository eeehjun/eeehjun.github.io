---
title: "[ue4] Gabage Collection"
author: eeehjun
date: 2022-02-18 00:00:00 +0900
categories: [Unreal Engine, Wiki]
tags: [ue4, Engine]
---

언리얼 엔진에서는 가비지 컬렉션 방식 중 하나인 Mark and sweep 방식을 사용한다. 
이 방식은 Root기반으로 참조된 노드를 순회하며 참조된 노드들은 마킹(Mark)하고, 마킹되지 않은 오브젝트들은 제거(Sweep)하는 방식이다.

<!-- begin preview line -->
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
<!-- end preview line -->


---
## Mark and sweep
![](/assets/img/ue/garbage-collection-mark-and-sweep.png)

<br/>

---
## Unreal garbage collection

### overview
![](/assets/img/ue/garbage-collection-process.png)

```cpp
/** 
 * Deletes all unreferenced objects, keeping objects that have any of the passed in KeepFlags set
 *
 * @param	KeepFlags			objects with those flags will be kept regardless of being referenced or not
 * @param	bPerformFullPurge	if true, perform a full purge after the mark pass
 */
void CollectGarbageInternal(EObjectFlags KeepFlags, bool bPerformFullPurge)
{
    //@ Mark
    {
        const double StartTime = FPlatformTime::Seconds();
        FRealtimeGC TagUsedRealtimeGC;
        TagUsedRealtimeGC.PerformReachabilityAnalysis(KeepFlags, bForceSingleThreadedGC, bWithClusters);
        UE_LOG(LogGarbage, Log, TEXT("%f ms for GC"), (FPlatformTime::Seconds() - StartTime) * 1000);
    }

    //@ Sweep
    {
        GatherUnreachableObjects(bForceSingleThreadedGC);
        NotifyUnreachableObjects(GUnreachableObjects);

        // This needs to happen after GatherUnreachableObjects since GatherUnreachableObjects can mark more (clustered) objects as unreachable
        FGCArrayPool::Get().ClearWeakReferences(bPerformFullPurge);

        if (bPerformFullPurge || !GIncrementalBeginDestroyEnabled)
        {
            UnhashUnreachableObjects(/**bUseTimeLimit = */ false);
            FScopedCBDProfile::DumpProfile();
        }
    }
}
```
{:file="GarbageCollection.cpp"}

<br/>

### FGCReferenceTokenStream
![](/assets/img/ue/garbage-collection-token-stream.png)

UClass에 정의된 프로퍼티들의 토큰으로 캐싱한다.

```cpp
/** 
 * Convenience struct containing all necessary information for a reference.
 */
struct FGCReferenceInfo
{
    union
    {
        struct
        {
            /** Return depth, e.g. 1 for last entry in an array, 2 for last entry in an array of structs of arrays, ... */
            uint32 ReturnCount  : 8;
            /** Type of reference */
            uint32 Type         : 5; // The number of bits needs to match TFastReferenceCollector::FStackEntry::ContainerHelperType
            /** Offset into struct/ object */
            uint32 Offset       : 19;
        };
        /** uint32 value of reference info, used for easy conversion to/ from uint32 for token array */
        uint32 Value;
    };
};
```
{:file="GarbageCollection.h"}

<br/>

UClass::AssembleReferenceTokenStream
: GC를 위한 클래스 별 Token stream
: 런타임 상에 한번만 실행

![](/assets/img/ue/garbage-collection-token-stream-process.png)

<br/>

### Mark process
```cpp
void PerformReachabilityAnalysis(EObjectFlags KeepFlags, bool bForceSingleThreaded, bool bWithClusters)
{
    /** Growing array of objects that require serialization */
    FGCArrayStruct* ArrayStruct = FGCArrayPool::Get().GetArrayStructFromPool();
    TArray<UObject*>& ObjectsToSerialize = ArrayStruct->ObjectsToSerialize;

    // Reset object count.
    GObjectCountDuringLastMarkPhase.Reset();

    // Make sure GC referencer object is checked for references to other objects even if it resides in permanent object pool
    if (FPlatformProperties::RequiresCookedData() && FGCObject::GGCObjectReferencer && GUObjectArray.IsDisregardForGC(FGCObject::GGCObjectReferencer))
    {
        ObjectsToSerialize.Add(FGCObject::GGCObjectReferencer);
    }

    {
        const double StartTime = FPlatformTime::Seconds();
        (this->*MarkObjectsFunctions[GetGCFunctionIndex(!bForceSingleThreaded, bWithClusters)])(ObjectsToSerialize, KeepFlags);
        UE_LOG(LogGarbage, Verbose, TEXT("%f ms for MarkObjectsAsUnreachable Phase (%d Objects To Serialize)"), (FPlatformTime::Seconds() - StartTime) * 1000, ObjectsToSerialize.Num());
    }

    {
        const double StartTime = FPlatformTime::Seconds();
        PerformReachabilityAnalysisOnObjects(ArrayStruct, bForceSingleThreaded, bWithClusters);
        UE_LOG(LogGarbage, Verbose, TEXT("%f ms for Reachability Analysis"), (FPlatformTime::Seconds() - StartTime) * 1000);
    }
}
```
{:file="UObjectGlobals.cpp"}


### Sweep process