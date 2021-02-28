# VisualStudio for MacでXamarinにPrismを導入する

#### はじめに

Xamarinを使う場合、Prismを使っての開発が何かと楽です。
が、Visual Studio for Mac には「Prism Template Pack」が提供されておらず、気軽にPrismを使うことができません。
そこで、自力でPrismを導入する手順をまとめてみました。

#### 参考サイト

・[Prism Template Packを使わないでXamarin.FormsソリューションにPrismを適用する](https://qiita.com/ats-y/items/f76098612786b3e9cf4a)

#### 前提

・Visual Studio for Mac 8.8.10(build 2)
・空白フォームのアプリ(Xamrin.Forms) でプロジェクト作成済み
※プロジェクト名は「XF5Psm8Introduction」としています。

#### 1. Xamarin.Formsのアップデート

全プロジェクトのXamarin.Formsを最新にアップデートします。
ついでに、Xamarin.Essentialsも。
・Xamarin.Forms：5.0.0.2012
・Xamarin.Essentials：1.6.1
( 2021/2/23 現在 )

#### 2. Androidのターゲットフレームワークを変更

ソリューションをビルドするとAndroidプロジェクトで次のエラーが発生しました。
AndroidのTargetFrameworkVersionを変更すると解消します。

Androidプロジェクトのオプションを開き以下の項目を変更します。
・ビルド->全般 のターゲットフレームワークを「Android 10.0(Q)」に変更
・ビルド->Androidアプリケーションの対象のAndroidAndroidバージョンを「Android 10.0(API level 29)」に変更

#### 3. NuGetでPrism.Unity.Formsのインストール

全プロジェクトにPrism.Unity.Formsをインストールします。
・Prism.Unity.Forms：8.0.0.1909
( 2021/2/23 現在 )

#### 4. App.xamlとApp.xaml.csを変更

App.xamlとApp.xaml.csを次のように修正します。
```cs
<prism:PrismApplication
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:prism="clr-namespace:Prism.Unity;assembly=Prism.Unity.Forms"
    x:Class="XF5Psm8Introduction.App">
    <Application.Resources>

    </Application.Resources>
</prism:PrismApplication>
```

```cs
using Prism;
using Prism.Ioc;

namespace XF5Psm8Introduction
{
    public partial class App
    {
        public App()
        {
            InitializeComponent();
        }

        public App(IPlatformInitializer initializer)
            : base(initializer)
        {
        }

        protected override void OnInitialized()
        {
            MainPage = new MainPage();
        }

        protected override void RegisterTypes(IContainerRegistry containerRegistry)
        {
        }
    }
}
```

#### 5. ViewとViewModelの作成

XF5Psm8IntroductionプロジェクトのMainPageを削除して、Views, ViewModelsフォルダを作成します。
Viewsフォルダに、MainPage.xaml(ContentPage)を追加します。
ViewModelsフォルダに、MainPageViewModel.csを追加します。

#### 6. NavigationServiceを使ってMainPageを表示

App.xaml.csを次のように修正します。
```cs
using Prism;
using Prism.Ioc;
using Xamarin.Forms;
using XF5Prism8Template.ViewModels;
using XF5Prism8Template.Views;

namespace XF5Prism8Template
{
    public partial class App
    {
        public App()
        {
            InitializeComponent();
        }

        public App(IPlatformInitializer initializer)
            : base(initializer)
        {
        }

        protected override async void OnInitialized()
        {
            // NavigationServiceを使ってMainPageを表示
            //MainPage = new MainPage();
            await NavigationService.NavigateAsync("NavigationPage/MainPage");
        }

        protected override void RegisterTypes(IContainerRegistry containerRegistry)
        {
            // NavigationPage, MainPage, MainPageViewModelをコンテナに追加
            containerRegistry.RegisterForNavigation<NavigationPage>();
            containerRegistry.RegisterForNavigation<MainPage, MainPageViewModel>();
        }
    }
}
```

#### 7. シュミレータで起動確認

ここまでくればPrismの導入は完了です。
最後にシュミレータでの起動確認をして終わりにしたいと思います。
