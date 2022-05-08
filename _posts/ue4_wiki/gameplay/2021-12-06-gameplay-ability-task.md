---
title: Gameplay ability task
author: eeehjun
date: 2021-12-05 08:35:00 +0900
categories: [Unreal Engine, Wiki]
tags: [ue4, Gameplay]
---


![](/assets/img/ue/gameplay-ability-wait-net-sync.png)

## AbilityTask_NetworkSyncPoint

```cpp
void UAbilityTask_NetworkSyncPoint::Activate()
{
    if (IsPredictingClient())
    {
        if (SyncType != EAbilityTaskNetSyncType::OnlyServerWait )
        {
            // As long as we are waiting (!= OnlyServerWait), listen for the GenericSignalFromServer event
            ReplicatedEventToListenFor = EAbilityGenericReplicatedEvent::GenericSignalFromServer;
        }
        if (SyncType != EAbilityTaskNetSyncType::OnlyClientWait)
        {
            // As long as the server is waiting (!= OnlyClientWait), send the Server and RPC for this signal
            AbilitySystemComponent->ServerSetReplicatedEvent(EAbilityGenericReplicatedEvent::GenericSignalFromClient, GetAbilitySpecHandle(), GetActivationPredictionKey(), AbilitySystemComponent->ScopedPredictionKey);
        }
    }
    else if (IsForRemoteClient())
    {
        if (SyncType != EAbilityTaskNetSyncType::OnlyClientWait )
        {
            // As long as we are waiting (!= OnlyClientWait), listen for the GenericSignalFromClient event
            ReplicatedEventToListenFor = EAbilityGenericReplicatedEvent::GenericSignalFromClient;
        }
        if (SyncType != EAbilityTaskNetSyncType::OnlyServerWait)
        {
            // As long as the client is waiting (!= OnlyServerWait), send the Server and RPC for this signal
            AbilitySystemComponent->ClientSetReplicatedEvent(EAbilityGenericReplicatedEvent::GenericSignalFromServer, GetAbilitySpecHandle(), GetActivationPredictionKey());
        }
    }

    if (ReplicatedEventToListenFor != EAbilityGenericReplicatedEvent::MAX)
    {
        CallOrAddReplicatedDelegate(ReplicatedEventToListenFor, FSimpleMulticastDelegate::FDelegate::CreateUObject(this,         &UAbilityTask_NetworkSyncPoint::OnSignalCallback));
    }
    else
    {
        // We aren't waiting for a replicated event, so the sync is complete.
        SyncFinished();
    }
}
```




