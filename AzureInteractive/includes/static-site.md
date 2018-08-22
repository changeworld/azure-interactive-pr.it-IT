Archiviazione BLOB di Azure è un servizio economico e altamente scalabile che può essere usato per ospitare file statici. Per questa esercitazione è possibile usarlo per gestire il contenuto statico (ad esempio HTML, JavaScript, CSS) per l'app Web che si compila.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione è una risorsa di Azure che consente di archiviare tabelle, code, file, BLOB (oggetti) e dischi delle macchine virtuali.

1. Accedere a Cloud Shell (Bash) selezionando il pulsante **Enter focus mode** (Accedi a modalità messa a fuoco). Questo pulsante si trova nella parte inferiore della pagina o in alto a destra, a seconda dell'ampiezza della finestra del browser. La modalità messa a fuoco ancora una finestra di Cloud Shell sul lato destro della finestra del browser, in modo da potere eseguire con facilità i comandi illustrati nell'esercitazione.

1. Un gruppo di risorse di Azure è un contenitore con risorse di Azure correlate per facilitare la gestione. Creare un nuovo gruppo di risorse denominato **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. Il contenuto statico (file HTML, CSS e JavaScript) per questa esercitazione è ospitato nell'archiviazione BLOB. L'archiviazione BLOB richiede un account di archiviazione. Creare un account di archiviazione (utilizzo generico V2) nel gruppo di risorse. Sostituire `<storage account name>` con un nome univoco.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Usando la barra di ricerca nella parte superiore del [portale di Azure](https://portal.azure.com), trovare l'account di archiviazione appena creato e aprirlo.

1. Nel riquadro di spostamento a sinistra selezionare **Sito Web statico (anteprima)** per configurare un contenitore per l'hosting di siti Web statici.
    - Selezionare **Abilitato** per abilitare il sito Web statico.
    - Immettere *index.html* come nome del documento di indice. Il campo include già *index.html* in un tipo di carattere di colore grigio, ma questo è solo un testo di esempio; sarà comunque necessario immettere tale valore nel campo.
    - Fare clic su **Save**.
    
    ![Immettere le impostazioni del sito Web statico](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Salvare l'**Endpoint primario** in una posizione da cui poterlo copiare facilmente durante l'esecuzione dell'esercitazione. Si tratta dell'URL dell'applicazione Web.

## <a name="upload-the-web-application"></a>Caricare l'applicazione Web

1. I file di origine per l'applicazione che si compila in questa esercitazione si trovano in un [repository GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). Assicurarsi di trovarsi all'interno della home directory in Cloud Shell e clonare questo repository.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    Il repository viene clonato in `/home/<username>/functions-first-serverless-web-application`.

1. L'applicazione Web sul lato client si trova nella cartella **www** e viene compilata con il framework JavaScript Vue.js. Passare a tale cartella ed eseguire i comandi npm per installare le dipendenze dell'applicazione e compilare l'applicazione. Il completamento dell'ultimo di questi comandi potrebbe richiedere alcuni minuti.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    L'applicazione viene generata nella cartella **dist**.

1. Passare dalla directory corrente a **dist** e caricare l'applicazione nel contenitore BLOB **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Aprire l'URL dell'endpoint primario dei siti Web statici di archiviazione in un Web browser per visualizzare l'applicazione.

    ![Home page della prima app Web senza server](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Summary

In questo modulo è stato creato un gruppo di risorse denominato **first-serverless-app** contenente un account di archiviazione. Un contenitore BLOB denominato **$web** nell'account di archiviazione archivia il contenuto statico per l'applicazione Web e rende il contenuto disponibile pubblicamente. Si vedrà ora come usare una funzione senza server per caricare immagini nell'archiviazione BLOB da questa applicazione Web.
