---
title: 'Schnellstart: Benutzerdefinierter virtueller Voice-First-Assistent (Vorschauversion), C# (UWP): Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erstellen Sie eine C#-UWP-Anwendung (Universelle Windows-Plattform) mithilfe des Cognitive Services Speech Software Development Kit (SDK)q. Sie stellen eine Verbindung Ihrer Clientanwendung mit einem zuvor erstellten Bot Framework-Bot her, der für die Verwendung des Direct Line Speech-Kanals konfiguriert ist. Die Anwendung basiert auf dem NuGet-Paket für das Speech SDK und auf Microsoft Visual Studio 2017.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 22c18b573e7107163f858c79956ca6f5380f6834
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604975"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Schnellstart: Erstellen eines virtuellen Voice-First-Assistenten mit dem Speech SDK, UWP

Schnellstarts sind auch für [Spracherkennung](quickstart-csharp-uwp.md), [Sprachsynthese](quickstart-text-to-speech-csharp-uwp.md) und [Sprachübersetzung](quickstart-translate-speech-uwp.md) verfügbar.

In diesem Artikel entwickeln Sie eine C#-UWP-Anwendung (Universelle Windows-Plattform) mithilfe des [Speech SDK](speech-sdk.md). Das Programm stellt eine Verbindung mit einem zuvor erstellten und konfigurierten Bot her, um die Nutzung eines virtuellen Voice-First-Assistenten von der Clientanwendung aus zu ermöglichen. Die Anwendung basiert auf dem [NuGet-Paket für das Speech SDK](https://aka.ms/csspeech/nuget) und auf Microsoft Visual Studio 2017 (beliebige Edition).

> [!NOTE]
> Mithilfe der universellen Windows-Plattform können Sie Apps entwickeln, die auf einem beliebigen Gerät mit Unterstützung für Windows 10 (z.B. PCs, Xbox, Surface Hub und andere Geräte) ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Azure-Abonnementschlüssel für Spracherkennungsdienste. [Beziehen Sie einen kostenlos](get-started.md), oder erstellen Sie ihn im [Azure-Portal](https://portal.azure.com).
* Ein zuvor erstellter, mit dem [Direct Line Speech-Kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) konfigurierter Bot

    > [!NOTE]
    > Direct Line Speech (Vorschauversion) ist derzeit in einer Teilmenge der Regionen verfügbar, die Spracherkennungsdienste unterstützen. Beachten Sie [die Liste der unterstützten Regionen für virtuelle Voice-First-Assistenten](regions.md#voice-first-virtual-assistants), und stellen Sie sicher, dass Ihre Ressourcen in einer dieser Regionen bereitgestellt werden.

## <a name="optional-get-started-fast"></a>Optional: Schneller Einstieg

In dieser Schnellstartanleitung erfahren Sie Schritt für Schritt, wie Sie eine einfache Clientanwendung erstellen, die Sie mit Ihrem sprachaktivierten Bot verbinden können. Wenn Sie lieber sofort loslegen möchten, finden Sie den kompilierbereiten Quellcode, der in dieser Schnellstartanleitung verwendet wird, in den [Speech SDK-Beispielen](https://aka.ms/csspeech/samples) im Ordner `quickstart`.

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Hinzufügen von Beispielcode

1. Die Benutzeroberfläche der Anwendung wird mithilfe von XAML definiert. Öffnen Sie `MainPage.xaml` im Projektmappen-Explorer. Ersetzen Sie in der XAML-Ansicht des Designers den gesamten Inhalt mit dem Folgenden.

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. Öffnen Sie die CodeBehind-Quelldatei `MainPage.xaml.cs`. Dieses ist unter `MainPage.xaml` gruppiert. Ersetzen Sie den Inhalt durch den folgenden Code: Dieses Beispiel umfasst:

    * Verwendung von Anweisungen für die Namespaces „Speech“ und „Speech.Dialog“
    * Eine einfache Implementierung zur Sicherstellung des Mikrofonzugriffs über eine verkabelte Tastenbedienung
    * Grundlegende UI-Hilfsprogramme zum Anzeigen von Meldungen und Fehlern in der Anwendung
    * Ein Landepunkt für den Pfad des Initialisierungscodes, der später aufgefüllt wird
    * Ein Hilfsprogramm für die Wiedergabe von Sprachsynthese (ohne Streamingunterstützung)
    * Ein leerer Schaltflächenhandler, um mit dem Lauschen zu beginnen, der später aufgefüllt wird

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Als Nächstes erstellen Sie den `DialogServiceConnector` mit den Informationen zu Ihrem Abonnement. Fügen Sie Folgendes dem Methodentext von `InitializeDialogServiceConnector` hinzu, wobei Sie die Zeichenfolgen `YourChannelSecret`, `YourSpeechSubscriptionKey` und `YourServiceRegion` durch Ihre eigenen Werte für Bot, Speech-Abonnement und [Region](regions.md) ersetzen.

    > [!NOTE]
    > Direct Line Speech (Vorschauversion) ist derzeit in einer Teilmenge der Regionen verfügbar, die Spracherkennungsdienste unterstützen. Beachten Sie [die Liste der unterstützten Regionen für virtuelle Voice-First-Assistenten](regions.md#voice-first-virtual-assistants), und stellen Sie sicher, dass Ihre Ressourcen in einer dieser Regionen bereitgestellt werden.

    > [!NOTE]
    > Informationen zur Konfiguration Ihres Bots und zum Abrufen eines Kanalgeheimnisses finden Sie in der Bot-Framework-Dokumentation zum [Direct Line Speech-Kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region. Note: only a subset of regions are currently supported

    var botConfig = DialogServiceConfig.FromBotSecret(channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

1. `DialogServiceConnector` nutzt mehrere Ereignisse, um Botaktivitäten, Spracherkennungsergebnisse und andere Informationen zu kommunizieren. Fügen Sie Handler für diese Ereignisse hinzu, und fügen Sie das Folgende am Ende des Methodentexts von `InitializeDialogServiceConnector` an.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };
    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Wenn die Konfiguration eingerichtet ist und die Ereignishandler registriert sind, muss der `DialogServiceConnector` jetzt lediglich noch lauschen. Fügen Sie dem Text der `ListenButton_ButtonClicked`-Methode in der `MainPage`-Klasse das Folgende hinzu.

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (connector == null)
        {
            InitializeDialogServiceConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = connector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = connector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await connector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. Speichern Sie alle Änderungen am Projekt.

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

1. Erstellen Sie die Anwendung. Wählen Sie in der Menüleiste von Visual Studio **Build** > **Projektmappe erstellen** aus. Der Code sollte nun ohne Fehler kompiliert werden.

    ![Screenshot der Visual Studio-Anwendung mit hervorgehobener Option „Projektmappe erstellen](media/sdk/qs-csharp-uwp-08-build.png "Erfolgreicher Build“")

1. Starten Sie die Anwendung. Wählen Sie in der Menüleiste von Visual Studio **Debuggen** > **Debuggen starten** aus, oder drücken Sie **F5**.

    ![Screenshot der Visual Studio-Anwendung mit hervorgehobener Option „Debuggen starten](media/sdk/qs-csharp-uwp-09-start-debugging.png "Debuggen der App starten“")

1. Ein Fenster wird angezeigt. Wählen Sie in Ihrer Anwendung **Mikrofon aktivieren** aus, und bestätigen Sie die Berechtigungsanforderung, die angezeigt wird.

    ![Screenshot der Berechtigungsanforderung](media/sdk/qs-csharp-uwp-10-access-prompt.png "Debuggen der App starten")

1. Wählen Sie **Mit Ihrem Bot sprechen** aus, und sprechen Sie einen englischen Ausdruck oder Satz in das Mikrofon Ihres Geräts. Ihre Spracheingabe wird an den Direct Line Speech-Kanal übermittelt, in Text transkribiert und im Fenster angezeigt.

    ![Screenshot der erfolgreichen Botaktivierung](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "Eine erfolgreiche Botaktivierung")

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen eines Basisbots](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Weitere Informationen

- [Informationen zu virtuellen Voice-First-Assistenten](voice-first-virtual-assistants.md)
- [Beziehen eines kostenlosen Abonnementschlüssels für die Spracherkennungsdienste](get-started.md)
- [Benutzerdefinierte Aktivierungswörter](speech-devices-sdk-create-kws.md)
- [Verbinden von Direct Line Speech mit Ihrem Bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [C#-Beispiele auf GitHub](https://aka.ms/csspeech/samples)
