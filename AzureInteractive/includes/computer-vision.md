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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079495"
---
A questo punto, l'applicazione è una raccolta funzionale che consente di caricare e visualizzare le immagini. In questo modulo viene illustrato come usare l'API Visione artificiale di Servizi cognitivi Microsoft per generare le didascalie per le immagini caricate e salvare le didascalie con i metadati delle immagini in Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Creare un account di Visione artificiale

Servizi cognitivi Microsoft è una raccolta di servizi che consentono agli sviluppatori di rendere le applicazioni più intelligenti. L'API Visione artificiale è un servizio senza server che elabora le immagini usando algoritmi avanzati e restituisce informazioni su ogni immagine. Include un livello gratuito che offre fino a 5.000 chiamate API al mese.

1. Verificare di essere ancora connessi a Cloud Shell. In caso contrario, selezionare **Enter focus mode** (Accedi a modalità messa a fuoco) per aprire una finestra di Cloud Shell. 

1. Creare un nuovo account di Servizi cognitivi di tipo **ComputerVision** con un nome univoco nel gruppo di risorse. Per il livello gratuito usare **F0** come SKU. Se si ha già un account esistente di Visione artificiale, potrebbe essere necessario creare un account Standard (S1); tuttavia, ciò può comportare alcuni costi.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Creare le impostazioni dell'app per le funzioni per l'URL e la chiave di Visione artificiale

Per chiamare l'API Visione artificiale, sono necessari un URL e una chiave.

1. Ottenere l'URL e la chiave API Visione artificiale e salvarli nelle variabili bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Creare le impostazioni dell'app denominate rispettivamente **COMP_VISION_KEY** e **COMP_VISION_URL** nell'app per le funzioni.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Chiamare l'API Visione artificiale dalla funzione ResizeImage

Nei passaggi seguenti si modifica la funzione **ResizeImage** per chiamare l'API Visione artificiale per descrivere ogni immagine caricata e salvare la descrizione in Cosmos DB.

1. Aprire l'app per le funzioni nel portale di Azure.

1. **C#**

    1. (C#) Usare il riquadro di spostamento a sinistra per trovare la funzione **ResizeImage** e aprire la finestra del codice.

    1. (C#) Sostituire il codice con il contenuto di [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Questo codice usa `HttpClient` per chiamare l'API Visione artificiale e salvare il risultato in Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Questa funzione richiede il pacchetto `axios` di npm per effettuare una chiamata HTTP all'API Visione artificiale. Per installare il pacchetto npm, fare clic sul nome dell'app per le funzioni nel riquadro di spostamento di sinistra e fare clic su **Funzionalità della piattaforma**.

    1. (JavaScript) Fare clic su **Console** per visualizzare una finestra della console.

    1. (JavaScript) Eseguire il comando `npm install axios` nella console. Il completamento dell'operazione potrebbe richiedere un minuto o due.

    1. (JavaScript) Fare clic sul nome della funzione (**ResizeImage**) nel riquadro di spostamento di sinistra per visualizzare la funzione e quindi sostituire tutto il contenuto del file **index.js** con il contenuto di [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Fare clic su **Log** sotto la finestra del codice per espandere il pannello dei log.

1. Fare clic su **Save**. Verificare nel pannello dei log che la funzione sia stata salvata correttamente e che non vi siano errori.


## <a name="test-the-application"></a>Test dell'applicazione

1. Aprire l'applicazione in un browser. Selezionare un file di immagine e caricarlo.

1. Dopo alcuni secondi, nella pagina verrà visualizzata l'anteprima della nuova immagine. Passare il mouse sull'immagine per visualizzare la descrizione generata da Visione artificiale.


## <a name="summary"></a>Summary

In questo modulo è stato descritto come generare automaticamente le didascalie per ogni immagine caricata usando l'API Visione artificiale di Servizi cognitivi Microsoft. Si vedrà ora come aggiungere l'autenticazione all'applicazione con l'autenticazione del servizio app di Azure.