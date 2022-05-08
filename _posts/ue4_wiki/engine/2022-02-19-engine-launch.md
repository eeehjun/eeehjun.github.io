---
title: "[ue4] Engine Launch"
author: eeehjun
date: 2022-02-19 00:00:00 +0900
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
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
<!-- end preview line -->


---
## Engine Launch
![](/assets/img/ue/engine-launch-overview.png)

```cpp
int32 GuardedMain( const TCHAR* CmdLine )
{
    EnginePreInit( CmdLine );

    EngineInit();

    while( !IsEngineExitRequested() )
    {
        EngineTick();
    }

    EngineExit();
}
```
{:file="Launch.cpp"}
<br/>

---
## GEngineLoop.PreInit
LoadCoreModules
: _CoreUObject_

LoadPreInitModules
: _Engine_
: _Renderer_
: _AnimGraphRuntime_
: _FPlatformApplicationMisc::LoadPreInitModules();_
: _\<Client> SlateRHIRenderer_
: _Landscape_
: _RenderCore_
: _\<Editor> TextureCompressor_
: _\<Editor> AudioEditor_
: _\<Editor> AnimationModifiers_

AppInit
: _FPlatformMisc::PlatformPreInit();_
: _LoadModules(ELoadingPhase::EarliestPossible)_
: _LoadModules(ELoadingPhase::PostConfigInit)_
: _LoadModulesForProject(ELoadingPhase::PostSplashScreen)_
: _LoadModulesForProject(ELoadingPhase::PreEarlyLoadingScreen)_

LoadStartupCoreModules
: _Core_
: _Networking_
: _FPlatformApplicationMisc::LoadStartupModules();_
: _Messaging_
: _\<Client> MRMesh_
: _\<Editor> UnrealEd_
: _\<Editor> EditorStyle_
: _\<Client> LandscapeEditorUtilities_
: _\<Client> SlateCore_
: _\<Client> UMG_
: _\<Editor> CollisionAnalyzer_
: _\<Editor> BehaviorTreeEditor_
: _\<Editor> GameplayTasksEditor_
: _\<Editor> AudioEditor_
: _\<Client> Overlay_
: _MediaAssets_
: _ClothingSystemRuntimeNv_
: _\<Editor> ClothingSystemEditor_
: _PacketHandler_
: _NetworkReplayStreaming_

PreInitPostStartupScreen
: _LoadModules(ELoadingPhase::EarliestPossible)_
: _LoadModules(ELoadingPhase::PreDefault)_
: _LoadModules(ELoadingPhase::Default)_
: _LoadModules(ELoadingPhase::PostDefault)_

---
1. 모듈이 로드될 때, 엔진은 해당 모듈에 정의된 모든 UObject 클래스 등록
`FModuleDescriptor::LoadModulesForPhase`
- Replection 시스템에 의해 클래스들을 인식
- 각 CDO(Class-Default-Object)를 구성 [CDO란 기본 상태의 클래스]

2. IModuleInterface::StartupModule 호출

<br/>

---
## GEngineLoop.Init

```cpp
int32 FEngineLoop::Init()
{
    GEngine = NewObject<UEngine>(GetTransientPackage(), EngineClass);
    GEngine->ParseCommandline();
    GEngine->Init(this);
    FCoreDelegates::OnPostEngineInit.Broadcast();

    IProjectManager::Get().LoadModulesForProject(ELoadingPhase::PostEngineInit);
    IPluginManager::Get().LoadModulesForEnabledPlugins(ELoadingPhase::PostEngineInit);

    GEngine->Start();
    GIsRunning = true;
    FCoreDelegates::OnFEngineLoopInitComplete.Broadcast();
}
```
{:file="LaunchEngineLoop.cpp"}

<b>UGameEngine::Init</b>
- Create GameInstance
- \<Client> Create GameViewportClient
- \<Client> Create LocalPlayer

<b>UGameEngine::Start</b>
- GameInstance->StartGameInstance
  - UEngine::Browse
    - UEngine::LoadMap
