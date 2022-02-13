---
title: Unreal Cast
author: eeehjun
date: 2021-01-03 18:32:00 -0500
categories: [Unreal Engine, Engine]
tags: [ue4]
---

```cpp
// Dynamically cast an object type-safely.
template <typename To, typename From>
FORCEINLINE To* Cast(From* Src)
{
    return TCastImpl<From, To>::DoCast(Src);
}
```
{: file="Casts.h"}


## Cast Teamplate

```cpp
template <typename From, typename To, ECastType CastType = TGetCastType<From, To>::Value>
struct TCastImpl
{
    // This is the cast flags implementation
    FORCEINLINE static To* DoCast( UObject* Src )
    {
        return Src && Src->GetClass()->HasAnyCastFlag(TCastFlags<To>::Value) ? (To*)Src : nullptr;
    }
    
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( UObject* Src )
    {
        return (To*)Src;
    }
    
    UE_DEPRECATED(4.25, "Cast<>() and CastChecked<>() should not be used with FProperties. Use CastField<>() or CastFieldChecked<>() instead.")
    FORCEINLINE static To* DoCast( FField* Src )
    {
        return Src && Src->IsA<To>() ? (To*)Src : nullptr;
    }
    
    UE_DEPRECATED(4.25, "Cast<>() and CastChecked<>() should not be used with FProperties. Use CastField<>() or CastFieldChecked<>() instead.")
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( FField* Src )
    {
        return (To*)Src;
    }
};

template <typename From, typename To>
struct TCastImpl<From, To, ECastType::UObjectToUObject>
{
    FORCEINLINE static To* DoCast( UObject* Src )
    {
        return Src && Src->IsA<To>() ? (To*)Src : nullptr;
    }
    
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( UObject* Src )
    {
        return (To*)Src;
    }
    
    UE_DEPRECATED(4.25, "Cast<>() and CastChecked<>() should not be used with FProperties. Use CastField<>() or CastFieldChecked<>() instead.")
    FORCEINLINE static To* DoCast( FField* Src )
    {
        return Src && Src->IsA<To>() ? (To*)Src : nullptr;
    }
    
    UE_DEPRECATED(4.25, "Cast<>() and CastChecked<>() should not be used with FProperties. Use CastField<>() or CastFieldChecked<>() instead.")
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( FField* Src )
    {
        return (To*)Src;
    }
};

template <typename From, typename To>
struct TCastImpl<From, To, ECastType::InterfaceToUObject>
{
    FORCEINLINE static To* DoCast( From* Src )
    {
        To* Result = nullptr;
        if (Src)
        {
            UObject* Obj = Src->_getUObject();
            if (Obj->IsA<To>())
            {
                Result = (To*)Obj;
            }
        }
        return Result;
    }
    
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( From* Src )
    {
        return Src ? (To*)Src->_getUObject() : nullptr;
    }
};

template <typename From, typename To>
struct TCastImpl<From, To, ECastType::UObjectToInterface>
{
    FORCEINLINE static To* DoCast( UObject* Src )
    {
        return Src ? (To*)Src->GetInterfaceAddress(To::UClassType::StaticClass()) : nullptr;
    }
    
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( UObject* Src )
    {
        return Src ? (To*)Src->GetInterfaceAddress(To::UClassType::StaticClass()) : nullptr;
    }
};

template <typename From, typename To>
struct TCastImpl<From, To, ECastType::InterfaceToInterface>
{
    FORCEINLINE static To* DoCast( From* Src )
    {
        return Src ? (To*)Src->_getUObject()->GetInterfaceAddress(To::UClassType::StaticClass()) : nullptr;
    }
    
    FORCEINLINE static To* DoCastCheckedWithoutTypeCheck( From* Src )
    {
        return Src ? (To*)Src->_getUObject()->GetInterfaceAddress(To::UClassType::StaticClass()) : nullptr;
    }
};
```
{: file="Casts.h"}



## IsA

```cpp
/** Returns true if this object is of the specified type. */
template <typename OtherClassType>
FORCEINLINE bool IsA( OtherClassType SomeBase ) const
{
    // We have a cyclic dependency between UObjectBaseUtility and UClass,
    // so we use a template to allow inlining of something we haven't yet seen, because it delays compilation until the function is called.
    
    // 'static_assert' that this thing is actually a UClass pointer or convertible to it.
    const UClass* SomeBaseClass = SomeBase;
    (void)SomeBaseClass;
    checkfSlow(SomeBaseClass, TEXT("IsA(NULL) cannot yield meaningful results"));
    
    const UClass* ThisClass = GetClass();
    
    // Stop the compiler doing some unnecessary branching for nullptr checks
    UE_ASSUME(SomeBaseClass);
    UE_ASSUME(ThisClass);
    
    return IsChildOfWorkaround(ThisClass, SomeBaseClass);
}

/** Returns true if this object is of the template type. */
template<class T>
bool IsA() const
{
    return IsA(T::StaticClass());
}

template <typename ClassType>
static FORCEINLINE bool IsChildOfWorkaround(const ClassType* ObjClass, const ClassType* TestCls)
{
    return ObjClass->IsChildOf(TestCls);
}
```
{: file="UObjectBaseUtility.h"}



## IsChildOf

```cpp
bool UStruct::IsChildOf( const UStruct* SomeBase ) const
{
    if (SomeBase == nullptr)
    {
        return false;
    }

    bool bOldResult = false;
    for ( const UStruct* TempStruct=this; TempStruct; TempStruct=TempStruct->GetSuperStruct() )
    {
    if ( TempStruct == SomeBase )
    {
        bOldResult = true;
        break;
    }
}

#if USTRUCT_FAST_ISCHILDOF_IMPL == USTRUCT_ISCHILDOF_STRUCTARRAY
    const bool bNewResult = IsChildOfUsingStructArray(*SomeBase);
#endif

#if USTRUCT_FAST_ISCHILDOF_COMPARE_WITH_OUTERWALK
    ensureMsgf(bOldResult == bNewResult, TEXT("New cast code failed"));
#endif

    return bOldResult;
}
```
{: file="Class.cpp"}


```cpp
TSharedPtr<SWidget> widget;
if (widget->GetType() == "SObjectWidget")
{
    SObjectWidget* objectWidget = static_cast<SObjectWidget*>(widget.Get());
}
```
{: file="Slate cast.h"}


https://answers.unrealengine.com/questions/214303/casting-slate-widgets.html




