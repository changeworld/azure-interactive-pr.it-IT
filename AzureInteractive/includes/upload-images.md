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
<span data-ttu-id="88e3f-103">L'applicazione che si sta creando è una raccolta di foto</span><span class="sxs-lookup"><span data-stu-id="88e3f-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="88e3f-104">che usa JavaScript lato client per chiamare API per caricare e visualizzare immagini.</span><span class="sxs-lookup"><span data-stu-id="88e3f-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="88e3f-105">In questo modulo si creerà un'API usando una funzione senza server che genera un URL temporaneo per caricare un'immagine.</span><span class="sxs-lookup"><span data-stu-id="88e3f-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="88e3f-106">L'applicazione Web usa l'URL generato per caricare un'immagine in un archivio Blob usando l'[API REST dell'archivio BLOB](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="88e3f-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="88e3f-107">Creare un contenitore di archiviazione BLOB per le immagini</span><span class="sxs-lookup"><span data-stu-id="88e3f-107">Create a blob storage container for images</span></span>

<span data-ttu-id="88e3f-108">L'applicazione richiede un contenitore di archiviazione separato per caricare e ospitare immagini.</span><span class="sxs-lookup"><span data-stu-id="88e3f-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="88e3f-109">Verificare di essere ancora connessi a Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="88e3f-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="88e3f-110">In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="88e3f-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="88e3f-111">Creare un nuovo contenitore denominato **images** nel proprio account di archiviazione con accesso pubblico a tutti i BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="88e3f-112">Creare un'app per le funzioni di Azure</span><span class="sxs-lookup"><span data-stu-id="88e3f-112">Create an Azure Function app</span></span>

<span data-ttu-id="88e3f-113">Funzioni di Azure è un servizio per l'esecuzione di funzioni senza server.</span><span class="sxs-lookup"><span data-stu-id="88e3f-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="88e3f-114">Una funzione senza server può essere attivata (chiamata) da eventi come una richiesta HTTP o la creazione di un BLOB in un contenitore di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="88e3f-115">Un'app per le funzioni di Azure è un contenitore per una o più funzioni senza server.</span><span class="sxs-lookup"><span data-stu-id="88e3f-115">An Azure Function app is a container for one or more serverless functions.</span></span>

<span data-ttu-id="88e3f-116">Nel gruppo di risorse creato in precedenza, creare una nuova app per le funzioni di Azure, assegnandole il nome univoco **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="88e3f-117">Le app per le funzioni richiedono un account di archiviazione. In questa esercitazione si userà l'account di archiviazione esistente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a><span data-ttu-id="88e3f-118">Configurare l'app per le funzioni</span><span class="sxs-lookup"><span data-stu-id="88e3f-118">Configure the function app</span></span>

<span data-ttu-id="88e3f-119">L'app per le funzioni in questa esercitazione richiede la versione 1.x del runtime di Funzioni.</span><span class="sxs-lookup"><span data-stu-id="88e3f-119">The function app in this tutorial requires version 1.x of the Functions runtime.</span></span> <span data-ttu-id="88e3f-120">Se si configura l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione su `~1`, l'app per le funzioni viene associata alla versione 1.x più recente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-120">Setting the `FUNCTIONS_EXTENSION_VERSION` application setting to `~1` pins the function app to the latest 1.x version.</span></span> <span data-ttu-id="88e3f-121">Configurare l'impostazione dell'applicazione con il comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).</span><span class="sxs-lookup"><span data-stu-id="88e3f-121">Set application settings with the [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) command.</span></span>

<span data-ttu-id="88e3f-122">Nel comando seguente dell'interfaccia della riga di comando di Azure il valore "<app_name>" corrisponde al nome dell'app per le funzioni.</span><span class="sxs-lookup"><span data-stu-id="88e3f-122">In the following Azure CLI command, \`<app_name> is the name of your function app.</span></span>

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="88e3f-123">Creare una funzione senza server attivata da HTTP</span><span class="sxs-lookup"><span data-stu-id="88e3f-123">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="88e3f-124">L'applicazione Web della raccolta foto invia alla funzione senza server una richiesta HTTP di generazione di un URL temporaneo per caricare in modo sicuro un'immagine nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-124">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="88e3f-125">La funzione viene attivata da una richiesta HTTP e usa Azure Storage SDK per generare e restituire l'URL protetto.</span><span class="sxs-lookup"><span data-stu-id="88e3f-125">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="88e3f-126">Dopo che l'app per le funzioni è stata creata, cercarla nel portale di Azure usando la casella di ricerca e fare clic per aprirla.</span><span class="sxs-lookup"><span data-stu-id="88e3f-126">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Aprire l'app per le funzioni](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="88e3f-128">Nel riquadro di spostamento di sinistra della finestra dell'app per le funzioni passare il mouse su **Funzioni** e fare clic su **+** per iniziare a creare una nuova funzione senza server.</span><span class="sxs-lookup"><span data-stu-id="88e3f-128">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Creare una nuova funzione](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="88e3f-130">Fare clic su **Funzione personalizzata** per visualizzare un elenco di modelli di funzione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-130">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="88e3f-131">Individuare il modello **HttpTrigger** e fare clic sul linguaggio da usare (C# o JavaScript).</span><span class="sxs-lookup"><span data-stu-id="88e3f-131">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="88e3f-132">Usare questi valori per creare una funzione che genera un URL di caricamento BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-132">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="88e3f-133">Impostazione</span><span class="sxs-lookup"><span data-stu-id="88e3f-133">Setting</span></span>      |  <span data-ttu-id="88e3f-134">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="88e3f-134">Suggested value</span></span>   | <span data-ttu-id="88e3f-135">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="88e3f-135">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="88e3f-136">**Lingua**</span><span class="sxs-lookup"><span data-stu-id="88e3f-136">**Language**</span></span> | <span data-ttu-id="88e3f-137">C# o JavaScript</span><span class="sxs-lookup"><span data-stu-id="88e3f-137">C# or JavaScript</span></span> | <span data-ttu-id="88e3f-138">Selezionare il linguaggio da usare.</span><span class="sxs-lookup"><span data-stu-id="88e3f-138">Select the language you want to use.</span></span> |
    | <span data-ttu-id="88e3f-139">**Dare un nome alla funzione**</span><span class="sxs-lookup"><span data-stu-id="88e3f-139">**Name your function**</span></span> | <span data-ttu-id="88e3f-140">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="88e3f-140">GetUploadUrl</span></span> | <span data-ttu-id="88e3f-141">Digitare questo nome esattamente come mostrato in modo che l'applicazione possa individuare la funzione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-141">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="88e3f-142">**Livello di autorizzazione**</span><span class="sxs-lookup"><span data-stu-id="88e3f-142">**Authorization level**</span></span> | <span data-ttu-id="88e3f-143">Anonima</span><span class="sxs-lookup"><span data-stu-id="88e3f-143">Anonymous</span></span> | <span data-ttu-id="88e3f-144">Consente l'accesso pubblico alla funzione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-144">Allow the function to be accessed publicly.</span></span> |

    ![Inserire le impostazioni per una nuova funzione attivata da HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="88e3f-146">Fare clic su **Crea** per creare la funzione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-146">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="88e3f-147">**C#**</span><span class="sxs-lookup"><span data-stu-id="88e3f-147">**C#**</span></span> 

    1. <span data-ttu-id="88e3f-148">Quando viene visualizzato il codice sorgente della funzione, sostituire l'intero contenuto di **run.csx** con il contenuto di [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="88e3f-148">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="88e3f-149">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="88e3f-149">**JavaScript**</span></span> 

    1. <span data-ttu-id="88e3f-150">(JavaScript) Questa funzione richiede il pacchetto `azure-storage` da npm per generare il token di firma di accesso condiviso necessario per creare l'URL protetto.</span><span class="sxs-lookup"><span data-stu-id="88e3f-150">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="88e3f-151">Per installare il pacchetto npm, fare clic sul nome dell'app per le funzioni nel riquadro di spostamento di sinistra e fare clic su **Funzionalità della piattaforma**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-151">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="88e3f-152">(JavaScript) Fare clic su **Console** per visualizzare una finestra della console.</span><span class="sxs-lookup"><span data-stu-id="88e3f-152">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Aprire una finestra della console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="88e3f-154">(JavaScript) Assicurarsi che la directory corrente sia **d:\home\site\wwwroot** eseguendo il comando `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="88e3f-154">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="88e3f-155">(JavaScript) Eseguire il comando `npm init -y` per creare un file **package.json** vuoto.</span><span class="sxs-lookup"><span data-stu-id="88e3f-155">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="88e3f-156">(JavaScript) Eseguire il comando `npm install --save azure-storage` nella console per installare il pacchetto e salvarlo in **package.json**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-156">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="88e3f-157">Il completamento dell'operazione potrebbe richiedere un minuto o due.</span><span class="sxs-lookup"><span data-stu-id="88e3f-157">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="88e3f-158">(JavaScript) Fare clic sul nome della funzione (**GetUploadUrl**) nel riquadro di spostamento di sinistra per visualizzare la funzione, quindi sostituire tutto il contenuto del file **index.js** con il contenuto di [**/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="88e3f-158">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js dopo l'aggiornamento](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="88e3f-160">Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.</span><span class="sxs-lookup"><span data-stu-id="88e3f-160">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="88e3f-161">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-161">Click **Save**.</span></span> <span data-ttu-id="88e3f-162">Verificare nel pannello dei log che la funzione sia stata compilata correttamente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-162">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="88e3f-163">La funzione genera un cosiddetto URL di firma di accesso condiviso, che consente di caricare un file nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-163">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="88e3f-164">L'URL di firma di accesso condiviso è valido per un breve periodo di tempo e consente il caricamento di un solo file.</span><span class="sxs-lookup"><span data-stu-id="88e3f-164">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="88e3f-165">Consultare la documentazione dell'archivio BLOB per ulteriori informazioni sull'[uso delle firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="88e3f-165">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="88e3f-166">Aggiungere una variabile di ambiente per la stringa di connessione di archiviazione</span><span class="sxs-lookup"><span data-stu-id="88e3f-166">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="88e3f-167">La funzione appena creata richiede una stringa di connessione per l'account di archiviazione in modo da poter generare l'URL SAS.</span><span class="sxs-lookup"><span data-stu-id="88e3f-167">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="88e3f-168">Invece di codificare la stringa di connessione come hardcoded nel corpo della funzione, è possibile memorizzarla come impostazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-168">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="88e3f-169">Le impostazioni dell'applicazione sono accessibili come variabili di ambiente da tutte le funzioni nell'app per le funzioni.</span><span class="sxs-lookup"><span data-stu-id="88e3f-169">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="88e3f-170">In Cloud Shell, eseguire una query per recuperare la stringa di connessione dell'account di archiviazione e salvarla in una variabile Bash denominata **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-170">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="88e3f-171">Verificare che la variabile sia impostata correttamente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-171">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="88e3f-172">Creare una nuova impostazione dell'applicazione denominata **AZURE_STORAGE_CONNECTION_STRING** usando il valore salvato nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-172">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="88e3f-173">Verificare che l'output del comando contenga la nuova impostazione dell'applicazione con il valore corretto.</span><span class="sxs-lookup"><span data-stu-id="88e3f-173">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="88e3f-174">Testare la funzione senza server</span><span class="sxs-lookup"><span data-stu-id="88e3f-174">Test the serverless function</span></span>

<span data-ttu-id="88e3f-175">Oltre alla creazione e alla modifica delle funzioni, il portale di Azure fornisce uno strumento predefinito anche per il test delle funzioni.</span><span class="sxs-lookup"><span data-stu-id="88e3f-175">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="88e3f-176">Per testare la funzione HTTP senza server, fare clic sulla scheda **Test** a destra della finestra del codice per espandere il pannello di test.</span><span class="sxs-lookup"><span data-stu-id="88e3f-176">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="88e3f-177">Modificare **Metodo HTTP** in **GET**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-177">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="88e3f-178">In **Query** fare clic su *Aggiungi parametro*\* e aggiungere il parametro seguente:</span><span class="sxs-lookup"><span data-stu-id="88e3f-178">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="88e3f-179">name</span><span class="sxs-lookup"><span data-stu-id="88e3f-179">name</span></span>      |  <span data-ttu-id="88e3f-180">value</span><span class="sxs-lookup"><span data-stu-id="88e3f-180">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="88e3f-181">filename</span><span class="sxs-lookup"><span data-stu-id="88e3f-181">filename</span></span> | <span data-ttu-id="88e3f-182">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="88e3f-182">image1.jpg</span></span> |

1. <span data-ttu-id="88e3f-183">Fare clic su **Esegui** nel pannello di test per inviare una richiesta HTTP alla funzione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-183">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="88e3f-184">La funzione restituisce un URL di caricamento nell'output.</span><span class="sxs-lookup"><span data-stu-id="88e3f-184">The function returns an upload URL in the output.</span></span> <span data-ttu-id="88e3f-185">L'esecuzione della funzione viene visualizzata nel pannello dei log.</span><span class="sxs-lookup"><span data-stu-id="88e3f-185">The function execution appears in the logs panel.</span></span>

    ![Log che mostrano la corretta esecuzione della funzione](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="88e3f-187">Configurare CORS nell'app per le funzioni</span><span class="sxs-lookup"><span data-stu-id="88e3f-187">Configure CORS in the function app</span></span>

<span data-ttu-id="88e3f-188">Poiché il front-end dell'app è ospitato nell'archivio BLOB, ha un nome di dominio diverso rispetto all'app per le funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="88e3f-188">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="88e3f-189">Per consentire al codice JavaScript lato client di chiamare correttamente la funzione appena creata, l'app per le funzioni deve essere configurata per la condivisione di risorse tra le origini (CORS).</span><span class="sxs-lookup"><span data-stu-id="88e3f-189">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="88e3f-190">Nella barra di spostamento di sinistra della finestra dell'app per le funzioni, fare clic sul nome dell'app per le funzioni.</span><span class="sxs-lookup"><span data-stu-id="88e3f-190">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="88e3f-191">Fare clic su **Funzionalità della piattaforma** per visualizzare un elenco di funzionalità avanzate.</span><span class="sxs-lookup"><span data-stu-id="88e3f-191">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="88e3f-192">Sotto **API** fare clic su **CORS**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-192">Under **API**, click **CORS**.</span></span>

    ![Selezionare CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="88e3f-194">Aggiungere un'origine consentita per l'URL dell'applicazione dal modulo precedente, omettendo il carattere **/** finale (ad esempio: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="88e3f-194">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Impostazioni CORS con l'aggiunta dell'URL dell'applicazione Web senza server](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="88e3f-196">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-196">Click **Save**.</span></span>

1. <span data-ttu-id="88e3f-197">C#</span><span class="sxs-lookup"><span data-stu-id="88e3f-197">C#</span></span>

   1. <span data-ttu-id="88e3f-198">(C#) Tornare alla funzione `GetUploadUrl` e quindi selezionare la scheda **Integrazione**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-198">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="88e3f-199">(C#) In Metodi HTTP selezionati selezionare **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-199">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="88e3f-200">**GET**, **POST** e **OPTIONS** devono essere tutti selezionati.</span><span class="sxs-lookup"><span data-stu-id="88e3f-200">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="88e3f-201">CORS usa il metodo OPTIONS, che non è selezionato per impostazione predefinita per le funzioni C#.</span><span class="sxs-lookup"><span data-stu-id="88e3f-201">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="88e3f-202">(C#) Fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-202">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="88e3f-203">Restando nel portale, passare all'app per le funzioni, selezionare la scheda **Panoramica** e quindi fare clic su **Riavvia** per assicurarsi che le modifiche per CORS abbiano effetto.</span><span class="sxs-lookup"><span data-stu-id="88e3f-203">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="88e3f-204">Configurare CORS nell'account di archiviazione</span><span class="sxs-lookup"><span data-stu-id="88e3f-204">Configure CORS in the Storage account</span></span>

<span data-ttu-id="88e3f-205">Poiché l'applicazione effettua anche chiamate JavaScript lato client all'archiviazione BLOB per caricare i file, è necessario inoltre configurare l'account di archiviazione per CORS.</span><span class="sxs-lookup"><span data-stu-id="88e3f-205">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="88e3f-206">Eseguire il comando seguente per consentire a tutte le origini di caricare file nell'account di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-206">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="88e3f-207">Modificare l'app Web per caricare immagini</span><span class="sxs-lookup"><span data-stu-id="88e3f-207">Modify the web app to upload images</span></span>

<span data-ttu-id="88e3f-208">L'app Web recupera le impostazioni da un file denominato **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-208">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="88e3f-209">Nei passaggi seguenti si creerà il file usando Cloud Shell, quindi si imposterà `window.apiBaseUrl` sull'URL dell'app per le funzioni e `window.blobBaseUrl` sull'URL dell'endpoint di Archiviazione BLOB di Azure.</span><span class="sxs-lookup"><span data-stu-id="88e3f-209">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="88e3f-210">In Cloud Shell assicurarsi che la directory corrente sia la cartella **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-210">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="88e3f-211">Eseguire una query per ottenere l'URL dell'app per le funzioni e memorizzarlo in una variabile Bash denominata **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-211">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="88e3f-212">Verificare che la variabile sia impostata correttamente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="88e3f-213">Per impostare l'URI di base delle chiamate API all'app per le funzioni, creare **settings.js** e aggiungere l'URL dell'app per le funzioni, come segue.</span><span class="sxs-lookup"><span data-stu-id="88e3f-213">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="88e3f-214">È possibile apportare la modifica eseguendo il comando seguente o usando un editor da riga di comando come VIM.</span><span class="sxs-lookup"><span data-stu-id="88e3f-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="88e3f-215">Verificare che il file sia stato scritto correttamente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-215">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="88e3f-216">Eseguire una query per ottenere l'URL di base di archiviazione BLOB e memorizzarlo in una variabile Bash denominata **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-216">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="88e3f-217">Verificare che la variabile sia impostata correttamente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-217">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="88e3f-218">Per impostare l'URI di base delle chiamate API all'app per le funzioni, aggiungere l'URL di archiviazione come la riga di codice seguente a **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-218">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="88e3f-219">È possibile apportare la modifica eseguendo il comando seguente o usando un editor da riga di comando come VIM.</span><span class="sxs-lookup"><span data-stu-id="88e3f-219">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="88e3f-220">Verificare che il file sia stato scritto correttamente e che ora contenga 2 righe.</span><span class="sxs-lookup"><span data-stu-id="88e3f-220">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="88e3f-221">Caricare il file nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-221">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="88e3f-222">Testare l'applicazione Web</span><span class="sxs-lookup"><span data-stu-id="88e3f-222">Test the web application</span></span>

<span data-ttu-id="88e3f-223">A questo punto, l'applicazione della raccolta è in grado di caricare un'immagine nell'archivio BLOB, ma non è ancora in grado di visualizzare le immagini.</span><span class="sxs-lookup"><span data-stu-id="88e3f-223">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="88e3f-224">Tenterà di chiamare una funzione `GetImages` che non esiste ancora perché verrà creata in un modulo successivo.</span><span class="sxs-lookup"><span data-stu-id="88e3f-224">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="88e3f-225">La chiamata avrà esito negativo e la pagina Web apparirà bloccata su "Analisi in corso...", ma l'immagine selezionata verrà caricata correttamente.</span><span class="sxs-lookup"><span data-stu-id="88e3f-225">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="88e3f-226">È possibile verificare che un'immagine sia stata caricata correttamente controllando il contenuto del contenitore **images** nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="88e3f-226">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="88e3f-227">In una finestra del browser, passare all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="88e3f-227">In a browser window, browse to the application.</span></span> <span data-ttu-id="88e3f-228">Selezionare un file di immagine e caricarlo.</span><span class="sxs-lookup"><span data-stu-id="88e3f-228">Select an image file and upload it.</span></span> <span data-ttu-id="88e3f-229">Il file viene caricato ma, poiché non è stata ancora aggiunta la possibilità di visualizzare le immagini, l'app non mostra la foto caricata.</span><span class="sxs-lookup"><span data-stu-id="88e3f-229">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="88e3f-230">La pagina Web apparirà bloccata su "Analyzing image..." (Analisi immagine in corso...). Questo comportamento verrà corretto in seguito.</span><span class="sxs-lookup"><span data-stu-id="88e3f-230">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="88e3f-231">In Cloud Shell verificare che l'immagine sia stata caricata nel contenitore **images**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-231">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="88e3f-232">Prima di passare all'esercitazione successiva, eliminare tutti i file nel contenitore **images**.</span><span class="sxs-lookup"><span data-stu-id="88e3f-232">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="88e3f-233">Summary</span><span class="sxs-lookup"><span data-stu-id="88e3f-233">Summary</span></span>

<span data-ttu-id="88e3f-234">In questo modulo si è creata un'app per le funzioni di Azure e si è visto come usare una funzione senza server per consentire a un'applicazione Web di caricare immagini nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-234">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="88e3f-235">Si vedrà ora come creare anteprime per le immagini caricate usando una funzione senza server attivata dal BLOB.</span><span class="sxs-lookup"><span data-stu-id="88e3f-235">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
