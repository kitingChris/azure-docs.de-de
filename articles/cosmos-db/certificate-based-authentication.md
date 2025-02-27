---
title: Zertifikatbasierte Authentifizierung in Azure Active Directory mit Azure Cosmos DB
description: Erfahren Sie, wie Sie eine Azure AD-Identität für die zertifikatbasierte Authentifizierung für den Zugriff auf Schlüssel aus Azure Cosmos DB konfigurieren.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: eb8c98df0f015244adf06a9b57f2223509f1f081
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082115"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-account"></a>Zertifikatbasierte Authentifizierung für eine Azure AD-Identität für den Zugriff auf Schlüssel aus einem Azure-Cosmos-Konto

Mithilfe der zertifikatbasierten Authentifizierung kann Ihre Clientanwendung mit Azure Active Directory (Azure AD) und einem Clientzertifikat authentifiziert werden. Sie können eine zertifikatbasierte Authentifizierung auf einem Computer durchführen, auf dem Sie eine Identität benötigen, wie beispielsweise einem lokalen Computer oder einem virtuellen Computer in Azure. Ihre Anwendung kann dann Azure Cosmo DB-Schlüssel lesen, ohne dass sich die Schlüssel direkt in der Anwendung befinden. Dieser Artikel beschreibt, wie Sie eine beispielhafte Azure AD-Anwendung erstellen, sie für die zertifikatbasierte Authentifizierung konfigurieren, sich mit der neuen Anwendungsidentität bei Azure anmelden und dann die Schlüssel von Ihrem Azure Cosmos-Konto abrufen. In diesem Artikel werden die Identitäten mit Azure PowerShell eingerichtet und eine C#-Beispiel-App bereitgestellt, die Schlüssel aus Ihrem Azure Cosmos-Konto authentifiziert und darauf zugreift.  

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie die [neueste Version](/powershell/azure/install-az-ps) von Azure PowerShell.

* Wenn Sie kein [Azure-Abonnement](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

## <a name="register-an-app-in-azure-ad"></a>Registrieren einer App in Azure AD

In diesem Schritt registrieren Sie eine beispielhafte Webanwendung in Ihrem Azure AD-Konto. Diese Anwendung wird später verwendet, um die Schlüssel aus deinem Azure Cosmos-Konto zu lesen. Führen Sie zum Registrieren der Anwendung die folgenden Schritte aus: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie den Bereich für das Azure **Active Directory**, wechseln Sie zum Bereich „App-Registrierungen“, und wählen Sie **Neue Registrierung** aus. 

   ![Registrierung einer neuen Anwendung in Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Geben Sie in das Formular **Anwendung registrieren** die folgenden Details ein:  

   * **Namen** – Geben Sie einen Namen für Ihre Anwendung ein. Dies kann ein beliebiger Name sein, wie z.B. „sampleApp“.
   * **Unterstützte Kontotypen** – Wählen Sie **Nur Konten nur in diesem Organisationsverzeichnis (Standardverzeichnis)** aus, um Ressourcen in Ihrem aktuellen Verzeichnis den Zugriff auf diese Anwendung zu ermöglichen. 
   * **Umleitungs-URL** – Wählen Sie eine Anwendung vom Typ **Web**, und geben Sie eine URL an, unter der Ihre Anwendung gehostet wird. Es kann eine beliebige URL sein. Für dieses Beispiel können Sie eine Test-URL wie `https://sampleApp.com` angeben. Dies ist kein Problem, auch wenn die App nicht existiert.

   ![Registrieren einer beispielhaften Webanwendung](./media/certificate-based-authentication/register-sample-web-app.png)

1. Wählen Sie nach dem Ausfüllen des Formulars **Registrieren** aus.

1. Nachdem die App registriert ist, notieren Sie sich die **Anwendungs-ID (Client)** und **Objekt-ID**. Diese Informationen werden in den nächsten Schritten verwendet. 

   ![Abrufen der Anwendungs- und Objekt-IDs](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Installieren des Azure AD-Moduls

In diesem Schritt installieren Sie das Azure AD PowerShell-Modul. Dieses Modul ist erforderlich, um die ID der Anwendung abzurufen, die Sie im vorherigen Schritt registriert haben, und dieser Anwendung ein selbstsigniertes Zertifikat zuzuordnen. 

1. Öffnen Sie Windows PowerShell ISE mit Administratorrechten. Installieren Sie das AZ PowerShell-Modul, und stellen Sie eine Verbindung mit Ihrem Abonnement her (falls noch nicht geschehen). Wenn Sie mehrere Abonnements haben, können Sie den Kontext des aktuellen Abonnements wie in den folgenden Befehlen gezeigt festlegen:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installieren und Importieren des [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)-Moduls

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Anmelden bei Ihrem Azure AD-Konto

Melden Sie sich bei Ihrem Azure AD an, bei dem Sie die Anwendung registriert haben. Verwenden Sie den Befehl „Connect-AzureAD“, um sich bei Ihrem Konto anzumelden, und geben Sie Ihre Azure-Anmeldeinformationen im Popupfenster ein. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Öffnen Sie eine weitere Windows PowerShell ISE-Instanz, und führen Sie die folgenden Befehle aus, um ein selbstsigniertes Zertifikat zu erstellen und den dem Zertifikat zugeordneten Schlüssel zu lesen:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Erstellen von zertifikatbasierten Anmeldeinformationen 

Führen Sie als nächstes die folgenden Befehle aus, um die Objekt-ID Ihrer Anwendung abzurufen und die zertifikatbasierten Anmeldeinformationen zu erstellen. In diesem Beispiel legen wir für das Zertifikat fest, dass es nach einem Jahr abläuft. Sie können jedoch ein beliebiges Enddatum festlegen.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Der obige Befehl führt zu einer Ausgabe ähnlich dem Screenshot unten:

![Ausgabe der Erstellung von zertifikatbasierten Anmeldeinformationen](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurieren Ihres Azure Cosmos-Kontos für die Verwendung der neuen Identität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie das Blatt **Zugriffssteuerung (IAM)** .

1. Wählen Sie **Auswählen** und dann **Rollenzuweisung hinzufügen** aus. Fügen Sie die Beispiel-App, die Sie im vorherigen Schritt erstellt haben, mit der Rolle **Mitwirkender** hinzu, wie im folgenden Screenshot gezeigt:

   ![Konfigurieren des Azure Cosmos-Kontos für die Verwendung der neuen Identität](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Wählen Sie nach dem Ausfüllen des Formulars **Speichern** aus.


## <a name="access-the-keys-from-powershell"></a>Zugriff auf Schlüssel über PowerShell

In diesem Schritt melden Sie sich bei Azure an, indem Sie die Anwendung und das von Ihnen erstellte Zertifikat verwenden und auf die Schlüssel Ihres Azure Cosmos-Kontos zugreifen. 

1. Löschen Sie zunächst die Anmeldeinformationen des Azure-Kontos, mit denen Sie sich bei Ihrem Konto angemeldet haben. Mit dem folgenden Befehl können Sie Anmeldeinformationen löschen:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Überprüfen Sie anschließend, ob Sie sich bei Azure-Portal anmelden können, indem Sie die Anmeldeinformationen der Anwendung verwenden und auf die Azure Cosmos DB-Schlüssel zugreifen:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Mit dem vorherigen Befehl werden der primäre und sekundäre Hauptschlüssel Ihres Azure Cosmos-Kontos angezeigt. Sie können das Aktivitätsprotokoll Ihres Azure Cosmos-Kontos einsehen, um zu überprüfen, ob die Anfrage zum Abrufen von Schlüsseln erfolgreich war und das Ereignis von der Anwendung „sampleApp“ ausgelöst wird. 
 
![Überprüfen des Aufrufs zum Abrufen der Schlüssel in Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Zugreifen auf die Schlüssel aus einer C#-Anwendung 

Sie können dieses Szenario auch überprüfen, indem Sie auf Schlüssel aus einer C#-Anwendung zugreifen. Die folgende C# Konsolenanwendung kann anhand der in Active Directory registrierten App auf die Azure Cosmos DB-Schlüssel zugreifen. Stellen Sie sicher, dass Sie die Angaben zu „tenantId“, „clientID“, „certName“, „Ressourcengruppenname“, „Abonnement-ID“ und „Azure Cosmos-Kontonamen“W aktualisieren, bevor Sie den Code ausführen. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Dieses Skript gibt den primären und sekundären Hauptschlüssel aus, wie im folgenden Screenshot gezeigt:

![csharp-Anwendungsausgabe](./media/certificate-based-authentication/csharp-application-output.png)

Wie im vorherigen Abschnitt können Sie das Aktivitätsprotokoll Ihres Azure Cosmos-Kontos einsehen, um zu überprüfen, ob die Anfrage zum Abrufen von Schlüsseln von der Anwendung „sampleApp“ ausgelöst wird. 


## <a name="next-steps"></a>Nächste Schritte

* [Sichern von Azure Cosmos-Schlüsseln mit Azure Key Vault](access-secrets-from-keyvault.md)

* [Sicherheitsattribute für Azure Cosmos DB](cosmos-db-security-attributes.md)