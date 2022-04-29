---
title: "UE4 Editor"

categories:
  - UE4STUDY


tags:
  - UE4
  - ueeditor


toc: true
toc_label: UE4 Editor
toc_sticky: true

sidebar_main: true
---

다른 개발자들을 위한 프로젝트 특성에 맞는 사용자 지정 도구를 만듭니다.

<br/>

# 새 편집기 모듈

엔진이 편집기 모드에서 실행될 때만 로드되는 새 모듈을 작성합니다.

`[Gamename].uproject` 파일을 열어 아래 내용을 추가합니다.

```json
{
	"FileVersion": 3,
	"EngineAssociation": "4.25",
	"Category": "",
	"Description": "",
	"Modules": [
		{
			"Name": "temp",
			"Type": "Runtime",
			"LoadingPhase": "Default",
			"AdditionalDependencies": [
				"CoreUObject",
				"Engine"
			]
		},
		---------------추가내용------------------
		{
			"Name": "temp_editor",
			"Type": "Editor",
			"LoadingPhase": "PostEngineInit",
			"AdditionalDependencies": [
				"CoreUObject",
				"Engine"
			]
		}
		----------------------------------------
	]
}
```

탐색기에서 `[gamename]/Source/temp_editor.Target.cs` 파일을 생성하고 아래처럼 작성합니다.

```cs
using UnrealBuildTool;
using System.Collections.Generic;

public class temp_editorTarget : TargetRules
{
	public temp_editorTarget(TargetInfo Target) : base(Target)
	{
		Type = TargetType.Editor;
		DefaultBuildSettings = BuildSettingsVersion.V2;

		ExtraModuleNames.AddRange( new string[] { "temp_editor" } );
	}
}
```

탐색기에서 `[gamename]/Source/temp_editor/temp_editor.Build.cs` 파일을 생성하고 아래처럼 작성합니다.

```cs
using UnrealBuildTool;

public class temp_editor : ModuleRules
{
	public temp_editor(ReadOnlyTargetRules Target) : base(Target)
	{
		PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;
	
		PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore", "RHI", "RenderCore", /*"ShaderCore",*/ "MainFrame", "AssetTools", "AppFramework", "PropertyEditor"});
		PublicDependencyModuleNames.Add("temp");
		PrivateDependencyModuleNames.AddRange(new string[] { "UnrealEd", "Slate", "SlateCore", "EditorStyle", "GraphEditor", "BlueprintGraph" });
	}
}
```

탐색기에서 `[gamename]/Source/temp_editor/temp_editor.h`,`[gamename]/Source/temp_editor/temp_editor.cpp`  파일을 생성하고 아래처럼 작성합니다.

```cpp
#pragma once

#include "Engine.h"
#include "Modules/ModuleInterface.h"
#include "Modules/ModuleManager.h"
#include "UnrealEd.h"

class Ftemp_editiorModule: public IModuleInterface
{
};
```

```cpp
#include "temp_editor.h"
#include "Modules/ModuleManager.h"
#include "Modules/ModuleInterface.h"

IMPLEMENT_GAME_MODULE(Ftemp_editiorModule, temp_editor)
```

이제 새 모듈을 사용할 수 있습니다.

<br/>

## 예제 분석

언리얼 프로젝트(.uproject)는 json 스타일 형식을 사용하며 프로젝트에 대한 다양한 정보를 관리합니다. 이 정보는 프로젝트를 구성하는 모듈에 대한 정보를 헤더 및 빌드 도구에 알리는 데 사용되며, 코드 생성과 makefile 생성에 사용됩니다.

uproject에 포함되는 정보는 아래와 같습니다.

* 엔진 버전
* 프로젝트 모듈 목록
* 모듈 선언 목록

모듈 선언은 아래 내용을 포함합니다.

* 모듈 이름
* 모듈의 타입
* 모듈의 로딩 단계
* 모듈의 종속성 목록

uproject에서 새 모듈이 포함하도록 변경되면 이를 위한 빌드 스크립트가 필요합니다.

빌드 스크립트는 C# 으로 작성되고 `[ModuleName].Build.cs` 로 명명합니다.

`IMPLEMENT_GAME_MODULE`는 `InitializeModule()`를 선언하며, 새 모듈 클래스의 인스턴스를 반환합니다.

<br/>

# 새 툴바 버튼

툴바에 새로운 버튼을 추가해봅니다.

만든 모듈에 새 클래스를 추가하고 아래처럼 버튼에 대한 정보를 작성합니다.

```cpp
#pragma once

#include "Framework/Commands/Commands.h"
#include "EditorStyleSet.h"

class FToolbarCmd: public TCommands<FToolbarCmd>
{
public:
	FToolbarCmd()
		: TCommands<FToolbarCmd>(
			FName(TEXT("UE4_Cookbook")),
			FText::FromString("Cookbook Commands"),
			NAME_None,
			FEditorStyle::GetStyleSetName())
	{};

	virtual void RegisterCommands() override
	{
#define LOCTEXT_NAMESPACE ""
		UI_COMMAND(MyButton, "Cookbook", "Demo Cookbook Toolbar Command", EUserInterfaceActionType::Button, FInputGesture());
		UI_COMMAND(MyMenuButton, "Cookbook", "Demo Cookbook Toolbar Command", EUserInterfaceActionType::Button, FInputGesture());
#undef LOCTEXT_NAMESPACE
	};

	TSharedPtr<FUICommandInfo> MyButton;

	TSharedPtr<FUICommandInfo> MyMenuButton;
};
```

이후 프로젝트의 모듈 파일(temp_editor.h)를 아래처럼 수정합니다.

```cpp
#pragma once

#include "Engine.h"
#include "Modules/ModuleInterface.h"
#include "Modules/ModuleManager.h"
#include "LevelEditor.h" 
#include "SlateBasics.h" 
#include "Framework/MultiBox/MultiBoxExtender.h"
#include "UnrealEd.h"
#include "ToolbarCmd.h"
#include "Interfaces/IMainFrameModule.h"

class Ftemp_editiorModule: public IModuleInterface
{

    virtual void StartupModule() override
    {
        FToolbarCmd::Register();

        TSharedPtr<FUICommandList> CommandList = MakeShareable(new FUICommandList());

        CommandList->MapAction(FToolbarCmd::Get().MyButton, FExecuteAction::CreateRaw(this, &Ftemp_editiorModule::MyButton_Clicked), FCanExecuteAction());


        ToolbarExtender = MakeShareable(new FExtender());

        FLevelEditorModule& LevelEditorModule = FModuleManager::LoadModuleChecked<FLevelEditorModule>("LevelEditor");

        Extension = ToolbarExtender->AddToolBarExtension("Compile", EExtensionHook::Before, CommandList, FToolBarExtensionDelegate::CreateRaw(this, &Ftemp_editiorModule::AddToolbarExtension));
        LevelEditorModule.GetToolBarExtensibilityManager()->AddExtender(ToolbarExtender);
    }

    virtual void ShutdownModule() override
    {
        ToolbarExtender->RemoveExtension(Extension.ToSharedRef());

        Extension.Reset();
        ToolbarExtender.Reset();
    }

    TSharedPtr<FExtender> ToolbarExtender;
    TSharedPtr<const FExtensionBase> Extension;

    void MyButton_Clicked()
    {
        TSharedRef<SWindow> CookbookWindow = SNew(SWindow)
            .Title(FText::FromString(TEXT("Cookbook Window")))
            .ClientSize(FVector2D(800, 400))
            .SupportsMaximize(false)
            .SupportsMinimize(false);

        IMainFrameModule& MainFrameModule =
            FModuleManager::LoadModuleChecked<IMainFrameModule>
            (TEXT("MainFrame"));

        if(MainFrameModule.GetParentWindow().IsValid())
        {
            FSlateApplication::Get().AddWindowAsNativeChild
            (CookbookWindow, MainFrameModule.GetParentWindow()
                .ToSharedRef());
        }
        else
        {
            FSlateApplication::Get().AddWindow(CookbookWindow);
        }

    };

    void AddToolbarExtension(FToolBarBuilder& builder)
    {

        FSlateIcon IconBrush =
            FSlateIcon(FEditorStyle::GetStyleSetName(),
                "LevelEditor.ViewOptions",
                "LevelEditor.ViewOptions.Small"); builder.AddToolBarButton(FToolbarCmd::Get()
                    .MyButton, NAME_None, FText::FromString("My Button"),
                    FText::FromString("Click me to display a message"),
                    IconBrush, NAME_None);

    };
};
```

빌드 후 언리얼 에디터를 재시작합니다.

새로운 눈 아이콘 툴바 버튼이 생성된 것을 확인할 수 있습니다.

![toolbar](/assets/images/ue4/editor_toolbar.png)

<br/>

## 예제 분석

언리얼 에디터 UI는 명령 패턴(Command) 개념을 기반으로 합니다. 따라서, 명령 세트를 포함하는 클래스를 생성하기 위해선 `TCommands`를 상속받아야 합니다.

`TCommands::RegisterCommands` 를 구현해서 명령 오브젝트를 만들 수 있습니다. 멤버 변수인 FUICommandInfo 인스턴스는 명령 클래스 내에 저장되어서 다른 장소에서 바인딩 할 수 있게 됩니다.

UI_COMMAND 매크로는 로컬 네임스페이스를 받습니다. 지역화를 사용할 의도가 없더라도 `LOCTEXT_NAMESPACE`에 유효한 값을 설정하기 위해 `#defines`와 함꼐 사용해야 합니다.

명령 세트는 만들었지만 실제 에디터에 적용하기 위해선 `StartupModule/ShutdownModule`를 작성해야 합니다.

FExtender 클래스를 써서 메뉴나 컨텍스트 메뉴, 툴바를 확장하는데 사용하고 인스턴스를 공유 포인터로 작성해 모듈 종료될 때 확장이 초기화 되지 않도록 합니다.

이후 AddToolBarExtension을 호출해 결과를 공유 포인터에 저장하고 모듈 초기화 해제 시점에 제거할 수 있도록 합니다.

<br/>

# 새 메뉴 버튼

툴바 추가하는 것 처럼 메뉴에도 추가할 수 있습니다. 툴바 추가 작업이 되어있다 가정하고 아래의 내용을 추가합니다.

TCommand를 상속받는 FMenuCmd 클래스를 추가해서 등록할 명령 클래스를 만들어줍니다.

```cpp
#pragma once

#include "Framework/Commands/Commands.h"
#include "EditorStyleSet.h"


class FMenuCmd: public TCommands<FMenuCmd>
{
public:
	FMenuCmd()
		: TCommands<FMenuCmd>(
			FName(TEXT("UE4_Cookbook")),
			FText::FromString("Cookbook Commands"),
			NAME_None,
			FEditorStyle::GetStyleSetName())
	{};

	virtual void RegisterCommands() override
	{
#define LOCTEXT_NAMESPACE ""
		UI_COMMAND(MyButton, "Cookbook", "Demo Cookbook Menu Command", EUserInterfaceActionType::Button, FInputGesture());
		UI_COMMAND(MyMenuButton, "Cookbook", "Demo Cookbook MenuCommand", EUserInterfaceActionType::Button, FInputGesture());
#undef LOCTEXT_NAMESPACE
	};

	TSharedPtr<FUICommandInfo> MyButton;

	TSharedPtr<FUICommandInfo> MyMenuButton;
};
```

이후 temp_editor.h 에서 클릭시 행동을 할 델리게이트 함수를 만들어주고

```cpp
void AddMenuExtension(FMenuBuilder& builder)
{
	FSlateIcon IconBrush =
		FSlateIcon(FEditorStyle::GetStyleSetName(),
			"LevelEditor.ViewOptions",
			"LevelEditor.ViewOptions.Small");

	builder.AddMenuEntry(FMenuCmd::Get().MyButton);
};
```

StartupModule 함수에서 메뉴를 등록하는 기능인 아래 내용을 추가해줍니다.

```cpp
FMenuCmd::Register();

LevelEditorModule.GetToolBarExtensibilityManager()->AddExtender(ToolbarExtender);
Extension = ToolbarExtender->AddMenuExtension("LevelEditor", EExtensionHook::Before, CommandList, FMenuExtensionDelegate::CreateRaw(this, &Ftemp_editiorModule::AddMenuExtension));
LevelEditorModule.GetMenuExtensibilityManager()->AddExtender(ToolbarExtender);
```

![menu](/assets/images/ue4/editor_menu.png)

<br/>

# 새 편집기 창

편집기 창은 개발자가 구성할 수 있는 설정이 포함된 도구가 있거나 사람들에게 정보 표시를 하려는 경우에 유용합니다. 여기선느 위의 툴바 또는 메뉴 버튼 클릭 이벤트에 새 편집기 창을 표시하는 기능을 추가합니다.

Ftemp_editiorModule::MyButton_Clicked 함수의 아래 내용을 수정한다.

```cpp
TSharedRef<SWindow> CookbookWindow = SNew(SWindow)
	.Title(FText::FromString(TEXT("Cookbook Window")))
	.ClientSize(FVector2D(800, 400))
	.SupportsMaximize(false)
	.SupportsMinimize(false)
	[
		SNew(SVerticalBox)
		+ SVerticalBox::Slot()
	.HAlign(HAlign_Center)
	.VAlign(VAlign_Center)
	[
		SNew(STextBlock)
		.Text(FText::FromString(TEXT("Hello from Slate")))
	]
	];
```

이후 툴바나 메뉴 버튼을 누르게 되면 새로운 편집기 창이 나옵니다.

![editor](/assets/images/ue4/editor_window.png)

<br/>

# 새 에셋 타입

컨텐츠 브라우저에서 새로운 에셋 타입을 생성할 수 있도록 새 에셋 클래스를 생성하고 등록해봅니다.

UObject 를 상속받는 새로운 클래스를 만듭니다. 이 클래스는 등록하기 위해 사용됩니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "NewAsset.generated.h"

UCLASS(BlueprintType, Blueprintable)
class TEMP_API UNewAsset : public UObject
{
	GENERATED_BODY()
	
public:
	UPROPERTY(EditAnywhere, Category="CustomAsset")
	FString Name;
};
```

> 아래는 4.25 이전 버전입니다. 4.25 이후 버전은 더 아래 있씁니다.

이후 UFactory 를 상속받는 새로운 클래스를 생성합니다. UFactory를 상속받음으로써 언리얼 에셋 인스턴스를 생성합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Factories/Factory.h"
#include "../temp/NewAsset.h"
#include "AssetFactory.generated.h"

UCLASS()
class TEMP_EDITOR_API UAssetFactory : public UFactory
{
	GENERATED_BODY()
public:
	UAssetFactory()
	{
		Super();
		bCreateNew = true;
		bEditAfterNew = true;
		SupportedClass = UNewAsset::StaticClass();
	}

	virtual UObject* FactoryCreateNew(UClass* InClass, UObject* InParent, FName InName, EObjectFlags Flags, UObject* Context, FFeedbackContext* Warn, FName CallingContext) override
	{
		auto NewObjectAsset = NewObject<UNewAsset>(InParent, InClass, InName, Flags);
		return NewObjectAsset;
	}
};
```

이후 빌드 후 언리얼을 재시작하면 Misc 카테고리에 등록이 되어있는 것을 볼 수 있습니다.

> 4.25 이후 버전입니다.

이후 FAssetTypeActions_Base 를 상속받는 새로운 클래스를 생성합니다. 각각 구현해야 하는 함수를 구현해줍니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "AssetTypeActions_Base.h"
#include "../temp/NewAsset.h"

class FAssetTypeActions: public FAssetTypeActions_Base
{
public:
	FAssetTypeActions(EAssetTypeCategories::Type InAssetCategory)
		: MyAssetCategory(InAssetCategory)
	{

	}

	virtual FColor GetTypeColor() const override
	{
		return FColor::Red;
	}

	virtual void OpenAssetEditor(const TArray<UObject*>& InObjects, TSharedPtr<class IToolkitHost> EditWithinLevelEditor = TSharedPtr<IToolkitHost>()) override
	{
		FSimpleAssetEditor::CreateEditor(EToolkitMode::Standalone, EditWithinLevelEditor, InObjects);
	}

	// IAssetTypeActions Implementation
	virtual FText GetName() const override { return FText::FromName(TEXT("NewAsset")); }
	virtual UClass* GetSupportedClass() const override { return UNewAsset::StaticClass(); }
	virtual uint32 GetCategories() override { return MyAssetCategory; }

private:
	EAssetTypeCategories::Type MyAssetCategory;
};
```

이후 StartUpModule 에 등록해줍니다.

```cpp
IAssetTools& AssetTools = FModuleManager::LoadModuleChecked<FAssetToolsModule>("AssetTools").Get();
EAssetTypeCategories::Type gameAssetCategory = AssetTools.RegisterAdvancedAssetCategory(FName(TEXT("CustomCategory")), FText::FromName(TEXT("CustomCategory")));
TSharedPtr<IAssetTypeActions> actionType = MakeShareable(new FAssetTypeActions(gameAssetCategory));
AssetTools.RegisterAssetTypeActions(actionType.ToSharedRef());
```

이제 추가한 새 에셋을 확인할 수 있습니다.

![newasset](/assets/images/ue4/editor_newasset.png)

<br/>

# 새 컨텍스트 메뉴

새로운 에셋 타입을 만들었다면 그에 알맞은 기능들을 추가해줄 필요가 있습니다. 그리고 이 기능은 이 에셋의 컨텍스트 메뉴에만 추가해주고 다른 타입에는 보이지 않게 해주고 싶을 수 있습니다. 이처럼 특정 에셋을 위한 기능을 컨텍스트 메뉴에 추가해봅니다.

아래 예제는 위의 UNewAsset 이 추가되어 있다고 가정합니다.

우선 컨텍스트 추가를 위한 새로운 클래스를 생성합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "AssetTypeActions_Base.h"
#include "Editor/MainFrame/Public/Interfaces/IMainFrameModule.h"
#include "../temp/NewAsset.h"

class TEMP_EDITOR_API FCustomContextAction: public FAssetTypeActions_Base
{
public:
	FCustomContextAction()
	{

	}

	~FCustomContextAction()
	{

	}

    virtual bool HasActions(const TArray<UObject*>& InObjects) const override
    {
        return true;
    }

    virtual void GetActions(const TArray<UObject*>& InObjects, FMenuBuilder& MenuBuilder) override
    {
        MenuBuilder.AddMenuEntry(
            FText::FromString("CustomAssetAction"),
            FText::FromString("Action from Cookbook Recipe"),
            FSlateIcon(FEditorStyle::GetStyleSetName(), "LevelEditor.ViewOptions"),
            FUIAction(
                FExecuteAction::CreateRaw(this, &FCustomContextAction::MyCustomAssetContext_Clicked),
                FCanExecuteAction()
            ));
    }

    virtual FText GetName() const override
    {
        return FText::FromString(TEXT("My Custom Asset"));
    }

    virtual UClass* GetSupportedClass() const override
    {
        return UNewAsset::StaticClass();
    }

    virtual FColor GetTypeColor() const override
    {
        return FColor::Emerald;
    }

    virtual uint32 GetCategories() override
    {
        return EAssetTypeCategories::Misc;
    }

    void MyCustomAssetContext_Clicked()
    {
        TSharedRef<SWindow> CookbookWindow = SNew(SWindow)
            .Title(FText::FromString(TEXT("Cookbook Window")))
            .ClientSize(FVector2D(800, 400))
            .SupportsMaximize(false)
            .SupportsMinimize(false);

        IMainFrameModule& MainFrameModule = FModuleManager::LoadModuleChecked<IMainFrameModule>(TEXT("MainFrame"));

        if(MainFrameModule.GetParentWindow().IsValid())
        {
            FSlateApplication::Get().AddWindowAsNativeChild(CookbookWindow, MainFrameModule.GetParentWindow().ToSharedRef());
        }
        else
        {
            FSlateApplication::Get().AddWindow(CookbookWindow);
        }

    };
};
```

이후 StartUpModule 에 등록해줍니다.

```cpp
IAssetTools& AssetTools = FModuleManager::LoadModuleChecked<FAssetToolsModule>("AssetTools").Get();
TSharedPtr<IAssetTypeActions> CustomAction = MakeShareable(new FCustomContextAction);
AssetTools.RegisterAssetTypeActions(CustomAction.ToSharedRef());
```

특정 에셋에만 새로운 컨텍스트 메뉴를 추가되었습니다.

![context](/assets/images/ue4/editor_context.png)

<br/>

# 새 콘솔 명령

개발 과정에서 콘솔 명령은 개발자나 테스터가 콘텐츠를 쉽게 우회하거나 현재 실행중인 테스트와 관련 없는 메커니즘을 비활성화 하는 등 매우 유용하게 사용할 수 있습니다. 런타임 중에 함수를 호출할 수 있습니다.

`새 편집기 모듈` 이 완료되어 있어야 합니다.

`Ftemp_editiorModule` 클래스에 아래 내용을 추가합니다.

```cpp
IConsoleCommand* DisplayTestCommand;
IConsoleCommand* DisplayUserSpecifiedWindow;

virtual void StartupModule() override
{
	DisplayTestCommand = IConsoleManager::Get().RegisterConsoleCommand(TEXT("DisplayTestCommandWindow"), TEXT("test"), FConsoleCommandDelegate::CreateRaw(this, &Ftemp_editiorModule::DisplayWindow, FString(TEXT("Test Command Window"))), ECVF_Default);

	DisplayUserSpecifiedWindow = IConsoleManager::Get().RegisterConsoleCommand(TEXT("DisplayWindow"), TEXT("test"), FConsoleCommandWithArgsDelegate::CreateLambda(
		[&](const TArray< FString >& Args)
	{
		FString WindowTitle;
		for(FString Arg : Args)
		{
			WindowTitle += Arg;
			WindowTitle.AppendChar(' ');
		}
		this->DisplayWindow(WindowTitle);
	}
	), ECVF_Default);
}

void DisplayWindow(FString WindowTitle)
{
	TSharedRef<SWindow> CookbookWindow = SNew(SWindow)
		.Title(FText::FromString(WindowTitle))
		.ClientSize(FVector2D(800, 400))
		.SupportsMaximize(false)
		.SupportsMinimize(false);
	IMainFrameModule& MainFrameModule =
		FModuleManager::LoadModuleChecked<IMainFrameModule>
		(TEXT("MainFrame"));
	if(MainFrameModule.GetParentWindow().IsValid())
	{
		FSlateApplication::Get().AddWindowAsNativeChild
		(CookbookWindow, MainFrameModule.GetParentWindow()
			.ToSharedRef());
	}
	else
	{
		FSlateApplication::Get().AddWindow(CookbookWindow);
	}
}
```

이후 레벨을 플레이 하고 `~`를 눌러 콘솔을 불러온 뒤에 명령어를 치면 구현한 기능이 보여지는 것을 확인할 수 있습니다.

![console](/assets/images/ue4/editor_console.png)

<br/>

# 새 BP 그래프 핀 시각화

C++ 클래스의 인스턴스를 변수로 사용하고 싶으면 UCLASS 매크로에서 BlueprintType 을 추가하면 됩니다. 하지만 단순히 UObject 로 취급되기에 멤버에 접근할 수는 없습니다.

하지만 FVector 는 리터럴 값으로 편집할 수 있습니다.

![gpp1](/assets/images/ue4/editor_graphpin_1.png)

이렇게 하기 위해선 Graph Pin 시각화를 사용해야 합니다.

우선 Test 모듈(메인 실행 게임모듈)에서 테스트할 클래스 `UNewAsset` 생성합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "NewAsset.generated.h"

UCLASS(BlueprintType, EditInlineNew)
class TEMP_API UNewAsset : public UObject
{
	GENERATED_BODY()
	
public:
	UPROPERTY(BlueprintReadWrite, EditAnywhere, Category="CustomAsset")
	FString Name;
};
```

TempEditor 모듈에서 SGraphPin 를 상속받는 새 클래스를 생성합니다.

```cpp
#pragma once

#include "SGraphPin.h"
#include "SGraphPinCustomAsset.h"
#include "temp_editor.h"
#include "Widgets/Colors/SColorPicker.h"
#include "./temp/NewAsset.h"

class TEMP_EDITOR_API SGraphPinCustomAsset: public SGraphPin
{
    SLATE_BEGIN_ARGS(SGraphPinCustomAsset) {}
    SLATE_END_ARGS()

    void Construct(const FArguments& InArgs, UEdGraphPin* InPin)
    {
        SGraphPin::Construct(SGraphPin::FArguments(), InPin);
    }
protected:
    virtual FSlateColor GetPinColor() const override { return FSlateColor(FColor::Black); };

    virtual TSharedRef<SWidget> GetDefaultValueWidget() override
    {
        return SNew(SColorPicker)
            .OnColorCommitted(this, &SGraphPinCustomAsset::ColorPicked);

    }

    void ColorPicked(FLinearColor SelectedColor)
    {
        UNewAsset* NewValue = NewObject<UNewAsset>();
        NewValue->ColorName = SelectedColor.ToFColor(false).ToHex();
        GraphPinObj->GetSchema()->TrySetDefaultObject(*GraphPinObj, NewValue);
    }
};
```

TempEditor 모듈에서 FGraphPanelPinFactory를 상속받는 새 클래스를 생성합니다.

```cpp
#pragma once

#include "EdGraphUtilities.h"
#include "../temp/NewAsset.h"
#include "SGraphPinCustomAsset.h"


struct TEMP_EDITOR_API FMyCustomAssetPinFactory : public FGraphPanelPinFactory
{
public:
    virtual TSharedPtr<class SGraphPin> CreatePin(class UEdGraphPin* Pin) const override
    {
        if(Pin->PinType.PinSubCategoryObject == UNewAsset::StaticClass())
        {
            return SNew(SGraphPinCustomAsset, Pin);
        }
        else
        {
            return nullptr;
        }
    };
};
```

이후 Ftemp_editiorModule 에서 아래를 추가합니다.

```cpp
멤버변수
TSharedPtr<FMyCustomAssetPinFactory> PinFactory;

=> StartupModule
PinFactory = MakeShareable(new FMyCustomAssetPinFactory());
FEdGraphUtilities::RegisterVisualPinFactory(PinFactory);

=> ShutdownModule
FEdGraphUtilities::UnregisterVisualPinFactory(PinFactory);
PinFactory.Reset();
```

빌드해서 확인해보면 인자에서 값을 넣을 수 있습니다.

![gpp2](/assets/images/ue4/editor_graphpin_2.png)

<br/>

# Custom Detail Panel

디테일 속성에서 클래스의 멤버를 수정할 수 있도록 커스텀 디테일 패널을 제작해봅니다.

타겟이 될 클래스를 생성합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "NewAsset.generated.h"

UCLASS(Blueprintable)
class TEMP_API UNewAsset : public UObject
{
	GENERATED_BODY()
	
public:
	UPROPERTY(BlueprintReadWrite, EditAnywhere, Category="CustomAsset")
	FString ColorName;
};
```

Detail 패널을 위한 IDetailCustomization 인터페이스를 상속받는 클래스를 생성합니다.

```cpp
#pragma once

#include "./temp/NewAsset.h" 
#include "DetailLayoutBuilder.h" 
#include "IDetailCustomization.h" 
#include "IPropertyTypeCustomization.h"

#include "temp_editor.h" 
#include "IDetailsView.h" 
#include "DetailLayoutBuilder.h" 
#include "DetailCategoryBuilder.h" 
#include "Widgets/Colors/SColorPicker.h"
#include "Widgets/SBoxPanel.h"
#include "DetailWidgetRow.h" 


class FMyCustomAssetDetailsCustomization: public IDetailCustomization
{

public:
    virtual void CustomizeDetails(IDetailLayoutBuilder& DetailBuilder) override
    {
        const TArray< TWeakObjectPtr<UObject> >& SelectedObjects = DetailBuilder.GetDetailsView()->GetSelectedObjects();

        for(int32 ObjectIndex = 0; !MyAsset.IsValid() && ObjectIndex < SelectedObjects.Num(); ++ObjectIndex)
        {
            const TWeakObjectPtr<UObject>& CurrentObject = SelectedObjects[ObjectIndex];
            if(CurrentObject.IsValid())
            {
                MyAsset = Cast<UNewAsset>(CurrentObject.Get());
            }
        }

        DetailBuilder.EditCategory("CustomCategory", FText::GetEmpty(), ECategoryPriority::Important)
            .AddCustomRow(FText::GetEmpty())
            [
                SNew(SVerticalBox)
                + SVerticalBox::Slot()
            .VAlign(VAlign_Center)
            [
                SNew(SColorPicker)
                .OnColorCommitted(this, &FMyCustomAssetDetailsCustomization::ColorPicked)
            ]
            ];
    }

    void ColorPicked(FLinearColor SelectedColor)
    {
        if(MyAsset.IsValid())
        {
            MyAsset.Get()->ColorName = SelectedColor.ToFColor(false).ToHex();
        }
    }

    static TSharedRef<IDetailCustomization> MakeInstance()
    {
        return MakeShareable(new FMyCustomAssetDetailsCustomization);
    }

    TWeakObjectPtr<class UNewAsset> MyAsset;
};

class FMyCustomAssetPropertyDetails: public IPropertyTypeCustomization
{
public:
    void ColorPicked(FLinearColor SelectedColor)
    {
        if(MyAsset)
        {
            MyAsset->ColorName = SelectedColor.ToFColor(false).ToHex();
        }
    }

    static TSharedRef<IPropertyTypeCustomization> MakeInstance()
    {
        return MakeShareable(new FMyCustomAssetPropertyDetails);
    }

    UNewAsset* MyAsset;
    virtual void CustomizeChildren(TSharedRef<IPropertyHandle> PropertyHandle, IDetailChildrenBuilder& ChildBuilder, IPropertyTypeCustomizationUtils& CustomizationUtils) override
    {

    }

    virtual void CustomizeHeader(TSharedRef<IPropertyHandle> PropertyHandle, FDetailWidgetRow& HeaderRow, IPropertyTypeCustomizationUtils& CustomizationUtils) override
    {
        UObject* PropertyValue = nullptr;
        auto GetValueResult = PropertyHandle->GetValue(PropertyValue);

        HeaderRow.NameContent()
            [
                PropertyHandle->CreatePropertyNameWidget()
            ];
        HeaderRow.ValueContent()
            [
                SNew(SVerticalBox)
                + SVerticalBox::Slot()
            .VAlign(VAlign_Center)
            [
                SNew(SColorPicker)
                .OnColorCommitted(this, &FMyCustomAssetPropertyDetails::ColorPicked)
            ]
            ];
    }

};
```

이후 temp_editor 클래스의 StartUpModule, ShutdownModule 에 추가합니다.

```cpp
//StartupModule

FPropertyEditorModule& PropertyModule = FModuleManager::LoadModuleChecked<FPropertyEditorModule>("PropertyEditor");
PropertyModule.RegisterCustomClassLayout(UNewAsset::StaticClass()->GetFName(), FOnGetDetailCustomizationInstance::CreateStatic(&FMyCustomAssetDetailsCustomization::MakeInstance));
PropertyModule.RegisterCustomPropertyTypeLayout(UNewAsset::StaticClass()->GetFName(), FOnGetPropertyTypeCustomizationInstance::CreateStatic(&FMyCustomAssetPropertyDetails::MakeInstance));

//ShutdownModule

FPropertyEditorModule& PropertyModule = FModuleManager::LoadModuleChecked<FPropertyEditorModule>("PropertyEditor");
PropertyModule.UnregisterCustomClassLayout(UNewAsset::StaticClass()->GetFName());
```

이후 UNewAsset 을 블루프린트화 한 뒤 디테일 패널을 확인해봅니다.

![detail](/assets/images/ue4/editor_detail.png)
