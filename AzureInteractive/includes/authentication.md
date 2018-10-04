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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460042"
---
<span data-ttu-id="a0141-103">L'autenticazione del servizio app di Azure abilita il supporto dell'autenticazione chiavi in mano in un'app per le funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="a0141-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="a0141-104">Si integra perfettamente con Facebook, Twitter, account Microsoft, Google e Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0141-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="a0141-105">È possibile aggiungere l'autenticazione del servizio app per proteggere le API back-end dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="a0141-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="a0141-106">Abilitare l'autenticazione del servizio app</span><span class="sxs-lookup"><span data-stu-id="a0141-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="a0141-107">Aprire l'app per le funzioni nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="a0141-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="a0141-108">In **Funzionalità della piattaforma** selezionare **Autenticazione/Autorizzazione**.</span><span class="sxs-lookup"><span data-stu-id="a0141-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Selezionare Autenticazione/Autorizzazione](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="a0141-110">Selezionare i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0141-110">Select the following values:</span></span>
    
    | <span data-ttu-id="a0141-111">Impostazione</span><span class="sxs-lookup"><span data-stu-id="a0141-111">Setting</span></span>      |  <span data-ttu-id="a0141-112">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="a0141-112">Suggested value</span></span>   | <span data-ttu-id="a0141-113">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="a0141-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="a0141-114">**Autenticazione servizio app**</span><span class="sxs-lookup"><span data-stu-id="a0141-114">**App Service Authentication**</span></span> | <span data-ttu-id="a0141-115">Attivato</span><span class="sxs-lookup"><span data-stu-id="a0141-115">On</span></span> | <span data-ttu-id="a0141-116">Abilita l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0141-116">Enable authentication.</span></span> |
    | <span data-ttu-id="a0141-117">**Azione da eseguire quando la richiesta non è autenticata**</span><span class="sxs-lookup"><span data-stu-id="a0141-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="a0141-118">Accedi con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a0141-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="a0141-119">Selezionare un metodo di autenticazione configurato (sotto).</span><span class="sxs-lookup"><span data-stu-id="a0141-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="a0141-120">**Provider di autenticazione**</span><span class="sxs-lookup"><span data-stu-id="a0141-120">**Authentication Providers**</span></span> | <span data-ttu-id="a0141-121">Vedere di seguito</span><span class="sxs-lookup"><span data-stu-id="a0141-121">See below</span></span> | <span data-ttu-id="a0141-122">Vedere di seguito</span><span class="sxs-lookup"><span data-stu-id="a0141-122">See below</span></span> |
    | <span data-ttu-id="a0141-123">**Archivio token**</span><span class="sxs-lookup"><span data-stu-id="a0141-123">**Token store**</span></span> | <span data-ttu-id="a0141-124">Attivato</span><span class="sxs-lookup"><span data-stu-id="a0141-124">On</span></span> | <span data-ttu-id="a0141-125">Consente al servizio app di archiviare e gestire i token.</span><span class="sxs-lookup"><span data-stu-id="a0141-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="a0141-126">**URL di reindirizzamento esterni consentiti**</span><span class="sxs-lookup"><span data-stu-id="a0141-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="a0141-127">URL dell'applicazione, ad esempio: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="a0141-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="a0141-128">URL a cui può essere reindirizzato il servizio app dopo l'autenticazione di un utente.</span><span class="sxs-lookup"><span data-stu-id="a0141-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="a0141-129">Selezionare **Azure Active Directory** per visualizzare **Impostazioni di Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="a0141-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="a0141-130">Selezionare **Rapida** come **Modalità di gestione** e inserire le informazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="a0141-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="a0141-131">Impostazione</span><span class="sxs-lookup"><span data-stu-id="a0141-131">Setting</span></span>      |  <span data-ttu-id="a0141-132">Valore consigliato</span><span class="sxs-lookup"><span data-stu-id="a0141-132">Suggested value</span></span>   | <span data-ttu-id="a0141-133">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="a0141-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="a0141-134">**Modalità di gestione**</span><span class="sxs-lookup"><span data-stu-id="a0141-134">**Management mode**</span></span> | <span data-ttu-id="a0141-135">Rapida, Crea nuova app AD</span><span class="sxs-lookup"><span data-stu-id="a0141-135">Express, Create new AD app</span></span> | <span data-ttu-id="a0141-136">Configura automaticamente un'entità servizio e l'autenticazione di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0141-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="a0141-137">**Creare un'app**</span><span class="sxs-lookup"><span data-stu-id="a0141-137">**Create app**</span></span> | <span data-ttu-id="a0141-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="a0141-138">my-serverless-webapp</span></span> | <span data-ttu-id="a0141-139">Immettere un nome applicazione univoco.</span><span class="sxs-lookup"><span data-stu-id="a0141-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="a0141-140">Fare clic su **OK** per salvare le impostazioni di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0141-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Impostazioni di Autenticazione/Autorizzazione e Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="a0141-142">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="a0141-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="a0141-143">Modificare l'app Web per abilitare l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="a0141-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="a0141-144">In Cloud Shell assicurarsi che la directory corrente sia la cartella **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="a0141-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="a0141-145">Per abilitare l'autenticazione nell'app per le funzioni, aggiungere la riga di codice seguente a **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="a0141-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="a0141-146">Effettuare la modifica e visualizzare il risultato eseguendo i comandi seguenti o usando un editor da riga di comando come VIM.</span><span class="sxs-lookup"><span data-stu-id="a0141-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="a0141-147">Verificare che la modifica sia stata effettuata nel file.</span><span class="sxs-lookup"><span data-stu-id="a0141-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="a0141-148">Caricare il file nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="a0141-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="a0141-149">Test dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="a0141-149">Test the application</span></span>

1. <span data-ttu-id="a0141-150">Aprire l'applicazione in un browser.</span><span class="sxs-lookup"><span data-stu-id="a0141-150">Open the application in a browser.</span></span> <span data-ttu-id="a0141-151">Fare clic su **Accedi** ed eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="a0141-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="a0141-152">Selezionare un file di immagine e caricarlo.</span><span class="sxs-lookup"><span data-stu-id="a0141-152">Select an image file and upload it.</span></span>

    ![Pagina di accesso](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="a0141-154">Summary</span><span class="sxs-lookup"><span data-stu-id="a0141-154">Summary</span></span>

<span data-ttu-id="a0141-155">In questo modulo è stato descritto come aggiungere l'autenticazione all'applicazione con l'autenticazione del servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="a0141-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
