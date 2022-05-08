---
title: "[ue4] UObject reference finder"
author: eeehjun
date: 2022-02-20 00:00:00 +0900
categories: [Unreal Engine, Manual]
tags: [ue4, manual]
---



<!-- begin preview line -->
<br/>
<!-- end preview line -->


---
## UML
```cpp
 int32 GetUObjectReferences(UObject* Object, TArray<UObject*>& OutReferredToObjects)
{
    if(!Obj || !Obj->IsValidLowLevelFast()) 
    {
        return -1;
    }

    TArray<UObject*> ReferredToObjects;
    FReferenceFinder ObjectReferenceCollector( ReferredToObjects, Obj, false, true, true, false);
    ObjectReferenceCollector.FindReferences( Obj );

    if(OutReferredToObjects)
    {
        OutReferredToObjects->Append(ReferredToObjects);
    }
    return OutReferredToObjects.Num();
}
```
{:file=""}


![](/assets/img/design/template-method-pattern-uml.png)
