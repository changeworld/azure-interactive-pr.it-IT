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
È stata completata la creazione di un'applicazione completa e senza server con i servizi di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si è terminato di usare questa applicazione, è possibile eseguire il comando seguente per eliminare tutte le risorse create durante l'esercitazione:

```azurecli
az group delete --name first-serverless-app
```

Quando richiesto, digitare `y`.  

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:
> [!div class="checklist"]
> * Configurare l'archiviazione BLOB di Azure per ospitare un sito Web statico e le immagini caricate.
> * Caricare le immagini nell'archiviazione BLOB di Azure con Funzioni di Azure.
> * Ridimensionare le immagini con Funzioni di Azure.
> * Archiviare i metadati delle immagini in Azure Cosmos DB.
> * Usare l'API Visione artificiale di Servizi cognitivi per generare automaticamente le didascalie delle immagini.
> * Usare Azure Active Directory per proteggere l'app Web tramite l'autenticazione degli utenti.

Per informazioni su come connettere ancora più servizi a Funzioni, continuare con l'esercitazione sulle app per la logica. 

> [!div class="nextstepaction"]
> [Funzioni con App per la logica](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
