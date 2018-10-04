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
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460000"
---
<span data-ttu-id="d2f86-103">A questo punto, l'applicazione è una raccolta funzionale che consente di caricare e visualizzare le immagini.</span><span class="sxs-lookup"><span data-stu-id="d2f86-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="d2f86-104">In questo modulo viene illustrato come usare l'API Visione artificiale di Servizi cognitivi Microsoft per generare le didascalie per le immagini caricate e salvare le didascalie con i metadati delle immagini in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d2f86-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="d2f86-105">Creare un account di Visione artificiale</span><span class="sxs-lookup"><span data-stu-id="d2f86-105">Create a Computer Vision account</span></span>

<span data-ttu-id="d2f86-106">Servizi cognitivi Microsoft è una raccolta di servizi che consentono agli sviluppatori di rendere le applicazioni più intelligenti.</span><span class="sxs-lookup"><span data-stu-id="d2f86-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="d2f86-107">L'API Visione artificiale è un servizio senza server che elabora le immagini usando algoritmi avanzati e restituisce informazioni su ogni immagine.</span><span class="sxs-lookup"><span data-stu-id="d2f86-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="d2f86-108">Include un livello gratuito che offre fino a 5.000 chiamate API al mese.</span><span class="sxs-lookup"><span data-stu-id="d2f86-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="d2f86-109">Verificare di essere ancora connessi a Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="d2f86-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="d2f86-110">In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="d2f86-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="d2f86-111">Creare un nuovo account di Servizi cognitivi di tipo **ComputerVision** con un nome univoco nel gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="d2f86-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="d2f86-112">Per il livello gratuito usare **F0** come SKU.</span><span class="sxs-lookup"><span data-stu-id="d2f86-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="d2f86-113">Se si ha già un account esistente di Visione artificiale, potrebbe essere necessario creare un account Standard (S1); tuttavia, ciò può comportare alcuni costi.</span><span class="sxs-lookup"><span data-stu-id="d2f86-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="d2f86-114">Creare le impostazioni dell'app per le funzioni per l'URL e la chiave di Visione artificiale</span><span class="sxs-lookup"><span data-stu-id="d2f86-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="d2f86-115">Per chiamare l'API Visione artificiale, sono necessari un URL e una chiave.</span><span class="sxs-lookup"><span data-stu-id="d2f86-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="d2f86-116">Ottenere l'URL e la chiave API Visione artificiale e salvarli nelle variabili bash.</span><span class="sxs-lookup"><span data-stu-id="d2f86-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="d2f86-117">Creare le impostazioni dell'app denominate rispettivamente **COMP_VISION_KEY** e **COMP_VISION_URL** nell'app per le funzioni.</span><span class="sxs-lookup"><span data-stu-id="d2f86-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="d2f86-118">Chiamare l'API Visione artificiale dalla funzione ResizeImage</span><span class="sxs-lookup"><span data-stu-id="d2f86-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="d2f86-119">Nei passaggi seguenti si modifica la funzione **ResizeImage** per chiamare l'API Visione artificiale per descrivere ogni immagine caricata e salvare la descrizione in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d2f86-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="d2f86-120">Aprire l'app per le funzioni nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="d2f86-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="d2f86-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="d2f86-121">**C#**</span></span>

    1. <span data-ttu-id="d2f86-122">(C#) Usare il riquadro di spostamento a sinistra per trovare la funzione **ResizeImage** e aprire la finestra del codice.</span><span class="sxs-lookup"><span data-stu-id="d2f86-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="d2f86-123">(C#) Sostituire il codice con il contenuto di [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="d2f86-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="d2f86-124">Questo codice usa `HttpClient` per chiamare l'API Visione artificiale e salvare il risultato in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d2f86-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="d2f86-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="d2f86-125">**JavaScript**</span></span>

    1. <span data-ttu-id="d2f86-126">(JavaScript) Questa funzione richiede il pacchetto `axios` di npm per effettuare una chiamata HTTP all'API Visione artificiale.</span><span class="sxs-lookup"><span data-stu-id="d2f86-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="d2f86-127">Per installare il pacchetto npm, fare clic sul nome dell'app per le funzioni nel riquadro di spostamento di sinistra e fare clic su **Funzionalità della piattaforma**.</span><span class="sxs-lookup"><span data-stu-id="d2f86-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="d2f86-128">(JavaScript) Fare clic su **Console** per visualizzare una finestra della console.</span><span class="sxs-lookup"><span data-stu-id="d2f86-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="d2f86-129">(JavaScript) Eseguire il comando `npm install axios` nella console.</span><span class="sxs-lookup"><span data-stu-id="d2f86-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="d2f86-130">Il completamento dell'operazione potrebbe richiedere un minuto o due.</span><span class="sxs-lookup"><span data-stu-id="d2f86-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="d2f86-131">(JavaScript) Fare clic sul nome della funzione (**ResizeImage**) nel riquadro di spostamento di sinistra per visualizzare la funzione e quindi sostituire tutto il contenuto del file **index.js** con il contenuto di [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="d2f86-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="d2f86-132">Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.</span><span class="sxs-lookup"><span data-stu-id="d2f86-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="d2f86-133">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="d2f86-133">Click **Save**.</span></span> <span data-ttu-id="d2f86-134">Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.</span><span class="sxs-lookup"><span data-stu-id="d2f86-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="d2f86-135">Test dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="d2f86-135">Test the application</span></span>

1. <span data-ttu-id="d2f86-136">Aprire l'applicazione in un browser.</span><span class="sxs-lookup"><span data-stu-id="d2f86-136">Open the application in a browser.</span></span> <span data-ttu-id="d2f86-137">Selezionare un file di immagine e caricarlo.</span><span class="sxs-lookup"><span data-stu-id="d2f86-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="d2f86-138">Dopo alcuni secondi, nella pagina verrà visualizzata l'anteprima della nuova immagine.</span><span class="sxs-lookup"><span data-stu-id="d2f86-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="d2f86-139">Passare il mouse sull'immagine per visualizzare la descrizione generata da Visione artificiale.</span><span class="sxs-lookup"><span data-stu-id="d2f86-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="d2f86-140">Summary</span><span class="sxs-lookup"><span data-stu-id="d2f86-140">Summary</span></span>

<span data-ttu-id="d2f86-141">In questo modulo è stato descritto come generare automaticamente le didascalie per ogni immagine caricata usando l'API Visione artificiale di Servizi cognitivi Microsoft.</span><span class="sxs-lookup"><span data-stu-id="d2f86-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="d2f86-142">Si vedrà ora come aggiungere l'autenticazione all'applicazione con l'autenticazione del servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="d2f86-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>
