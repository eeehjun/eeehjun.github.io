---
title: Blueprint
author: eeehjun
date: 2021-12-23 05:27:00 +0900
categories: [Unreal Engine, Wiki]
tags: [ue4, Engine]
---


<!-- begin preview line -->
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
&emsp;&emsp;&emsp;&emsp;
<!-- end preview line -->


## Class Diagram

### UBlueprint
![](/assets/img/ue/blueprint-diagram.png)



---

## Actor에서 블루프린트 CPP 함수를 호출 과정


### 1. 글루 기능 정의

> UBT에 의해 리플렉션 코드 xyz.generated.h와 xyz.gen.cpp가 생성

```cpp
UFUNCTION(BlueprintCallable, BlueprintAuthorityOnly, Category=Replication)
void SetReplicates(bool bInReplicates);
```
{: file="Actor.h"}

```cpp
void AActor::StaticRegisterNativesAActor()
{
    UClass* Class = AActor::StaticClass();
    static const FNameNativePtrPair Funcs[] = {
        // ...
        { "SetReplicates", &AActor::execSetReplicates },
        // ...
    };
    FNativeFunctionRegistrar::RegisterFunctions(Class, Funcs, UE_ARRAY_COUNT(Funcs));
}
```
{: file="Actor.gen.cpp"}



### 2. 런타임 상에 글루 기능 등록

> 모듈 시작 시 클래스를 등록하면서 IMPLEMENT_CLASS에 정의된 AutoInitialize 함수를 호출해준다.

```cpp
// Register a class at startup time.
IMPLEMENT_CLASS(AActor, 2133058577);
```
{: file="Actor.gen.cpp"}


IMPLEMENT_CLASS는 




2. 글루 기능 등록