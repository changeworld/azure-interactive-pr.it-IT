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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460025"
---
<span data-ttu-id="3bc88-103">In questa esercitazione viene distribuita un'applicazione Web semplice che presenta un'interfaccia utente basata su HTML.</span><span class="sxs-lookup"><span data-stu-id="3bc88-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="3bc88-104">Un back-end senza server consente all'applicazione di caricare le immagini e ottenere automaticamente le didascalie con la descrizione delle immagini.</span><span class="sxs-lookup"><span data-stu-id="3bc88-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Esecuzione dell'app Web](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="3bc88-106">Il diagramma seguente mostra i servizi di Azure usati dall'applicazione:</span><span class="sxs-lookup"><span data-stu-id="3bc88-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="3bc88-107">Archiviazione BLOB gestisce il contenuto Web statico (HTML, CSS o JS) e archivia le immagini.</span><span class="sxs-lookup"><span data-stu-id="3bc88-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="3bc88-108">Funzioni di Azure gestisce i caricamenti delle immagini, il ridimensionamento e l'archiviazione dei metadati.</span><span class="sxs-lookup"><span data-stu-id="3bc88-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="3bc88-109">Cosmos DB archivia i metadati delle immagini.</span><span class="sxs-lookup"><span data-stu-id="3bc88-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="3bc88-110">App per la logica recupera le didascalie delle immagini dall'API Visione artificiale.</span><span class="sxs-lookup"><span data-stu-id="3bc88-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="3bc88-111">Azure Active Directory gestisce l'autenticazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="3bc88-111">Azure Active Directory manages user authentication.</span></span>

![Diagramma dell'architettura della soluzione](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="3bc88-113">In questa esercitazione si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="3bc88-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="3bc88-114">Configurare l'archiviazione BLOB di Azure per ospitare un sito Web statico e le immagini caricate.</span><span class="sxs-lookup"><span data-stu-id="3bc88-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="3bc88-115">Caricare le immagini nell'archiviazione BLOB di Azure con Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="3bc88-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="3bc88-116">Ridimensionare le immagini con Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="3bc88-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="3bc88-117">Archiviare i metadati delle immagini in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="3bc88-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="3bc88-118">Usare l'API Visione artificiale di Servizi cognitivi per generare automaticamente le didascalie delle immagini.</span><span class="sxs-lookup"><span data-stu-id="3bc88-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="3bc88-119">Usare Azure Active Directory per proteggere l'app Web tramite l'autenticazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="3bc88-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
