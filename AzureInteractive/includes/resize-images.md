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
<span data-ttu-id="06cf6-103">Nel modulo precedente si è visto come una funzione senza server possa facilitare il caricamento sicuro di immagini nell'archivio BLOB da un'applicazione Web.</span><span class="sxs-lookup"><span data-stu-id="06cf6-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="06cf6-104">In questo modulo si creerà un'altra funzione senza server che controllerà il caricamento di immagini e creerà le relative anteprime.</span><span class="sxs-lookup"><span data-stu-id="06cf6-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="06cf6-105">Creare un contenitore di archiviazione BLOB per le anteprime</span><span class="sxs-lookup"><span data-stu-id="06cf6-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="06cf6-106">Le immagini con le dimensioni originali vengono archiviate in un contenitore denominato **images**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="06cf6-107">È necessario un altro contenitore per memorizzare le anteprime delle immagini.</span><span class="sxs-lookup"><span data-stu-id="06cf6-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="06cf6-108">Verificare di essere ancora connessi a Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="06cf6-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="06cf6-109">In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="06cf6-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="06cf6-110">Creare un nuovo contenitore denominato **thumbnails** nel proprio account di archiviazione con accesso pubblico a tutti i BLOB.</span><span class="sxs-lookup"><span data-stu-id="06cf6-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="06cf6-111">Creare una funzione senza server attivata dal BLOB</span><span class="sxs-lookup"><span data-stu-id="06cf6-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="06cf6-112">Un trigger definisce come viene richiamata una funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="06cf6-113">La funzione che si creerà ora usa un trigger BLOB: la funzione viene richiamata automaticamente quando un BLOB (file immagine) viene caricato nel contenitore **images**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="06cf6-114">Una funzione deve avere un trigger.</span><span class="sxs-lookup"><span data-stu-id="06cf6-114">A function must have one trigger.</span></span> <span data-ttu-id="06cf6-115">I trigger hanno dei dati associati, ovvero in genere il payload che ha attivato la funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="06cf6-116">Le associazioni definiscono il modo in cui una funzione legge o scrive i dati in Azure o in servizi di terze parti.</span><span class="sxs-lookup"><span data-stu-id="06cf6-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="06cf6-117">Questa funzione crea una versione di anteprima dell'immagine che la attiva e salva l'anteprima in un contenitore denominato *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="06cf6-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="06cf6-118">Aprire l'app per le funzioni nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="06cf6-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="06cf6-119">Nel riquadro di spostamento di sinistra della finestra dell'app per le funzioni passare il mouse su **Funzioni** e fare clic su **+** per iniziare a creare una nuova funzione senza server.</span><span class="sxs-lookup"><span data-stu-id="06cf6-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="06cf6-120">Se viene visualizzata una pagina della guida introduttiva, fare clic su **Funzione personalizzata** per visualizzare un elenco di modelli di funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="06cf6-121">Individuare il modello **BlobTrigger** e selezionarlo.</span><span class="sxs-lookup"><span data-stu-id="06cf6-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="06cf6-122">Usare questi valori per creare una funzione che crea anteprime quando vengono caricate immagini.</span><span class="sxs-lookup"><span data-stu-id="06cf6-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="06cf6-123">Impostazione</span><span class="sxs-lookup"><span data-stu-id="06cf6-123">Setting</span></span>      |  <span data-ttu-id="06cf6-124">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="06cf6-124">Suggested value</span></span>   | <span data-ttu-id="06cf6-125">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="06cf6-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="06cf6-126">**Lingua**</span><span class="sxs-lookup"><span data-stu-id="06cf6-126">**Language**</span></span> | <span data-ttu-id="06cf6-127">C# o JavaScript</span><span class="sxs-lookup"><span data-stu-id="06cf6-127">C# or JavaScript</span></span> | <span data-ttu-id="06cf6-128">Scegliere il linguaggio preferito</span><span class="sxs-lookup"><span data-stu-id="06cf6-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="06cf6-129">**Dare un nome alla funzione**</span><span class="sxs-lookup"><span data-stu-id="06cf6-129">**Name your function**</span></span> | <span data-ttu-id="06cf6-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="06cf6-130">ResizeImage</span></span> | <span data-ttu-id="06cf6-131">Digitare questo nome esattamente come mostrato in modo che l'applicazione possa individuare la funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="06cf6-132">**Percorso**</span><span class="sxs-lookup"><span data-stu-id="06cf6-132">**Path**</span></span> | <span data-ttu-id="06cf6-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="06cf6-133">images/{name}</span></span> | <span data-ttu-id="06cf6-134">Esegue la funzione quando appare un file nel contenitore **images**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="06cf6-135">**Informazioni account di archiviazione**</span><span class="sxs-lookup"><span data-stu-id="06cf6-135">**Storage account information**</span></span> | <span data-ttu-id="06cf6-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="06cf6-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="06cf6-137">Usare la variabile di ambiente creata in precedenza con la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Immettere le impostazioni per la nuova funzione](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="06cf6-139">Fare clic su **Crea** per creare la funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="06cf6-140">Quando la funzione è creata, fare clic su **Integrazione** per visualizzare le associazioni di attivazione, input e output.</span><span class="sxs-lookup"><span data-stu-id="06cf6-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="06cf6-141">Fare clic su **Nuovo output** per creare una nuova associazione di output.</span><span class="sxs-lookup"><span data-stu-id="06cf6-141">Click **New output** to create a new output binding.</span></span>

    ![Selezionare Nuovo output nella scheda Integrazione](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="06cf6-143">Selezionare **Archiviazione BLOB di Azure** e fare clic su **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="06cf6-144">Può essere necessario scorrere verso il basso per vedere il pulsante **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Selezionare Archiviazione BLOB di Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="06cf6-146">Immettere i valori seguenti.</span><span class="sxs-lookup"><span data-stu-id="06cf6-146">Enter the following values.</span></span>

    | <span data-ttu-id="06cf6-147">Impostazione</span><span class="sxs-lookup"><span data-stu-id="06cf6-147">Setting</span></span>      |  <span data-ttu-id="06cf6-148">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="06cf6-148">Suggested value</span></span>   | <span data-ttu-id="06cf6-149">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="06cf6-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="06cf6-150">**Nome del parametro del BLOB**</span><span class="sxs-lookup"><span data-stu-id="06cf6-150">**Blob parameter name**</span></span> | <span data-ttu-id="06cf6-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="06cf6-151">thumbnail</span></span> | <span data-ttu-id="06cf6-152">La funzione usa il parametro con questo nome per scrivere l'anteprima.</span><span class="sxs-lookup"><span data-stu-id="06cf6-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="06cf6-153">**Usa valore restituito della funzione**</span><span class="sxs-lookup"><span data-stu-id="06cf6-153">**Use function return value**</span></span> | <span data-ttu-id="06cf6-154">No </span><span class="sxs-lookup"><span data-stu-id="06cf6-154">No</span></span> |  |
    | <span data-ttu-id="06cf6-155">**Percorso**</span><span class="sxs-lookup"><span data-stu-id="06cf6-155">**Path**</span></span> | <span data-ttu-id="06cf6-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="06cf6-156">thumbnails/{name}</span></span> | <span data-ttu-id="06cf6-157">Le anteprime vengono scritti in un contenitore denominato **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="06cf6-158">**Connessione dell'account di archiviazione**</span><span class="sxs-lookup"><span data-stu-id="06cf6-158">**Storage account connection**</span></span> | <span data-ttu-id="06cf6-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="06cf6-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="06cf6-160">Usare la variabile di ambiente creata in precedenza con la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Immettere le impostazioni per l'associazione di output del BLOB](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="06cf6-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="06cf6-162">**JavaScript**</span></span>

    1. <span data-ttu-id="06cf6-163">(JavaScript) Fare clic su **Editor avanzato** nell'angolo superiore destro della finestra per visualizzare il codice JSON che rappresenta le associazioni.</span><span class="sxs-lookup"><span data-stu-id="06cf6-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="06cf6-164">(JavaScript) Nell'associazione `blobTrigger` aggiungere una proprietà denominata `dataType` con valore `binary`.</span><span class="sxs-lookup"><span data-stu-id="06cf6-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="06cf6-165">In questo modo si configura l'associazione per il passaggio dei contenuti del BLOB alla funzione come dati binari.</span><span class="sxs-lookup"><span data-stu-id="06cf6-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

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

1. <span data-ttu-id="06cf6-166">Fare clic su **Salva** per creare la nuova associazione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="06cf6-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="06cf6-167">**C#**</span></span>

    1. <span data-ttu-id="06cf6-168">(C#) Selezionare il nome della funzione **ResizeImage** nel riquadro di spostamento di sinistra per aprire il codice sorgente della funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="06cf6-169">(C#) La funzione richiede un pacchetto NuGet chiamato **ImageResizer** per generare le anteprime.</span><span class="sxs-lookup"><span data-stu-id="06cf6-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="06cf6-170">I pacchetti NuGet vengono aggiunti alle funzioni C# tramite un file **project.json**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="06cf6-171">Per creare il file, fare clic su **Visualizza file** a destra per visualizzare i file che costituiscono la funzione.</span><span class="sxs-lookup"><span data-stu-id="06cf6-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="06cf6-172">(C#) Fare clic su **Aggiungi** per aggiungere un nuovo file denominato **project.json**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="06cf6-173">(C#) Copiare il contenuto di [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) nel file appena creato.</span><span class="sxs-lookup"><span data-stu-id="06cf6-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="06cf6-174">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="06cf6-174">Save the file.</span></span> <span data-ttu-id="06cf6-175">I pacchetti vengono ripristinati automaticamente quando il file viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="06cf6-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![file project.json con ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="06cf6-177">(C#) Selezionare **run.csx** in **Visualizza file** e sostituirne il contenuto con quello presente in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="06cf6-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="06cf6-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="06cf6-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="06cf6-179">(JavaScript) Questa funzione richiede il pacchetto `jimp` da npm per ridimensionare la foto.</span><span class="sxs-lookup"><span data-stu-id="06cf6-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="06cf6-180">Per installare il pacchetto npm, fare clic sul nome dell'app per le funzioni nel riquadro di spostamento di sinistra e fare clic su **Funzionalità della piattaforma**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="06cf6-181">(JavaScript) Fare clic su **Console** per visualizzare una finestra della console.</span><span class="sxs-lookup"><span data-stu-id="06cf6-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="06cf6-182">(JavaScript) Eseguire il comando `npm install jimp` nella console.</span><span class="sxs-lookup"><span data-stu-id="06cf6-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="06cf6-183">Il completamento dell'operazione potrebbe richiedere un minuto o due.</span><span class="sxs-lookup"><span data-stu-id="06cf6-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="06cf6-184">(JavaScript) Fare clic sul nome della funzione **ResizeImage** nel riquadro di spostamento di sinistra per visualizzare la funzione, quindi sostituire tutto il contenuto del file **index.js** con il contenuto di [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="06cf6-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="06cf6-185">Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.</span><span class="sxs-lookup"><span data-stu-id="06cf6-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="06cf6-186">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-186">Click **Save**.</span></span> <span data-ttu-id="06cf6-187">Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.</span><span class="sxs-lookup"><span data-stu-id="06cf6-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="06cf6-188">Testare la funzione senza server</span><span class="sxs-lookup"><span data-stu-id="06cf6-188">Test the serverless function</span></span>

1. <span data-ttu-id="06cf6-189">Aprire l'applicazione in un browser.</span><span class="sxs-lookup"><span data-stu-id="06cf6-189">Open the application in a browser.</span></span> <span data-ttu-id="06cf6-190">Selezionare un file di immagine e caricarlo.</span><span class="sxs-lookup"><span data-stu-id="06cf6-190">Select an image file and upload it.</span></span> <span data-ttu-id="06cf6-191">Il file viene caricato ma, poiché non è stata ancora aggiunta la possibilità di visualizzare le immagini, l'app non mostra la foto caricata.</span><span class="sxs-lookup"><span data-stu-id="06cf6-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="06cf6-192">In Cloud Shell verificare che l'immagine sia stata caricata nel contenitore **images**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="06cf6-193">Verificare che sia stata creata l'anteprima in un contenitore denominato **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="06cf6-194">Ottenere l'URL dell'anteprima.</span><span class="sxs-lookup"><span data-stu-id="06cf6-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="06cf6-195">Aprire l'URL in un browser e verificare che l'anteprima sia stata creata correttamente.</span><span class="sxs-lookup"><span data-stu-id="06cf6-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="06cf6-196">Prima di passare all'esercitazione successiva, eliminare tutti i file nei contenitori **images** e **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="06cf6-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="06cf6-197">Summary</span><span class="sxs-lookup"><span data-stu-id="06cf6-197">Summary</span></span>

<span data-ttu-id="06cf6-198">In questo modulo è stata creata una funzione senza server per creare un'anteprima ogni volta che viene caricata un'immagine in un contenitore di archiviazione BLOB.</span><span class="sxs-lookup"><span data-stu-id="06cf6-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="06cf6-199">Si vedrà ora come usare Azure Cosmos DB per memorizzare ed elencare i metadati delle immagini.</span><span class="sxs-lookup"><span data-stu-id="06cf6-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
