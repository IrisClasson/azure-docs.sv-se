---
title: Använda objekt lagring (BLOB) från Xamarin | Microsoft Docs
description: Med Azure Storage klient biblioteket för Xamarin kan utvecklare skapa iOS-, Android-och Windows Store-appar med sina interna användar gränssnitt. Den här självstudien visar hur du använder Xamarin för att skapa ett program som använder Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726345"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Använda Blob Storage från Xamarin

Xamarin gör det möjligt för utvecklare att C# använda en delad kodbas för att skapa iOS-, Android-och Windows Store-appar med sina inbyggda användar gränssnitt. Den här självstudien visar hur du använder Azure Blob Storage med ett Xamarin-program. Om du vill veta mer om Azure Storage, innan du simhopp till koden, se [Introduktion till Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Skapa ett nytt Xamarin-program

I den här självstudien kommer vi att skapa en app som är riktad mot Android, iOS och Windows. Den här appen skapar helt enkelt en behållare och laddar upp en BLOB i den här behållaren. Vi använder Visual Studio i Windows, men samma information kan användas när du skapar en app med Xamarin Studio på macOS.

Följ de här stegen för att skapa ditt program:

1. Om du inte redan har gjort det kan du hämta och installera [Xamarin för Visual Studio](https://www.xamarin.com/download).
2. Öppna Visual Studio och skapa en tom app (inbyggd portabel): **File > New > Project > plattforms oberoende > tom app (inbyggd)** .
3. Högerklicka på din lösning i fönstret Solution Explorer och välj **Hantera NuGet-paket för lösningen**. Sök efter **windowsazure. Storage** och installera den senaste stabila versionen på alla projekt i lösningen.
4. Skapa och kör ditt projekt.

Du bör nu ha ett program som gör att du kan klicka på en knapp som ökar en räknare.

## <a name="create-container-and-upload-blob"></a>Skapa behållare och ladda upp BLOB

Därefter lägger du till `(Portable)` en kod i `MyClass.cs`under ditt projekt. Den här koden skapar en behållare och överför en blob till den här behållaren. `MyClass.cs`bör se ut så här:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Se till att ersätta "your_account_name_here" och "your_account_key_here" med det faktiska konto namnet och konto nyckeln.

Dina iOS-, Android-och Windows Phone-projekt har referenser till ditt bärbara projekt, vilket innebär att du kan skriva all delad kod på en plats och använda den i alla dina projekt. Nu kan du lägga till följande kodrad till varje projekt för att börja dra nytta av:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Köra programmet

Du kan nu köra det här programmet i en Android-eller Windows Phone-emulator. Du kan också köra det här programmet i en iOS-emulator, men det kräver en Mac. Om du vill ha mer information om hur du gör detta läser du dokumentationen för att [ansluta Visual Studio till en Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

När du har kört appen skapas behållaren `mycontainer` i ditt lagrings konto. Den ska innehålla blobben `myblob`, som innehåller texten,. `Hello, world!` Du kan kontrol lera detta med hjälp av [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar ett plattforms oberoende program i Xamarin som använder Azure Storage, särskilt fokuserar på ett scenario i Blob Storage. Du kan dock göra mycket mer med inte bara Blob Storage, utan även med tabell, fil och Queue Storage. Mer information finns i följande artiklar:

* [Komma igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduktion till Azure Files](../files/storage-files-introduction.md)
* [Utveckla för Azure Files med .NET](../files/storage-dotnet-how-to-use-files.md)
* [Komma igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure Queue Storage med hjälp av .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
