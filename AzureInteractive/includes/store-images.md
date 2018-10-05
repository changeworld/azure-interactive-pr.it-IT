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
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460059"
---
Azure Cosmos DB è il database di Microsoft multimodello, senza server, distribuito a livello globale. In questo modulo si vedrà come usare Funzioni di Azure per memorizzare e recuperare i metadati delle immagini come documenti JSON in Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Creare un account, un database e una raccolta Cosmos DB

Un account Cosmos DB è una risorsa di Azure che contiene database Cosmos DB.

1. Verificare di essere ancora connessi a Cloud Shell.  In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell. 

1. Creare un account Cosmos DB con un nome univoco nello stesso gruppo di risorse delle altre risorse di questa esercitazione.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Dopo avere creato l'account Cosmos DB, creare un nuovo database denominato **imagesdb** nell'account.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Dopo la creazione del database, creare una nuova raccolta denominata **images** nel database con una velocità effettiva di 400 unità richiesta (UR).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Salvare un documento in Cosmos DB quando viene creata un'anteprima

L'associazione di output di Cosmos DB consente di creare documenti in una raccolta Cosmos DB da Funzioni di Azure. Nei passaggi che seguono si configura un'associazione di output di Cosmos DB nella funzione **ResizeImage** e si modifica la funzione in modo che restituisca un documento (oggetto) da salvare.

1. Aprire l'app per le funzioni nel portale di Azure.

1. Nel riquadro di spostamento di sinistra espandere la funzione **ResizeImage** e quindi selezionare **Integrazione**.

1. Sotto **Output** fare clic su **Nuovo output**.

1. Individuare l'elemento **Azure Cosmos DB** e selezionarlo. Quindi fare clic su **Seleziona**.

    ![Selezionare Nuovo output](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Compilare i campi sotto **Azure Cosmos DB output** (Output Azure Cosmos DB) con i valori seguenti.

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | --- | --- | ---|
    | **Nome del parametro del documento** | Selezionare **Usa valore restituito della funzione** | Il valore della casella di testo viene impostato automaticamente su **$return**. |
    | **Nome database** | imagesdb | Usare il nome del database creato in precedenza. |
    | **Nome raccolta** | images | Usare il nome della raccolta creata in precedenza. |

1. Accanto a **Connessione all'account Azure Cosmos DB** fare clic su **nuova**. Selezionare l'account Cosmos DB creato in precedenza.

    ![Immettere le impostazioni per l'associazione di output di Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Fare clic su **Salva** per creare l'associazione di output di Cosmos DB.

1. Fare clic sul nome della funzione **ResizeImage** a sinistra per aprire la funzione.

1. **C#**

    1. (C#) Cambiare il tipo restituito dalla funzione da **void** a **object**.

    1. (C#) Alla fine della funzione, aggiungere il blocco di codice seguente per restituire il documento da salvare:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx per la funzione ResizeImages dopo le modifiche](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Modificare l'istruzione `context.done()` nella clausola `else` per restituire il documento da salvare a Cosmos DB.

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.

1. Fare clic su **Save**. Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Creare una funzione per elencare le immagini da Cosmos DB

L'applicazione Web richiede un'API per recuperare i metadati delle immagini da Cosmos DB. Nei passaggi che seguono si creerà una funzione attivata tramite HTTP che usa un'associazione di input di Cosmos DB per eseguire una query sulla raccolta di database.

1. Nell'app per le funzioni passare il puntatore del mouse su **Funzioni** a sinistra e fare clic su **+** per creare una nuova funzione.

1. Individuare il modello **HttpTrigger** e selezionarlo.

1. Usare questi valori per creare una funzione che genera un URL per ottenere le immagini.

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | --- | --- | ---|
    | **Dare un nome alla funzione** | GetImages | Digitare questo nome esattamente come mostrato in modo che l'applicazione possa individuare la funzione. |
    | **Livello di autorizzazione** | Anonima | Consente l'accesso pubblico alla funzione. |

1. Fare clic su **Create**(Crea).

1. Dopo la creazione della nuova funzione, fare clic su **Integrazione** sotto il nome della funzione nel riquadro di spostamento di sinistra.

1. Fare clic su **Nuovo input** e selezionare **Azure Cosmos DB**. 

    ![Selezionare Nuovo input](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Fare clic su **Seleziona**.

1. Inserire i valori seguenti:

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | --- | --- | ---|
    | **Nome del parametro del documento** | documents | Corrisponde al nome del parametro nella funzione. |
    | **Nome database** | imagesdb |  |
    | **Nome raccolta** | images |  |
    | **SQL query** | select * from c order by c._ts desc | Ottiene i documenti, a cominciare dal più recente. |
    | **Connessione all'account Azure Cosmos DB** | Selezionare la stringa di connessione esistente |  |

1. Fare clic su **Salva** per creare l'associazione di input.

1. **C#**

    1. Fare clic sul nome della funzione per aprire la finestra del codice, quindi sostituire tutto il contenuto di **run.csx** con il contenuto di [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Fare clic sul nome della funzione per aprire la finestra del codice, quindi sostituire tutto il contenuto di **index.js** con il contenuto di [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.

1. Fare clic su **Save**. Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.


## <a name="test-the-application"></a>Test dell'applicazione

1. Aprire l'applicazione in un browser. Selezionare un file di immagine e caricarlo.

1. Dopo alcuni secondi, nella pagina verrà visualizzata l'anteprima della nuova immagine.

1. Nel portale di Azure usare la casella di ricerca per cercare il proprio account Cosmos DB in base al nome. Fare clic sull'account per aprirlo.

1. Fare clic su **Esplora dati** a sinistra per sfogliare le raccolte e i documenti.

1. Sotto il database **imagesdb** selezionare la raccolta **images**.

1. Verificare che sia stato creato un documento per l'immagine caricata.

    ![Esplora dati con un documento per un'immagine caricata](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Summary

In questo modulo è stato descritto come creare un account, un database e una raccolta di Cosmos DB. Si è inoltre visto come usare le associazioni di Cosmos DB per salvare e recuperare i metadati delle immagini nella raccolta di Cosmos DB. Si vedrà ora come generare automaticamente una didascalia per ogni immagine caricata usando Servizi cognitivi Microsoft.
