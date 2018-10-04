---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460084"
---
Nel modulo precedente si è visto come una funzione senza server possa facilitare il caricamento sicuro di immagini nell'archivio BLOB da un'applicazione Web. In questo modulo si creerà un'altra funzione senza server che controllerà il caricamento di immagini e creerà le relative anteprime.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Creare un contenitore di archiviazione BLOB per le anteprime

Le immagini con le dimensioni originali vengono archiviate in un contenitore denominato **images**. È necessario un altro contenitore per memorizzare le anteprime delle immagini.

1. Verificare di essere ancora connessi a Cloud Shell (bash).  In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell. 

1. Creare un nuovo contenitore denominato **thumbnails** nel proprio account di archiviazione con accesso pubblico a tutti i BLOB.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Creare una funzione senza server attivata dal BLOB

Un trigger definisce come viene richiamata una funzione. La funzione che si creerà ora usa un trigger BLOB: la funzione viene richiamata automaticamente quando un BLOB (file immagine) viene caricato nel contenitore **images**. Una funzione deve avere un trigger. I trigger hanno dei dati associati, ovvero in genere il payload che ha attivato la funzione.

Le associazioni definiscono il modo in cui una funzione legge o scrive i dati in Azure o in servizi di terze parti. Questa funzione crea una versione di anteprima dell'immagine che la attiva e salva l'anteprima in un contenitore denominato *thumbnails*.

1. Aprire l'app per le funzioni nel portale di Azure.

1. Nel riquadro di spostamento di sinistra della finestra dell'app per le funzioni passare il mouse su **Funzioni** e fare clic su **+** per iniziare a creare una nuova funzione senza server. Se viene visualizzata una pagina della guida introduttiva, fare clic su **Funzione personalizzata** per visualizzare un elenco di modelli di funzione.

1. Individuare il modello **BlobTrigger** e selezionarlo.

1. Usare questi valori per creare una funzione che crea anteprime quando vengono caricate immagini.

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | --- | --- | ---|
    | **Lingua** | C# o JavaScript | Scegliere il linguaggio preferito |
    | **Dare un nome alla funzione** | ResizeImage | Digitare questo nome esattamente come mostrato in modo che l'applicazione possa individuare la funzione. |
    | **Percorso** | images/{name} | Esegue la funzione quando appare un file nel contenitore **images**. |
    | **Informazioni account di archiviazione** | AZURE_STORAGE_CONNECTION_STRING | Usare la variabile di ambiente creata in precedenza con la stringa di connessione. |

    ![Immettere le impostazioni per la nuova funzione](media/functions-first-serverless-web-app/3-new-function.png)

1. Fare clic su **Crea** per creare la funzione.

1. Quando la funzione è creata, fare clic su **Integrazione** per visualizzare le associazioni di attivazione, input e output.

1. Fare clic su **Nuovo output** per creare una nuova associazione di output.

    ![Selezionare Nuovo output nella scheda Integrazione](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Selezionare **Archiviazione BLOB di Azure** e fare clic su **Seleziona**. Può essere necessario scorrere verso il basso per vedere il pulsante **Seleziona**.

    ![Selezionare Archiviazione BLOB di Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Immettere i valori seguenti.

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | --- | --- | ---|
    | **Nome del parametro del BLOB** | thumbnail | La funzione usa il parametro con questo nome per scrivere l'anteprima. |
    | **Usa valore restituito della funzione** | No  |  |
    | **Percorso** | thumbnails/{name} | Le anteprime vengono scritti in un contenitore denominato **thumbnails**. |
    | **Connessione dell'account di archiviazione** | AZURE_STORAGE_CONNECTION_STRING | Usare la variabile di ambiente creata in precedenza con la stringa di connessione. |

    ![Immettere le impostazioni per l'associazione di output del BLOB](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Fare clic su **Editor avanzato** nell'angolo superiore destro della finestra per visualizzare il codice JSON che rappresenta le associazioni.

    1. (JavaScript) Nell'associazione `blobTrigger` aggiungere una proprietà denominata `dataType` con valore `binary`. In questo modo si configura l'associazione per il passaggio dei contenuti del BLOB alla funzione come dati binari.

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. Fare clic su **Salva** per creare la nuova associazione.

1. **C#**

    1. (C#) Selezionare il nome della funzione **ResizeImage** nel riquadro di spostamento di sinistra per aprire il codice sorgente della funzione.

    1. (C#) La funzione richiede un pacchetto NuGet chiamato **ImageResizer** per generare le anteprime. I pacchetti NuGet vengono aggiunti alle funzioni C# tramite un file **project.json**. Per creare il file, fare clic su **Visualizza file** a destra per visualizzare i file che costituiscono la funzione.
    
    1. (C#) Fare clic su **Aggiungi** per aggiungere un nuovo file denominato **project.json**.
    
    1. (C#) Copiare il contenuto di [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) nel file appena creato. Salvare il file. I pacchetti vengono ripristinati automaticamente quando il file viene aggiornato.
    
        ![file project.json con ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Selezionare **run.csx** in **Visualizza file** e sostituirne il contenuto con quello presente in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Questa funzione richiede il pacchetto `jimp` da npm per ridimensionare la foto. Per installare il pacchetto npm, fare clic sul nome dell'app per le funzioni nel riquadro di spostamento di sinistra e fare clic su **Funzionalità della piattaforma**.

    1. (JavaScript) Fare clic su **Console** per visualizzare una finestra della console.

    1. (JavaScript) Eseguire il comando `npm install jimp` nella console. Il completamento dell'operazione potrebbe richiedere un minuto o due.

    1. (JavaScript) Fare clic sul nome della funzione **ResizeImage** nel riquadro di spostamento di sinistra per visualizzare la funzione, quindi sostituire tutto il contenuto del file **index.js** con il contenuto di [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.

1. Fare clic su **Save**. Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.


## <a name="test-the-serverless-function"></a>Testare la funzione senza server

1. Aprire l'applicazione in un browser. Selezionare un file di immagine e caricarlo. Il file viene caricato ma, poiché non è stata ancora aggiunta la possibilità di visualizzare le immagini, l'app non mostra la foto caricata.

1. In Cloud Shell verificare che l'immagine sia stata caricata nel contenitore **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Verificare che sia stata creata l'anteprima in un contenitore denominato **thumbnails**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Ottenere l'URL dell'anteprima.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Aprire l'URL in un browser e verificare che l'anteprima sia stata creata correttamente.

1. Prima di passare all'esercitazione successiva, eliminare tutti i file nei contenitori **images** e **thumbnails**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Summary

In questo modulo è stata creata una funzione senza server per creare un'anteprima ogni volta che viene caricata un'immagine in un contenitore di archiviazione BLOB. Si vedrà ora come usare Azure Cosmos DB per memorizzare ed elencare i metadati delle immagini.
