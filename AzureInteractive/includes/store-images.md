---
title: File di inclusione
description: File di inclusione
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079465"
---
<span data-ttu-id="359cd-103">Azure Cosmos DB è il database di Microsoft multimodello, senza server, distribuito a livello globale.</span><span class="sxs-lookup"><span data-stu-id="359cd-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="359cd-104">In questo modulo si vedrà come usare Funzioni di Azure per memorizzare e recuperare i metadati delle immagini come documenti JSON in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="359cd-105">Creare un account, un database e una raccolta Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="359cd-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="359cd-106">Un account Cosmos DB è una risorsa di Azure che contiene database Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="359cd-107">Verificare di essere ancora connessi a Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="359cd-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="359cd-108">In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="359cd-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="359cd-109">Creare un account Cosmos DB con un nome univoco nello stesso gruppo di risorse delle altre risorse di questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="359cd-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="359cd-110">Dopo avere creato l'account Cosmos DB, creare un nuovo database denominato **imagesdb** nell'account.</span><span class="sxs-lookup"><span data-stu-id="359cd-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="359cd-111">Dopo la creazione del database, creare una nuova raccolta denominata **images** nel database con una velocità effettiva di 400 unità richiesta (UR).</span><span class="sxs-lookup"><span data-stu-id="359cd-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="359cd-112">Salvare un documento in Cosmos DB quando viene creata un'anteprima</span><span class="sxs-lookup"><span data-stu-id="359cd-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="359cd-113">L'associazione di output di Cosmos DB consente di creare documenti in una raccolta Cosmos DB da Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="359cd-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="359cd-114">Nei passaggi che seguono si configura un'associazione di output di Cosmos DB nella funzione **ResizeImage** e si modifica la funzione in modo che restituisca un documento (oggetto) da salvare.</span><span class="sxs-lookup"><span data-stu-id="359cd-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="359cd-115">Aprire l'app per le funzioni nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="359cd-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="359cd-116">Nel riquadro di spostamento di sinistra espandere la funzione **ResizeImage** e quindi selezionare **Integrazione**.</span><span class="sxs-lookup"><span data-stu-id="359cd-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="359cd-117">Sotto **Output** fare clic su **Nuovo output**.</span><span class="sxs-lookup"><span data-stu-id="359cd-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="359cd-118">Individuare l'elemento **Azure Cosmos DB** e selezionarlo.</span><span class="sxs-lookup"><span data-stu-id="359cd-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="359cd-119">Quindi fare clic su **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="359cd-119">Then click **Select**.</span></span>

    ![Selezionare Nuovo output](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="359cd-121">Compilare i campi sotto **Azure Cosmos DB output** (Output Azure Cosmos DB) con i valori seguenti.</span><span class="sxs-lookup"><span data-stu-id="359cd-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="359cd-122">Impostazione</span><span class="sxs-lookup"><span data-stu-id="359cd-122">Setting</span></span>      |  <span data-ttu-id="359cd-123">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="359cd-123">Suggested value</span></span>   | <span data-ttu-id="359cd-124">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="359cd-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="359cd-125">**Nome del parametro del documento**</span><span class="sxs-lookup"><span data-stu-id="359cd-125">**Document parameter name**</span></span> | <span data-ttu-id="359cd-126">Selezionare **Usa valore restituito della funzione**</span><span class="sxs-lookup"><span data-stu-id="359cd-126">Select **Use function return value**</span></span> | <span data-ttu-id="359cd-127">Il valore della casella di testo viene impostato automaticamente su **$return**.</span><span class="sxs-lookup"><span data-stu-id="359cd-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="359cd-128">**Nome database**</span><span class="sxs-lookup"><span data-stu-id="359cd-128">**Database name**</span></span> | <span data-ttu-id="359cd-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="359cd-129">imagesdb</span></span> | <span data-ttu-id="359cd-130">Usare il nome del database creato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="359cd-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="359cd-131">**Nome raccolta**</span><span class="sxs-lookup"><span data-stu-id="359cd-131">**Collection name**</span></span> | <span data-ttu-id="359cd-132">images</span><span class="sxs-lookup"><span data-stu-id="359cd-132">images</span></span> | <span data-ttu-id="359cd-133">Usare il nome della raccolta creata in precedenza.</span><span class="sxs-lookup"><span data-stu-id="359cd-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="359cd-134">Accanto a **Connessione all'account Azure Cosmos DB** fare clic su **nuova**.</span><span class="sxs-lookup"><span data-stu-id="359cd-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="359cd-135">Selezionare l'account Cosmos DB creato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="359cd-135">Select the Cosmos DB account you previously created.</span></span>

    ![Immettere le impostazioni per l'associazione di output di Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="359cd-137">Fare clic su **Salva** per creare l'associazione di output di Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="359cd-138">Fare clic sul nome della funzione **ResizeImage** a sinistra per aprire la funzione.</span><span class="sxs-lookup"><span data-stu-id="359cd-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="359cd-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="359cd-139">**C#**</span></span>

    1. <span data-ttu-id="359cd-140">(C#) Cambiare il tipo restituito dalla funzione da **void** a **object**.</span><span class="sxs-lookup"><span data-stu-id="359cd-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="359cd-141">(C#) Alla fine della funzione, aggiungere il blocco di codice seguente per restituire il documento da salvare:</span><span class="sxs-lookup"><span data-stu-id="359cd-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx per la funzione ResizeImages dopo le modifiche](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="359cd-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="359cd-143">**JavaScript**</span></span>

    1. <span data-ttu-id="359cd-144">(JavaScript) Modificare l'istruzione `context.done()` nella clausola `else` per restituire il documento da salvare a Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

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

1. <span data-ttu-id="359cd-145">Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.</span><span class="sxs-lookup"><span data-stu-id="359cd-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="359cd-146">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="359cd-146">Click **Save**.</span></span> <span data-ttu-id="359cd-147">Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.</span><span class="sxs-lookup"><span data-stu-id="359cd-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="359cd-148">Creare una funzione per elencare le immagini da Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="359cd-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="359cd-149">L'applicazione Web richiede un'API per recuperare i metadati delle immagini da Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="359cd-150">Nei passaggi che seguono</span><span class="sxs-lookup"><span data-stu-id="359cd-150">In the following steps.</span></span> <span data-ttu-id="359cd-151">si creerà una funzione attivata tramite HTTP che usa un'associazione di input di Cosmos DB per eseguire una query sulla raccolta di database.</span><span class="sxs-lookup"><span data-stu-id="359cd-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="359cd-152">Nell'app per le funzioni passare il puntatore del mouse su **Funzioni** a sinistra e fare clic su **+** per creare una nuova funzione.</span><span class="sxs-lookup"><span data-stu-id="359cd-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="359cd-153">Individuare il modello **HttpTrigger** e selezionarlo.</span><span class="sxs-lookup"><span data-stu-id="359cd-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="359cd-154">Usare questi valori per creare una funzione che genera un URL per ottenere le immagini.</span><span class="sxs-lookup"><span data-stu-id="359cd-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="359cd-155">Impostazione</span><span class="sxs-lookup"><span data-stu-id="359cd-155">Setting</span></span>      |  <span data-ttu-id="359cd-156">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="359cd-156">Suggested value</span></span>   | <span data-ttu-id="359cd-157">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="359cd-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="359cd-158">**Dare un nome alla funzione**</span><span class="sxs-lookup"><span data-stu-id="359cd-158">**Name your function**</span></span> | <span data-ttu-id="359cd-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="359cd-159">GetImages</span></span> | <span data-ttu-id="359cd-160">Digitare questo nome esattamente come mostrato in modo che l'applicazione possa individuare la funzione.</span><span class="sxs-lookup"><span data-stu-id="359cd-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="359cd-161">**Livello di autorizzazione**</span><span class="sxs-lookup"><span data-stu-id="359cd-161">**Authorization level**</span></span> | <span data-ttu-id="359cd-162">Anonima</span><span class="sxs-lookup"><span data-stu-id="359cd-162">Anonymous</span></span> | <span data-ttu-id="359cd-163">Consente l'accesso pubblico alla funzione.</span><span class="sxs-lookup"><span data-stu-id="359cd-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="359cd-164">Fare clic su **Create**(Crea).</span><span class="sxs-lookup"><span data-stu-id="359cd-164">Click **Create**.</span></span>

1. <span data-ttu-id="359cd-165">Dopo la creazione della nuova funzione, fare clic su **Integrazione** sotto il nome della funzione nel riquadro di spostamento di sinistra.</span><span class="sxs-lookup"><span data-stu-id="359cd-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="359cd-166">Fare clic su **Nuovo input** e selezionare **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="359cd-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Selezionare Nuovo input](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="359cd-168">Fare clic su **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="359cd-168">Click **Select**.</span></span>

1. <span data-ttu-id="359cd-169">Inserire i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="359cd-169">Fill out the following values:</span></span>

    | <span data-ttu-id="359cd-170">Impostazione</span><span class="sxs-lookup"><span data-stu-id="359cd-170">Setting</span></span>      |  <span data-ttu-id="359cd-171">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="359cd-171">Suggested value</span></span>   | <span data-ttu-id="359cd-172">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="359cd-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="359cd-173">**Nome del parametro del documento**</span><span class="sxs-lookup"><span data-stu-id="359cd-173">**Document parameter name**</span></span> | <span data-ttu-id="359cd-174">documents</span><span class="sxs-lookup"><span data-stu-id="359cd-174">documents</span></span> | <span data-ttu-id="359cd-175">Corrisponde al nome del parametro nella funzione.</span><span class="sxs-lookup"><span data-stu-id="359cd-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="359cd-176">**Nome database**</span><span class="sxs-lookup"><span data-stu-id="359cd-176">**Database name**</span></span> | <span data-ttu-id="359cd-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="359cd-177">imagesdb</span></span> |  |
    | <span data-ttu-id="359cd-178">**Nome raccolta**</span><span class="sxs-lookup"><span data-stu-id="359cd-178">**Collection name**</span></span> | <span data-ttu-id="359cd-179">images</span><span class="sxs-lookup"><span data-stu-id="359cd-179">images</span></span> |  |
    | <span data-ttu-id="359cd-180">**SQL query**</span><span class="sxs-lookup"><span data-stu-id="359cd-180">**SQL query**</span></span> | <span data-ttu-id="359cd-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="359cd-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="359cd-182">Ottiene i documenti, a cominciare dal più recente.</span><span class="sxs-lookup"><span data-stu-id="359cd-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="359cd-183">**Connessione all'account Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="359cd-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="359cd-184">Selezionare la stringa di connessione esistente</span><span class="sxs-lookup"><span data-stu-id="359cd-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="359cd-185">Fare clic su **Salva** per creare l'associazione di input.</span><span class="sxs-lookup"><span data-stu-id="359cd-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="359cd-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="359cd-186">**C#**</span></span>

    1. <span data-ttu-id="359cd-187">Fare clic sul nome della funzione per aprire la finestra del codice, quindi sostituire tutto il contenuto di **run.csx** con il contenuto di [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="359cd-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="359cd-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="359cd-188">**JavaScript**</span></span>

    1. <span data-ttu-id="359cd-189">Fare clic sul nome della funzione per aprire la finestra del codice, quindi sostituire tutto il contenuto di **index.js** con il contenuto di [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="359cd-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="359cd-190">Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.</span><span class="sxs-lookup"><span data-stu-id="359cd-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="359cd-191">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="359cd-191">Click **Save**.</span></span> <span data-ttu-id="359cd-192">Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.</span><span class="sxs-lookup"><span data-stu-id="359cd-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="359cd-193">Test dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="359cd-193">Test the application</span></span>

1. <span data-ttu-id="359cd-194">Aprire l'applicazione in un browser.</span><span class="sxs-lookup"><span data-stu-id="359cd-194">Open the application in a browser.</span></span> <span data-ttu-id="359cd-195">Selezionare un file di immagine e caricarlo.</span><span class="sxs-lookup"><span data-stu-id="359cd-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="359cd-196">Dopo alcuni secondi, nella pagina verrà visualizzata l'anteprima della nuova immagine.</span><span class="sxs-lookup"><span data-stu-id="359cd-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="359cd-197">Nel portale di Azure usare la casella di ricerca per cercare il proprio account Cosmos DB in base al nome.</span><span class="sxs-lookup"><span data-stu-id="359cd-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="359cd-198">Fare clic sull'account per aprirlo.</span><span class="sxs-lookup"><span data-stu-id="359cd-198">Click it to open it.</span></span>

1. <span data-ttu-id="359cd-199">Fare clic su **Esplora dati** a sinistra per sfogliare le raccolte e i documenti.</span><span class="sxs-lookup"><span data-stu-id="359cd-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="359cd-200">Sotto il database **imagesdb** selezionare la raccolta **images**.</span><span class="sxs-lookup"><span data-stu-id="359cd-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="359cd-201">Verificare che sia stato creato un documento per l'immagine caricata.</span><span class="sxs-lookup"><span data-stu-id="359cd-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Esplora dati con un documento per un'immagine caricata](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="359cd-203">Summary</span><span class="sxs-lookup"><span data-stu-id="359cd-203">Summary</span></span>

<span data-ttu-id="359cd-204">In questo modulo è stato descritto come creare un account, un database e una raccolta di Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="359cd-205">Si è inoltre visto come usare le associazioni di Cosmos DB per salvare e recuperare i metadati delle immagini nella raccolta di Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="359cd-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="359cd-206">Si vedrà ora come generare automaticamente una didascalia per ogni immagine caricata usando Servizi cognitivi Microsoft.</span><span class="sxs-lookup"><span data-stu-id="359cd-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>