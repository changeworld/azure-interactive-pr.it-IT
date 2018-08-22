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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079460"
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