---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 10/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3779c2e130afa7ee8d5879f30a924e258b7a41e9
ms.sourcegitcommit: fdb43556b8dcf67cb39c18e532b5fab7ac53eaee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49315977"
---
L'applicazione che si sta creando è una raccolta di foto che usa JavaScript lato client per chiamare API per caricare e visualizzare immagini. In questo modulo si creerà un'API usando una funzione senza server che genera un URL temporaneo per caricare un'immagine. L'applicazione Web usa l'URL generato per caricare un'immagine in un archivio Blob usando l'[API REST dell'archivio BLOB](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Creare un contenitore di archiviazione BLOB per le immagini

L'applicazione richiede un contenitore di archiviazione separato per caricare e ospitare immagini.

1. Verificare di essere ancora connessi a Cloud Shell (bash). In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell.

1.  Creare un nuovo contenitore denominato **images** nel proprio account di archiviazione con accesso pubblico a tutti i BLOB.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

Funzioni di Azure è un servizio per l'esecuzione di funzioni senza server. Una funzione senza server può essere attivata (chiamata) da eventi come una richiesta HTTP o la creazione di un BLOB in un contenitore di archiviazione.

Un'app per le funzioni di Azure è un contenitore per una o più funzioni senza server.

Nel gruppo di risorse creato in precedenza, creare una nuova app per le funzioni di Azure, assegnandole il nome univoco **first-serverless-app**. Le app per le funzioni richiedono un account di archiviazione. In questa esercitazione si userà l'account di archiviazione esistente.

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

L'app per le funzioni in questa esercitazione richiede la versione 1.x del runtime di Funzioni. Se si configura l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione su `~1`, l'app per le funzioni viene associata alla versione 1.x più recente. Configurare l'impostazione dell'applicazione con il comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

Nel comando seguente dell'interfaccia della riga di comando di Azure il valore "<app_name>" corrisponde al nome dell'app per le funzioni.

```azurecli
az functionapp config appsettings set --name <function app name> -g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a>Creare una funzione senza server attivata da HTTP

L'applicazione Web della raccolta foto invia alla funzione senza server una richiesta HTTP di generazione di un URL temporaneo per caricare in modo sicuro un'immagine nell'archivio BLOB. La funzione viene attivata da una richiesta HTTP e usa Azure Storage SDK per generare e restituire l'URL protetto.

1. Dopo che l'app per le funzioni è stata creata, cercarla nel portale di Azure usando la casella di ricerca e fare clic per aprirla.

    ![Aprire l'app per le funzioni](media/functions-first-serverless-web-app/2-search-function-app.png)

1. Nel riquadro di spostamento di sinistra della finestra dell'app per le funzioni passare il mouse su **Funzioni** e fare clic su **+** per iniziare a creare una nuova funzione senza server.

    ![Creare una nuova funzione](media/functions-first-serverless-web-app/2-new-function.png)

1. Fare clic su **Funzione personalizzata** per visualizzare un elenco di modelli di funzione.

1. Individuare il modello **HttpTrigger** e fare clic sul linguaggio da usare (C# o JavaScript).

1. Usare questi valori per creare una funzione che genera un URL di caricamento BLOB.

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | --- | --- | ---|
    | **Lingua** | C# o JavaScript | Selezionare il linguaggio da usare. |
    | **Dare un nome alla funzione** | GetUploadUrl | Digitare questo nome esattamente come mostrato in modo che l'applicazione possa individuare la funzione. |
    | **Livello di autorizzazione** | Anonima | Consente l'accesso pubblico alla funzione. |

    ![Inserire le impostazioni per una nuova funzione attivata da HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Fare clic su **Crea** per creare la funzione.

1. **C#** 

    1. Quando viene visualizzato il codice sorgente della funzione, sostituire l'intero contenuto di **run.csx** con il contenuto di [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Questa funzione richiede il pacchetto `azure-storage` da npm per generare il token di firma di accesso condiviso necessario per creare l'URL protetto. Per installare il pacchetto npm, fare clic sul nome dell'app per le funzioni nel riquadro di spostamento di sinistra e fare clic su **Funzionalità della piattaforma**.

    1. (JavaScript) Fare clic su **Console** per visualizzare una finestra della console.

        ![Aprire una finestra della console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Assicurarsi che la directory corrente sia **d:\home\site\wwwroot** eseguendo il comando `cd d:\home\site\wwwroot`.

    1. (JavaScript) Eseguire il comando `npm init -y` per creare un file **package.json** vuoto.

    1. (JavaScript) Eseguire il comando `npm install --save azure-storage` nella console per installare il pacchetto e salvarlo in **package.json**. Il completamento dell'operazione potrebbe richiedere un minuto o due.

    1. (JavaScript) Fare clic sul nome della funzione (**GetUploadUrl**) nel riquadro di spostamento di sinistra per visualizzare la funzione, quindi sostituire tutto il contenuto del file **index.js** con il contenuto di [**/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![index.js dopo l'aggiornamento](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.

1. Fare clic su **Save**. Verificare nel pannello dei log che la funzione sia stata compilata correttamente.

La funzione genera un cosiddetto URL di firma di accesso condiviso, che consente di caricare un file nell'archivio BLOB. L'URL di firma di accesso condiviso è valido per un breve periodo di tempo e consente il caricamento di un solo file. Consultare la documentazione dell'archivio BLOB per ulteriori informazioni sull'[uso delle firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Aggiungere una variabile di ambiente per la stringa di connessione di archiviazione

La funzione appena creata richiede una stringa di connessione per l'account di archiviazione in modo da poter generare l'URL SAS. Invece di codificare la stringa di connessione come hardcoded nel corpo della funzione, è possibile memorizzarla come impostazione dell'applicazione. Le impostazioni dell'applicazione sono accessibili come variabili di ambiente da tutte le funzioni nell'app per le funzioni.

1. In Cloud Shell, eseguire una query per recuperare la stringa di connessione dell'account di archiviazione e salvarla in una variabile Bash denominata **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Verificare che la variabile sia impostata correttamente.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Creare una nuova impostazione dell'applicazione denominata **AZURE_STORAGE_CONNECTION_STRING** usando il valore salvato nel passaggio precedente.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Verificare che l'output del comando contenga la nuova impostazione dell'applicazione con il valore corretto.


## <a name="test-the-serverless-function"></a>Testare la funzione senza server

Oltre alla creazione e alla modifica delle funzioni, il portale di Azure fornisce uno strumento predefinito anche per il test delle funzioni.

1. Per testare la funzione HTTP senza server, fare clic sulla scheda **Test** a destra della finestra del codice per espandere il pannello di test.

1. Modificare **Metodo HTTP** in **GET**.

1. In **Query** fare clic su *Aggiungi parametro** e aggiungere il parametro seguente:

    | name      |  value   | 
    | --- | --- |
    | filename | image1.jpg |

1. Fare clic su **Esegui** nel pannello di test per inviare una richiesta HTTP alla funzione.

1. La funzione restituisce un URL di caricamento nell'output. L'esecuzione della funzione viene visualizzata nel pannello dei log.

    ![Log che mostrano la corretta esecuzione della funzione](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>Configurare CORS nell'app per le funzioni

Poiché il front-end dell'app è ospitato nell'archivio BLOB, ha un nome di dominio diverso rispetto all'app per le funzioni di Azure. Per consentire al codice JavaScript lato client di chiamare correttamente la funzione appena creata, l'app per le funzioni deve essere configurata per la condivisione di risorse tra le origini (CORS).

1. Nella barra di spostamento di sinistra della finestra dell'app per le funzioni, fare clic sul nome dell'app per le funzioni.

1. Fare clic su **Funzionalità della piattaforma** per visualizzare un elenco di funzionalità avanzate.

1. Sotto **API** fare clic su **CORS**.

    ![Selezionare CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Aggiungere un'origine consentita per l'URL dell'applicazione dal modulo precedente, omettendo il carattere **/** finale (ad esempio: `https://firstserverlessweb.z4.web.core.windows.net`).

    ![Impostazioni CORS con l'aggiunta dell'URL dell'applicazione Web senza server](media/functions-first-serverless-web-app/2-add-cors.png)

1. Fare clic su **Save**.

1. C#

   1. (C#) Tornare alla funzione `GetUploadUrl` e quindi selezionare la scheda **Integrazione**.

   1. (C#) In Metodi HTTP selezionati selezionare **OPTIONS**.

      **GET**, **POST** e **OPTIONS** devono essere tutti selezionati. CORS usa il metodo OPTIONS, che non è selezionato per impostazione predefinita per le funzioni C#.  

   1. (C#) Fare clic su **Salva**.

1. Restando nel portale, passare all'app per le funzioni, selezionare la scheda **Panoramica** e quindi fare clic su **Riavvia** per assicurarsi che le modifiche per CORS abbiano effetto.

## <a name="configure-cors-in-the-storage-account"></a>Configurare CORS nell'account di archiviazione

Poiché l'applicazione effettua anche chiamate JavaScript lato client all'archiviazione BLOB per caricare i file, è necessario inoltre configurare l'account di archiviazione per CORS.

1. Eseguire il comando seguente per consentire a tutte le origini di caricare file nell'account di archiviazione.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>Modificare l'app Web per caricare immagini

L'app Web recupera le impostazioni da un file denominato **settings.js**. Nei passaggi seguenti si creerà il file usando Cloud Shell, quindi si imposterà `window.apiBaseUrl` sull'URL dell'app per le funzioni e `window.blobBaseUrl` sull'URL dell'endpoint di Archiviazione BLOB di Azure.

1. In Cloud Shell assicurarsi che la directory corrente sia la cartella **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Eseguire una query per ottenere l'URL dell'app per le funzioni e memorizzarlo in una variabile Bash denominata **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Verificare che la variabile sia impostata correttamente.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Per impostare l'URI di base delle chiamate API all'app per le funzioni, creare **settings.js** e aggiungere l'URL dell'app per le funzioni, come segue.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    È possibile apportare la modifica eseguendo il comando seguente o usando un editor da riga di comando come VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Verificare che il file sia stato scritto correttamente.

    ```azurecli
    cat settings.js
    ```

1. Eseguire una query per ottenere l'URL di base di archiviazione BLOB e memorizzarlo in una variabile Bash denominata **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Verificare che la variabile sia impostata correttamente.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Per impostare l'URI di base delle chiamate API all'app per le funzioni, aggiungere l'URL di archiviazione come la riga di codice seguente a **settings.js**.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    È possibile apportare la modifica eseguendo il comando seguente o usando un editor da riga di comando come VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Verificare che il file sia stato scritto correttamente e che ora contenga 2 righe.

    ```azurecli
    cat settings.js
    ```

1. Caricare il file nell'archivio BLOB.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Testare l'applicazione Web

A questo punto, l'applicazione della raccolta è in grado di caricare un'immagine nell'archivio BLOB, ma non è ancora in grado di visualizzare le immagini. Tenterà di chiamare una funzione `GetImages` che non esiste ancora perché verrà creata in un modulo successivo. La chiamata avrà esito negativo e la pagina Web apparirà bloccata su "Analisi in corso...", ma l'immagine selezionata verrà caricata correttamente.

È possibile verificare che un'immagine sia stata caricata correttamente controllando il contenuto del contenitore **images** nel portale di Azure.

1. In una finestra del browser, passare all'applicazione. Selezionare un file di immagine e caricarlo. Il file viene caricato ma, poiché non è stata ancora aggiunta la possibilità di visualizzare le immagini, l'app non mostra la foto caricata. La pagina Web apparirà bloccata su "Analyzing image..." (Analisi immagine in corso...). Questo comportamento verrà corretto in seguito.

1. In Cloud Shell verificare che l'immagine sia stata caricata nel contenitore **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Prima di passare all'esercitazione successiva, eliminare tutti i file nel contenitore **images**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Summary

In questo modulo si è creata un'app per le funzioni di Azure e si è visto come usare una funzione senza server per consentire a un'applicazione Web di caricare immagini nell'archivio BLOB. Si vedrà ora come creare anteprime per le immagini caricate usando una funzione senza server attivata dal BLOB.
