<span data-ttu-id="6aae6-101">Archiviazione BLOB di Azure è un servizio economico e altamente scalabile che può essere usato per ospitare file statici.</span><span class="sxs-lookup"><span data-stu-id="6aae6-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="6aae6-102">Per questa esercitazione è possibile usarlo per gestire il contenuto statico (ad esempio HTML, JavaScript, CSS) per l'app Web che si compila.</span><span class="sxs-lookup"><span data-stu-id="6aae6-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="6aae6-103">Creare un account di archiviazione</span><span class="sxs-lookup"><span data-stu-id="6aae6-103">Create a Storage account</span></span>

<span data-ttu-id="6aae6-104">Un account di archiviazione è una risorsa di Azure che consente di archiviare tabelle, code, file, BLOB (oggetti) e dischi delle macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="6aae6-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="6aae6-105">Accedere a Cloud Shell (Bash) selezionando il pulsante **Enter focus mode** (Accedi a modalità messa a fuoco).</span><span class="sxs-lookup"><span data-stu-id="6aae6-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="6aae6-106">Questo pulsante si trova nella parte inferiore della pagina o in alto a destra, a seconda dell'ampiezza della finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="6aae6-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="6aae6-107">La modalità messa a fuoco ancora una finestra di Cloud Shell sul lato destro della finestra del browser, in modo da potere eseguire con facilità i comandi illustrati nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="6aae6-108">Un gruppo di risorse di Azure è un contenitore con risorse di Azure correlate per facilitare la gestione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="6aae6-109">Creare un nuovo gruppo di risorse denominato **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="6aae6-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="6aae6-110">Il contenuto statico (file HTML, CSS e JavaScript) per questa esercitazione è ospitato nell'archiviazione BLOB.</span><span class="sxs-lookup"><span data-stu-id="6aae6-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="6aae6-111">L'archiviazione BLOB richiede un account di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="6aae6-112">Creare un account di archiviazione (utilizzo generico V2) nel gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="6aae6-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="6aae6-113">Sostituire `<storage account name>` con un nome univoco.</span><span class="sxs-lookup"><span data-stu-id="6aae6-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="6aae6-114">Usando la barra di ricerca nella parte superiore del [portale di Azure](https://portal.azure.com), trovare l'account di archiviazione appena creato e aprirlo.</span><span class="sxs-lookup"><span data-stu-id="6aae6-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="6aae6-115">Nel riquadro di spostamento a sinistra selezionare **Sito Web statico (anteprima)** per configurare un contenitore per l'hosting di siti Web statici.</span><span class="sxs-lookup"><span data-stu-id="6aae6-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="6aae6-116">Selezionare **Abilitato** per abilitare il sito Web statico.</span><span class="sxs-lookup"><span data-stu-id="6aae6-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="6aae6-117">Immettere *index.html* come nome del documento di indice.</span><span class="sxs-lookup"><span data-stu-id="6aae6-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="6aae6-118">Il campo include già *index.html* in un tipo di carattere di colore grigio, ma questo è solo un testo di esempio; sarà comunque necessario immettere tale valore nel campo.</span><span class="sxs-lookup"><span data-stu-id="6aae6-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="6aae6-119">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="6aae6-119">Click **Save**.</span></span>
    
    ![Immettere le impostazioni del sito Web statico](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="6aae6-121">Salvare l'**Endpoint primario** in una posizione da cui poterlo copiare facilmente durante l'esecuzione dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="6aae6-122">Si tratta dell'URL dell'applicazione Web.</span><span class="sxs-lookup"><span data-stu-id="6aae6-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="6aae6-123">Caricare l'applicazione Web</span><span class="sxs-lookup"><span data-stu-id="6aae6-123">Upload the web application</span></span>

1. <span data-ttu-id="6aae6-124">I file di origine per l'applicazione che si compila in questa esercitazione si trovano in un [repository GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="6aae6-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="6aae6-125">Assicurarsi di trovarsi all'interno della home directory in Cloud Shell e clonare questo repository.</span><span class="sxs-lookup"><span data-stu-id="6aae6-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="6aae6-126">Il repository viene clonato in `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="6aae6-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="6aae6-127">L'applicazione Web sul lato client si trova nella cartella **www** e viene compilata con il framework JavaScript Vue.js.</span><span class="sxs-lookup"><span data-stu-id="6aae6-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="6aae6-128">Passare a tale cartella ed eseguire i comandi npm per installare le dipendenze dell'applicazione e compilare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="6aae6-129">Il completamento dell'ultimo di questi comandi potrebbe richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="6aae6-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="6aae6-130">L'applicazione viene generata nella cartella **dist**.</span><span class="sxs-lookup"><span data-stu-id="6aae6-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="6aae6-131">Passare dalla directory corrente a **dist** e caricare l'applicazione nel contenitore BLOB **$web**.</span><span class="sxs-lookup"><span data-stu-id="6aae6-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="6aae6-132">Aprire l'URL dell'endpoint primario dei siti Web statici di archiviazione in un Web browser per visualizzare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Home page della prima app Web senza server](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="6aae6-134">Summary</span><span class="sxs-lookup"><span data-stu-id="6aae6-134">Summary</span></span>

<span data-ttu-id="6aae6-135">In questo modulo è stato creato un gruppo di risorse denominato **first-serverless-app** contenente un account di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="6aae6-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="6aae6-136">Un contenitore BLOB denominato **$web** nell'account di archiviazione archivia il contenuto statico per l'applicazione Web e rende il contenuto disponibile pubblicamente.</span><span class="sxs-lookup"><span data-stu-id="6aae6-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="6aae6-137">Si vedrà ora come usare una funzione senza server per caricare immagini nell'archiviazione BLOB da questa applicazione Web.</span><span class="sxs-lookup"><span data-stu-id="6aae6-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
