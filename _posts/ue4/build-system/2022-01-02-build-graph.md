---
title: "[ue4] Build Graph"
author: eeehjun
date: 2021-12-24 00:32:00 +0900
categories: [Unreal Engine, Build System]
tags: [ue4]
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


## Build steps





## Command-line parameters


D:\Git\UE4\Engine\Source\Programs\AutomationTool\AutomationUtils\ProjectParams.cs

### basic parameters
```console
-CrashReporter                      true if we should build crash reporter
-clean                              wipe intermediate folders before building
-signedPak                          true if it use encrypted pak files
-signpak=keys                       sign the generated pak file with the specified key, i.e.
                                    -signpak=C:\Encryption.keys. Also implies -signedpak.
-RunAssetNativization               convert blueprint assets to source code
-pak                                generate a pak file
-skippak                            use a pak file, but assume it is already built, implies pak
-UTF8Output                         로그, 콘솔 창 및 기타 출력은 UTF8 인코딩을 사용합니다.
```

### build parameters
```console
-build                              True if build step should be executed
-SkipBuildClient                    Trun if then don't build the client exe
-SkipBuildEditor                    True if then don't build the editor exe
-noxge                              True if XGE should NOT be used for building
-ForceDebugInfo                     Whether to force debug info to be generated.
```

### stage parameters
```console
-stage                              put this build in a stage directory
-skipstage                          uses a stage directory, but assumes everything is already there, implies -stage
-stagingdirectory=Path              Directory to copy the builds to, i.e. -stagingdirectory=C:\Stage
-nodebuginfo                        do not copy debug files to the stage
-nocleanstage                       skip cleaning the stage directory
-StageCommandline=XYZ               command line to put into the stage in UE4CommandLine.txt 
```

### cook parameters
```console
-cook,                              -cookonthefly Determines if the build is going to use cooked data
-skipcook                           use a cooked build, but we assume the cooked data is up to date and where it
                                    belongs, implies -cook
-IgnoreCookErrors                   Ignores cook errors and continues with packaging etc
-CookFlavor                         Multi/ATC/DXT/ETC1/ETC2/PVRTC/ASTC: Android cook formatting
-CookPartialgc                      while cooking clean up packages as we are done with them rather then cleaning
                                    everything up when we run out of space
-CookInEditor                       Did we cook in the editor instead of in UAT
-CookOutputDir=C:\cooked            Output directory for cooked data. 
                                    Default: Project/Saved/Cooked(UAT) & Project/Saved/EditorCooked(Editor)
-AdditionalCookerOptions=xxx        Additional cooker options to include on the cooker commandline
-Compressed                         Compress packages during cook.
-EncryptIniFiles                    encrypt ini files in the pak file
-EncryptEverything                  Encrypt all files in the pak file. Secure, but will cause some slowdown to runtime IO 
                                    performance, and high entropy to packaged data which will be bad for patching
-EncryptPakIndex                    Encrypt the pak index, making it impossible to use unrealpak to manipulate the pak file
                                    without the encryption key
-UnversionedCookedContent           Do not include a version number in the cooked content
-IterativeCooking(-Iterate)         Uses the iterative cooking
-CookAll                            Cook all the things in the content directory for this project
-CookMapsOnly                       Only cook maps (and referenced content) instead of cooking everything only affects -cookall flag
-MapsToCook=map1+map2+map3          List of maps to include when no other map list is specified on commandline
-SkipCookingEditorContent           Skips content under /Engine/Editor when cooking
-NumCookersToSpawn=n                number of additional cookers to spawn while cooking
-FastCook                           Uses fast cook path if supported by target
```

### run parameters
```console
-실행: 빌드 완료 후 게임 실행
-CookOnTheFly: 서버에서 쿠킹된 리소스 사용
-CookOnTheFlyStreaming: 위와 동일하지만 리소스를 로컬로 캐시하지 않습니다.
-FileServer: UnrealFileServer에서 쿠킹된 리소스 데이터 사용
-DedicatedServer(-Server): 빌드 완료 후 ds 서버 실행
-Client: TargetType.Client에 해당하는 구성을 사용하여 게임 실행
-NoClient: 서버만 실행
-LogWindow: 로그 창 생성
-Map=xxx: 게임이 실행되는 레벨 지정
-AdditionalServerMapParams=?param=value: 서버 맵의 추가 매개변수
-NumClients=n: 클라이언트 수
-AddCmdline=/-ServerCommandline=/-ClientCommandline=xx: 추가 프로세스 매개변수
```

### package parameters
```console
-package                           package the project for the target platform
-skippackage                       Skips packaging the project for the target platform
-distribution                      package for distribution the project
-prereqs                           stage prerequisites along with the project
```

### archive parameters
```console
-archive                           put this build in an archive directory
-archivedirectory=Path             Directory to archive the builds to, i.e. -archivedirectory=C:\Archive
-createappbundle                   When archiving for Mac, set this to true to package it in a .app bundle instead
                                   of normal loose files
```

### deploy parameters
```console
-deploy                            deploy the project for the target platform
-DeployFolder                      Location to deploy to on the target platform
```






```console
-project=Path                      Project path (required), i.e: -project=QAGame,
                                   -project=Samples\BlackJack\BlackJack.uproject,
                                   -project=D:\Projects\MyProject.uproject
-destsample                        Destination Sample name
-foreigndest                       Foreign Destination
-targetplatform=PlatformName       target platform for building, cooking and deployment (also -Platform)
-servertargetplatform=PlatformName target platform for building, cooking and deployment of the dedicated server
                                   (also -ServerPlatform)
-foreign                           Generate a foreign uproject from blankproject and use that
-foreigncode                       Generate a foreign code uproject from platformergame and use that


-skipcookonthefly                  in a cookonthefly build, used solely to pass information to the package step

-unattended                        assumes no operator is present, always terminates without waiting for something.

-iostore                           generate I/O store container file(s)

-prepak                            attempt to avoid cooking and instead pull pak files from the network, implies pak
                                   and skipcook
-signed                            the game should expect to use a signed pak file.
-PakAlignForMemoryMapping          The game will be set up for memory mapping bulk data.

-skipiostore                       override the -iostore commandline option to not run it


-manifests                         generate streaming install manifests when cooking data
-createchunkinstall                generate streaming install data from manifest when cooking data, requires -stage
                                   & -manifests







-separatedebuginfo                 output debug info to a separate directory
-MapFile                           generates a *.map file

-run                               run the game after it is built (including server, if -server)
-cookonthefly                      run the client with cooked data provided by cook on the fly server
-Cookontheflystreaming             run the client in streaming cook on the fly mode (don't cache files locally
                                   instead force reget from server each file load)
-fileserver                        run the client with cooked data provided by UnrealFileServer
-dedicatedserver                   build, cook and run both a client and a server (also -server)
-client                            build, cook and run a client and a server, uses client target configuration
-noclient                          do not run the client, just run the server
-logwindow                         create a log window for the client




-applocaldir                       location of prerequisites for applocal deployment
-Prebuilt                          this is a prebuilt cooked and packaged build
-AdditionalPackageOptions          extra options to pass to the platform's packager

-getfile                           download file from target after successful run
-IgnoreLightMapErrors              Whether Light Map errors should be treated as critical

-ue4exe=ExecutableName             Name of the UE4 Editor executable, i.e. -ue4exe=UE4Editor.exe

-archivemetadata                   Archive extra metadata files in addition to the build (e.g. build.properties)

-iterativecooking                  Uses the iterative cooking, command line: -iterativecooking or -iterate
-CookMapsOnly                      Cook only maps this only affects usage of -cookall the flag
-CookAll                           Cook all the things in the content directory for this project
-SkipCookingEditorContent          Skips content under /Engine/Editor when cooking
-FastCook                          Uses fast cook path if supported by target
-cmdline                           command line to put into the stage in UE4CommandLine.txt
-bundlename                        string to use as the bundle name when deploying to mobile device
-map                               map to run the game with
-AdditionalServerMapParams         Additional server map params, i.e ?param=value
-device                            Devices to run the game on
-serverdevice                      Device to run the server on
-skipserver                        Skip starting the server
-numclients=n                      Start extra clients, n should be 2 or more
-addcmdline                        Additional command line arguments for the program
-servercmdline                     Additional command line arguments for the program
-clientcmdline                     Override command line arguments to pass to the client
-nullrhi                           add -nullrhi to the client commandlines
-fakeclient                        adds ?fake to the server URL
-editortest                        rather than running a client, run the editor instead
-RunAutomationTests                when running -editortest or a client, run all automation tests, not compatible
                                   with -server
-Crash=index                       when running -editortest or a client, adds commands like debug crash, debug
                                   rendercrash, etc based on index
-deviceuser                        Linux username for unattended key genereation
-devicepass                        Linux password
-RunTimeoutSeconds                 timeout to wait after we lunch the game
-SpecifiedArchitecture             Determine a specific Minimum OS
-UbtArgs                           extra options to pass to ubt
-MapsToRebuildLightMaps            List of maps that need light maps rebuilding
-MapsToRebuildHLODMaps             List of maps that need HLOD rebuilding
-ForceMonolithic                   Toggle to combined the result into one executable
-ForceDebugInfo                    Forces debug info even in development builds
-ForceNonUnity                     Toggle to disable the unity build system
-ForceUnity                        Toggle to force enable the unity build system
-Licensee                          If set, this build is being compiled by a licensee
-NoSign                            Skips signing of code/content files.
```