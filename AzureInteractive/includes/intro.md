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
In questa esercitazione viene distribuita un'applicazione Web semplice che presenta un'interfaccia utente basata su HTML. Un back-end senza server consente all'applicazione di caricare le immagini e ottenere automaticamente le didascalie con la descrizione delle immagini.

![Esecuzione dell'app Web](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

Il diagramma seguente mostra i servizi di Azure usati dall'applicazione:

1. Archiviazione BLOB gestisce il contenuto Web statico (HTML, CSS o JS) e archivia le immagini.
2. Funzioni di Azure gestisce i caricamenti delle immagini, il ridimensionamento e l'archiviazione dei metadati.
3. Cosmos DB archivia i metadati delle immagini.
4. App per la logica recupera le didascalie delle immagini dall'API Visione artificiale.
5. Azure Active Directory gestisce l'autenticazione degli utenti.

![Diagramma dell'architettura della soluzione](media/functions-first-serverless-web-app/0-architecture.jpg)

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare l'archiviazione BLOB di Azure per ospitare un sito Web statico e le immagini caricate.
> * Caricare le immagini nell'archiviazione BLOB di Azure con Funzioni di Azure.
> * Ridimensionare le immagini con Funzioni di Azure.
> * Archiviare i metadati delle immagini in Azure Cosmos DB.
> * Usare l'API Visione artificiale di Servizi cognitivi per generare automaticamente le didascalie delle immagini.
> * Usare Azure Active Directory per proteggere l'app Web tramite l'autenticazione degli utenti.
