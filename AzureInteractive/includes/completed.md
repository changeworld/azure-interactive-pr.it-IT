---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459999"
---
<span data-ttu-id="e3aa8-103">È stata completata la creazione di un'applicazione completa e senza server con i servizi di Azure.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="e3aa8-104">Pulire le risorse</span><span class="sxs-lookup"><span data-stu-id="e3aa8-104">Clean up resources</span></span>

<span data-ttu-id="e3aa8-105">Quando si è terminato di usare questa applicazione, è possibile eseguire il comando seguente per eliminare tutte le risorse create durante l'esercitazione:</span><span class="sxs-lookup"><span data-stu-id="e3aa8-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="e3aa8-106">Quando richiesto, digitare `y`.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="e3aa8-107">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e3aa8-107">Next steps</span></span>

<span data-ttu-id="e3aa8-108">Questa esercitazione illustra come:</span><span class="sxs-lookup"><span data-stu-id="e3aa8-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="e3aa8-109">Configurare l'archiviazione BLOB di Azure per ospitare un sito Web statico e le immagini caricate.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="e3aa8-110">Caricare le immagini nell'archiviazione BLOB di Azure con Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="e3aa8-111">Ridimensionare le immagini con Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="e3aa8-112">Archiviare i metadati delle immagini in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="e3aa8-113">Usare l'API Visione artificiale di Servizi cognitivi per generare automaticamente le didascalie delle immagini.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="e3aa8-114">Usare Azure Active Directory per proteggere l'app Web tramite l'autenticazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="e3aa8-115">Per informazioni su come connettere ancora più servizi a Funzioni, continuare con l'esercitazione sulle app per la logica.</span><span class="sxs-lookup"><span data-stu-id="e3aa8-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="e3aa8-116">Funzioni con App per la logica</span><span class="sxs-lookup"><span data-stu-id="e3aa8-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
