# Descrizione

Codice del capitolo relativo all'utilizzo di Kubernetes in ambito cloud.

> Gli esempi riportati sono da intendersi a scopo illustrativo, mentre tutti i dettagli riguardo il codice e il suo 
funzionamento sono riportati all'interno del libro edito da Apogeo: 
> https://amzn.to/3AMpQj2
-------------------------------------------------------------------------------------------------------------------

Cap 15/AWS/task.json
---------------------------
# DESCRIZIONE
Il codice che hai fornito è una rappresentazione JSON di una definizione di un task di ECS (Amazon Elastic Container Service) per eseguire un container NGINX su un'istanza EC2. Ecco una spiegazione dei principali elementi e alcune informazioni sui comandi relativi:

1. **requiresCompatibilities:**
   - Indica le compatibilità richieste per l'esecuzione del task. In questo caso, richiede la compatibilità con le istanze EC2.

2. **containerDefinitions:**
   - Contiene le informazioni relative al container che verrà eseguito.
   - **name:** Il nome del container è "nginx".
   - **image:** L'immagine Docker utilizzata per il container è "nginx:latest".
   - **memory:** La quantità di memoria allocata per il container è 256 MB.
   - **cpu:** La quantità di risorse CPU allocate per il container è 256 unità.
   - **essential:** Indica se il container è essenziale per il task. Se impostato su "true", il fallimento del container equivale al fallimento del task.
   - **portMappings:** Specifica la mappatura delle porte. In questo caso, la porta 80 del container è mappata sulla porta 80 dell'istanza EC2.
   - **logConfiguration:** Configurazione dei log per il container.
      - **logDriver:** Utilizza il driver di log "awslogs".
      - **options:** Parametri specifici per il driver di log. Qui vengono specificati il gruppo di log, la regione e il prefisso dello stream.

3. **volumes:**
   - Elenco dei volumi da montare nel container. In questo caso, è vuoto, quindi non ci sono volumi specificati.

4. **networkMode:**
   - Specifica la modalità di rete utilizzata dal task. In questo caso, è "bridge", che consente al container di avere il proprio stack di rete isolato.

5. **placementConstraints:**
   - Specifica vincoli sulla posizione in cui il task può essere eseguito. In questo caso, è vuoto, quindi non ci sono vincoli specificati.

6. **family:**
   - Il nome della famiglia del task, che è "nginx".

# COMANDI

Per eseguire questo task su ECS, è possibile utilizzare il comando `ecs-cli`. Assicurati di aver configurato l'`ecs-cli` correttamente prima di eseguire questi comandi.

```bash
# Creare una definizione del task ECS dal file JSON
ecs-cli compose --file task-definition.json create

# Eseguire il task su ECS
ecs-cli compose --file task-definition.json up

# Visualizzare lo stato del task
ecs-cli ps
```

Nota: Assicurati di sostituire "task-definition.json" con il percorso effettivo del tuo file di definizione del task. Inoltre, è necessario avere l'`ecs-cli` installato e configurato con le credenziali appropriate.



Cap 15/AWS/task.yaml
---------------------------
# DESCRIZIONE
Il codice che hai fornito sembra avere un problema di formattazione nei portMappings e logConfiguration. Correggo la formattazione qui:

```yaml
requiresCompatibilities:
  - EC2
containerDefinitions:
  - name: nginx
    image: nginx:latest
    memory: 256
    cpu: 256
    essential: true
    portMappings:
      - containerPort: 80
        protocol: tcp
    logConfiguration:
      logDriver: awslogs
      options:
        awslogs-group: awslogs-nginx-ecs
        awslogs-region: eu-west-1
        awslogs-stream-prefix: nginx
volumes: []
networkMode: bridge
placementConstraints: []
family: nginx
```

Ora, ecco una spiegazione dei principali elementi e alcune informazioni sui comandi relativi:

1. **requiresCompatibilities:**
   - Indica le compatibilità richieste per l'esecuzione del task. In questo caso, richiede la compatibilità con le istanze EC2.

2. **containerDefinitions:**
   - Contiene le informazioni relative al container che verrà eseguito.
   - **name:** Il nome del container è "nginx".
   - **image:** L'immagine Docker utilizzata per il container è "nginx:latest".
   - **memory:** La quantità di memoria allocata per il container è 256 MB.
   - **cpu:** La quantità di risorse CPU allocate per il container è 256 unità.
   - **essential:** Indica se il container è essenziale per il task. Se impostato su "true", il fallimento del container equivale al fallimento del task.
   - **portMappings:** Specifica la mappatura delle porte. In questo caso, la porta 80 del container è mappata sulla porta 80 dell'istanza EC2.
   - **logConfiguration:** Configurazione dei log per il container.
      - **logDriver:** Utilizza il driver di log "awslogs".
      - **options:** Parametri specifici per il driver di log. Qui vengono specificati il gruppo di log, la regione e il prefisso dello stream.

3. **volumes:**
   - Elenco dei volumi da montare nel container. In questo caso, è vuoto, quindi non ci sono volumi specificati.

4. **networkMode:**
   - Specifica la modalità di rete utilizzata dal task. In questo caso, è "bridge", che consente al container di avere il proprio stack di rete isolato.

5. **placementConstraints:**
   - Specifica vincoli sulla posizione in cui il task può essere eseguito. In questo caso, è vuoto, quindi non ci sono vincoli specificati.

6. **family:**
   - Il nome della famiglia del task, che è "nginx".

# COMANDI

Per eseguire questo task su ECS, è possibile utilizzare il comando `ecs-cli`. Assicurati di aver configurato l'`ecs-cli` correttamente prima di eseguire questi comandi.

```bash
# Creare una definizione del task ECS dal file YAML
ecs-cli compose --file task-definition.yaml create

# Eseguire il task su ECS
ecs-cli compose --file task-definition.yaml up

# Visualizzare lo stato del task
ecs-cli ps
```

Assicurati di sostituire "task-definition.yaml" con il percorso effettivo del tuo file di definizione del task. Inoltre, è necessario avere l'`ecs-cli` installato e configurato con le credenziali appropriate.



Cap 15/Azure/Automated deployments - Demo/Dockerfile
----------------------------------------------------
# DESCRIZIONE
Il codice Dockerfile che hai fornito è progettato per creare un'immagine Docker per un'applicazione Python Flask. Di seguito, una spiegazione delle principali istruzioni e un esempio di comandi relativi:

```Dockerfile
# Usa l'immagine di base Python 3.8 su sistema operativo Debian slim
FROM python:3.8-slim-buster

# Etichetta dell'autore o del maintainer dell'immagine
LABEL maintainer="serena.sensini@gmail.com"

# Imposta la directory di lavoro all'interno del container su /app
WORKDIR /app

# Copia il file requirements.txt nella directory corrente del container
COPY requirements.txt requirements.txt

# Esegue l'installazione delle dipendenze definite in requirements.txt
RUN pip install -r requirements.txt

# Copia tutto il contenuto locale nella directory corrente del container (/app)
COPY . .

# Imposta il comando di default per l'immagine quando viene eseguita
ENTRYPOINT ["python"]

# Comandi di default eseguiti quando l'immagine viene avviata
CMD ["-m", "flask", "run", "--host=0.0.0.0"]
```

Spiegazione delle istruzioni:

1. **FROM python:3.8-slim-buster:**
   - Usa come base l'immagine Docker ufficiale di Python versione 3.8 su sistema operativo Debian slim.

2. **LABEL maintainer="serena.sensini@gmail.com":**
   - Aggiunge un'etichetta per indicare il maintainer dell'immagine.

3. **WORKDIR /app:**
   - Imposta la directory di lavoro all'interno del container su `/app`. Le istruzioni successive verranno eseguite in questa directory.

4. **COPY requirements.txt requirements.txt:**
   - Copia il file `requirements.txt` dalla directory locale all'interno del container nella directory corrente (`/app`).

5. **RUN pip install -r requirements.txt:**
   - Esegue il comando `pip install` per installare le dipendenze specificate nel file `requirements.txt`.

6. **COPY . .:**
   - Copia tutti i file dalla directory locale all'interno del container nella directory corrente (`/app`).

7. **ENTRYPOINT ["python"]:**
   - Imposta l'entry point predefinito quando l'immagine viene eseguita, in questo caso, esegue il comando `python`.

8. **CMD ["-m", "flask", "run", "--host=0.0.0.0"]:**
   - Fornisce i comandi di default che verranno eseguiti quando l'immagine viene avviata. In questo caso, avvia l'applicazione Flask con il comando `python -m flask run --host=0.0.0.0`.

# COMANDI

Per costruire e eseguire il container Docker, puoi utilizzare i seguenti comandi:

```bash
# Assicurati di trovarti nella directory del Dockerfile
cd /percorso/del/tuo/Dockerfile

# Costruisci l'immagine Docker
docker build -t nome_immagine:tag .

# Esegui il container
docker run -p 5000:5000 nome_immagine:tag
```

Assicurati di sostituire "nome_immagine" e "tag" con i valori desiderati. Il flag `-p` specifica il mapping delle porte, nel caso specifico la porta 5000 del tuo host alla porta 5000 del container (usata da Flask di default).


Cap 15/Azure/Automated deployments - Demo/aks-workflow-example.yaml
-------------------------------------------------------------------
# DESCRIZIONE
Questo è un file di definizione di un flusso di lavoro GitHub Actions per un'applicazione che coinvolge la creazione e il push di un'immagine Docker in un Azure Container Registry (ACR) e il successivo rilascio su un cluster Azure Kubernetes Service (AKS). Ecco una spiegazione delle principali sezioni e un esempio di comandi correlati:

```yaml
name: aks-workflow-name

on:
  push:
    branches:
      - main
  workflow_dispatch: {}

env:
  ACR_RESOURCE_GROUP: myresourcegroup
  AZURE_CONTAINER_REGISTRY: myacr
  CLUSTER_NAME: my-cluster
  CLUSTER_RESOURCE_GROUP: my-cluster-rg
  CONTAINER_NAME: my-image
  DEPLOYMENT_MANIFEST_PATH: |
    ./deployment.yaml
    ./service.yaml

jobs:
  buildImage:
    permissions:
      contents: read
      id-token: write
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: azure/login@v1.4.3
        name: Azure login
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      - name: Build and push image to ACR
        run: az acr build --image ${{ env.CONTAINER_NAME }}:${{ github.sha }} --registry ${{ env.AZURE_CONTAINER_REGISTRY }} -g ${{ env.ACR_RESOURCE_GROUP }} -f ./Dockerfile ./

  deploy:
    permissions:
      actions: read
      contents: read
      id-token: write
    runs-on: ubuntu-latest
    needs:
      - buildImage
    steps:
      - uses: actions/checkout@v3
      - uses: azure/login@v1.4.3
        name: Azure login
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      - uses: azure/use-kubelogin@v1
        name: Set up kubelogin for non-interactive login
        with:
          kubelogin-version: v0.0.25
      - uses: azure/aks-set-context@v3
        name: Get K8s context
        with:
          admin: "false"
          cluster-name: ${{ env.CLUSTER_NAME }}
          resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
          use-kubelogin: "true"
      - uses: Azure/k8s-deploy@v4
        name: Deploys application
        with:
          action: deploy
          images: ${{ env.AZURE_CONTAINER_REGISTRY }}.azurecr.io/${{ env.CONTAINER_NAME }}:${{ github.sha }}
          manifests: ${{ env.DEPLOYMENT_MANIFEST_PATH }}
          namespace: my-namespace
```

Spiegazione delle sezioni chiave:

1. **on:**
   - Definisce gli eventi che attiveranno il flusso di lavoro. In questo caso, il flusso di lavoro viene attivato su ogni push nel branch `main` e anche manualmente attraverso il trigger `workflow_dispatch`.

2. **env:**
   - Definisce variabili di ambiente che verranno utilizzate nel flusso di lavoro.

3. **jobs:**
   - Contiene una serie di lavori che verranno eseguiti in sequenza o parallelamente.

   - **buildImage:**
     - Crea un'immagine Docker basata su `./Dockerfile` e la carica su Azure Container Registry (ACR).

   - **deploy:**
     - Configura il contesto AKS e rilascia l'applicazione usando le immagini dell'ACR e i manifesti specificati.

# COMANDI

I comandi relativi sono incorporati direttamente nel flusso di lavoro. Tuttavia, alcuni passaggi chiave includono:

- Per il lavoro `buildImage`, vengono utilizzati comandi `az acr build` per costruire e caricare l'immagine Docker in Azure Container Registry.

- Per il lavoro `deploy`, vengono utilizzati i comandi `azure/login`, `azure/use-kubelogin`, `azure/aks-set-context` per autenticarsi, configurare il contesto AKS e impostare le credenziali per il rilascio.

- Infine, `Azure/k8s-deploy` viene utilizzato per il rilascio dell'applicazione su AKS, utilizzando le immagini dell'ACR e i manifesti specificati.

Nota: Assicurati di avere configurate le seguenti variabili d'ambiente nel tuo repository GitHub o nel tuo account:

- `AZURE_CLIENT_ID`
- `AZURE_SUBSCRIPTION_ID`
- `AZURE_TENANT_ID`

Queste credenziali sono necessarie per l'accesso ad Azure e all'AKS.


Cap 15/Azure/Automated deployments - Demo/app.py
------------------------------------------------
# DESCRIZIONE
Il codice Python che hai fornito è un'applicazione Flask di base. Ecco una breve spiegazione del codice e alcuni comandi relativi:

### Codice Flask:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == "__main__":
    app.run(debug=True)
```

**Spiegazione del Codice:**

1. **`from flask import Flask`**: Importa la classe `Flask` dal framework Flask.

2. **`app = Flask(__name__)`**: Crea un'istanza dell'applicazione Flask. Il parametro `__name__` rappresenta il nome del modulo corrente.

3. **`@app.route('/')`**: Un decorator che definisce la route principale ("/") e la funzione associata che sarà chiamata quando qualcuno visita quella route.

4. **`def hello_world():`**: La funzione associata alla route "/". Restituisce la stringa "Hello World!".

5. **`if __name__ == "__main__":`**: Verifica se lo script Python è eseguito direttamente (non importato come modulo in un altro script). In tal caso, avvia il server Flask.

6. **`app.run(debug=True)`**: Avvia il server Flask in modalità di debug. La modalità di debug abilita funzionalità come il riavvio automatico del server quando il codice cambia.

# COMANDI

Per eseguire questa applicazione Flask, segui questi passaggi:

1. **Installazione di Flask:**
   ```bash
   pip install Flask
   ```

2. **Esecuzione dell'applicazione:**
   ```bash
   python nome_del_file.py
   ```

   Assicurati di sostituire `nome_del_file.py` con il nome del tuo file Python.

   Il server Flask dovrebbe ora essere in esecuzione, e puoi aprire il tuo browser e visitare `http://127.0.0.1:5000/` per vedere il messaggio "Hello World!".

   Nota: La porta di default è 5000. Se desideri utilizzare una porta diversa, puoi specificarla come argomento per `app.run()`, ad esempio, `app.run(debug=True, port=8080)`.


Cap 15/Azure/Automated deployments - Demo/deployment.yaml
---------------------------------------------------------
# DESCRIZIONE
Il file YAML che hai fornito è una definizione di un oggetto Deployment in Kubernetes. Ecco una breve spiegazione del codice:

### Definizione di un oggetto Deployment in Kubernetes:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    labels:
        app: flask-app
    name: flask-app
spec:
    replicas: 1
    selector:
        matchLabels:
            app: flask-app
    template:
        metadata:
            labels:
                app: flask-app
        spec:
            containers:
                - env:
                      - name: APP_VERSION
                        value: "1.0.0"
                  image: ssensini/simple-flask-app
                  name: flask-app
                  ports:
                      - containerPort: 5000
                  resources: {}
            restartPolicy: Always
status: {}
```

**Spiegazione del Codice:**

1. **`apiVersion: apps/v1`**: Specifica la versione dell'API di Kubernetes e il tipo di oggetto, che è Deployment in questo caso.

2. **`kind: Deployment`**: Indica che stiamo definendo un oggetto Deployment.

3. **`metadata:`**: Contiene metadati associati all'oggetto, come etichette (`labels`) e il nome del deployment.

4. **`spec:`**: Contiene le specifiche del deployment, come il numero di repliche, il selettore per individuare i pod, e il template per la creazione dei pod.

   - **`replicas: 1`**: Specifica che vogliamo una replica del pod.

   - **`selector:`**: Specifica il selettore che il deployment userà per individuare i pod. In questo caso, corrisponde alle etichette degli oggetti Pod.

   - **`template:`**: Definisce il template per la creazione dei pod.

      - **`metadata:`**: Contiene le etichette associate al pod.

      - **`spec:`**: Contiene la specifica del pod, che include i container all'interno del pod.

         - **`containers:`**: Elenco dei container nel pod.

            - **`env:`**: Variabili d'ambiente per il container.

               - **`- name: APP_VERSION`**: Nome della variabile d'ambiente.
               - **`value: "1.0.0"`**: Valore della variabile d'ambiente.

            - **`image: ssensini/simple-flask-app`**: Specifica l'immagine Docker da utilizzare per il container.

            - **`name: flask-app`**: Nome del container.

            - **`ports:`**: Specifica le porte su cui il container è in ascolto.

               - **`- containerPort: 5000`**: Porta del container su cui l'applicazione Flask sta ascoltando.

            - **`resources: {}`**: Definisce le risorse allocate al container.

         - **`restartPolicy: Always`**: Specifica la politica di riavvio del pod, che è "Always" (riavvia il pod in caso di fallimento).

5. **`status: {}`**: Contiene lo stato attuale del deployment. Inizialmente vuoto.

# COMANDI

Per applicare questo deployment su un cluster Kubernetes, puoi utilizzare il comando `kubectl`:

```bash
kubectl apply -f nome_del_file.yaml
```

Assicurati di sostituire `nome_del_file.yaml` con il nome del tuo file YAML. Questo comando creerà il deployment nel cluster Kubernetes. Puoi monitorare lo stato del deployment utilizzando il comando:

```bash
kubectl get deployments
```

e verificare i pod associati con:

```bash
kubectl get pods
```

Per visualizzare i dettagli del deployment, puoi usare:

```bash
kubectl describe deployment flask-app
```

Ecco invece alcuni comandi aggiuntivi che potresti trovare utili:

### Aggiornare il Deployment:

Se apporti modifiche al file YAML del deployment e vuoi applicare queste modifiche al cluster, puoi utilizzare il comando `kubectl apply` nuovamente:

```bash
kubectl apply -f nome_del_file_modificato.yaml
```

Questo aggiornerà il deployment nel cluster con le nuove configurazioni.

### Visualizzare i Dettagli del Pod:

Puoi ottenere ulteriori dettagli sui pod associati al deployment utilizzando il comando `kubectl describe pod`:

```bash
kubectl describe pod <nome_del_pod>
```

Sostituisci `<nome_del_pod>` con il nome effettivo del pod. Puoi ottenere i nomi dei pod usando `kubectl get pods`.

### Monitorare i Log del Pod:

Puoi visualizzare i log del tuo pod con il comando `kubectl logs`:

```bash
kubectl logs <nome_del_pod>
```

### Scaling del Deployment:

Puoi ridimensionare il numero di repliche del deployment utilizzando il comando `kubectl scale`. Ad esempio, per ridimensionare il deployment a 3 repliche:

```bash
kubectl scale deployment flask-app --replicas=3
```

### Rimuovere il Deployment:

Se vuoi rimuovere il deployment e i pod associati dal cluster, puoi utilizzare il comando `kubectl delete`:

```bash
kubectl delete deployment flask-app
```

Questi sono solo alcuni dei comandi di base che puoi utilizzare per gestire un deployment in Kubernetes. Per ulteriori informazioni, consulta la documentazione ufficiale di Kubernetes e `kubectl`.



Cap 15/Azure/Automated deployments - Demo/service.yaml
------------------------------------------------------
# DESCRIZIONE
Il file YAML che hai fornito è una definizione di un oggetto Service in Kubernetes. Ecco una breve spiegazione del codice:

### Definizione di un oggetto Service in Kubernetes:

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: flask-app
  name: flask-app
spec:
  ports:
    - name: "5000"
      port: 5000
      targetPort: 5000
  selector:
    app: flask-app
  type: LoadBalancer
  externalTrafficPolicy: Cluster
```

**Spiegazione del Codice:**

1. **`apiVersion: v1`**: Specifica la versione dell'API di Kubernetes e il tipo di oggetto, che è Service in questo caso.

2. **`kind: Service`**: Indica che stiamo definendo un oggetto Service.

3. **`metadata:`**: Contiene metadati associati all'oggetto, come etichette (`labels`) e il nome del service.

4. **`spec:`**: Contiene le specifiche del service, come le porte esposte e il selettore per instradare il traffico verso i pod.

   - **`ports:`**: Specifica le porte su cui il service ascolterà.

      - **`name: "5000"`**: Nome della porta.
      - **`port: 5000`**: Porta del service.
      - **`targetPort: 5000`**: Porta su cui i pod mirati dal service stanno ascoltando.

   - **`selector:`**: Specifica il selettore che il service userà per instradare il traffico ai pod corrispondenti.

      - **`app: flask-app`**: Selettore basato su etichetta per instradare il traffico a tutti i pod con l'etichetta `app: flask-app`.

   - **`type: LoadBalancer`**: Specifica il tipo di service. In questo caso, è un service di tipo LoadBalancer, che consente di esporre il service su un indirizzo IP esterno (se supportato dalla tua piattaforma cloud).

   - **`externalTrafficPolicy: Cluster`**: Specifica la politica di traffico esterno. In questo caso, il traffico esterno viene instradato ai nodi del cluster.

# COMANDI

Per applicare questo service su un cluster Kubernetes, puoi utilizzare il comando `kubectl`:

```bash
kubectl apply -f nome_del_file.yaml
```

Assicurati di sostituire `nome_del_file.yaml` con il nome del tuo file YAML. Questo comando creerà il service nel cluster Kubernetes.

Per visualizzare i dettagli del service, puoi utilizzare il comando:

```bash
kubectl get services
```

Per ottenere ulteriori dettagli su un service specifico, puoi utilizzare:

```bash
kubectl describe service flask-app
```

Se stai utilizzando un service di tipo LoadBalancer, il tuo fornitore di cloud potrebbe richiedere qualche istante per allocare un indirizzo IP esterno. Puoi controllare lo stato del service con:

```bash
kubectl get services -w
```

Il flag `-w` indica di attendere e aggiornare continuamente lo stato del service. Una volta ottenuto l'indirizzo IP esterno, puoi accedere alla tua applicazione Flask tramite tale indirizzo IP e la porta specificata nel service.


# STRUTTURA PROGETTO
```bash
Cap15
├───AWS
│   ├───task.json
│   ├───task.yaml
│
├───Azure/Automated deployments - Demo
    ├───Dockerfile
    ├───aks-workflow-example.yaml
    ├───app.py
    ├───deployment.yaml
    ├───pg-deployment.yaml
    ├───requirements.txt
    ├───service.yaml
```

Per far funzionare l'applicazione Flask in un ambiente Azure con un'infrastruttura gestita da Kubernetes (AKS), insieme a un workflow di GitHub Actions per l'automazione delle distribuzioni, segui questi passaggi. Assumerò che tu abbia già configurato un cluster AKS in Azure.

### 1. Configurazione del Cluster AKS in Azure:

Assicurati di avere un cluster AKS configurato e funzionante in Azure. Puoi seguire la documentazione ufficiale di Azure per la creazione di un cluster AKS.

### 2. Configurazione di GitHub Secrets:

Nel tuo repository GitHub, configura le seguenti GitHub Secrets. Queste sono variabili d'ambiente sicure che GitHub Actions può utilizzare durante l'esecuzione dei workflow.

- `AZURE_CLIENT_ID`: L'ID client del tuo servizio Azure AD.
- `AZURE_CLIENT_SECRET`: Il segreto del tuo servizio Azure AD.
- `AZURE_SUBSCRIPTION_ID`: L'ID della tua sottoscrizione Azure.
- `AZURE_TENANT_ID`: L'ID del tuo tenant Azure AD.

### 3. Contenuto dei File:

Assicurati che i file nella struttura della directory siano configurati correttamente:

- **Dockerfile:** Contiene le istruzioni per la creazione dell'immagine Docker per l'app Flask.

- **aks-workflow-example.yaml:** Contiene il flusso di lavoro di GitHub Actions per automatizzare il build dell'immagine e il rilascio su AKS.

- **app.py:** Contiene il codice sorgente dell'applicazione Flask.

- **deployment.yaml:** Contiene la definizione del Deployment per Kubernetes.

- **pg-deployment.yaml:** Se stai usando un database PostgreSQL, contiene la definizione del Deployment per il database.

- **requirements.txt:** Specifica le dipendenze Python del tuo progetto.

- **service.yaml:** Contiene la definizione del Service per Kubernetes.

### 4. Contenuto del File `aks-workflow-example.yaml`:

```yaml
name: Azure AKS Deployment

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v2

    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up kubelogin for non-interactive login
      uses: azure/aks-set-context@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        cluster-name: your-cluster-name
        resource-group: your-resource-group
        use-kubelogin: true

    - name: Build and Push Docker Image
      run: |
        docker build -t your-container-registry.azurecr.io/your-image-name:$GITHUB_SHA .
        docker push your-container-registry.azurecr.io/your-image-name:$GITHUB_SHA

    - name: Deploy to AKS
      uses: azure/k8s-deploy@v1
      with:
        azureCredentials: ${{ secrets.AZURE_CREDENTIALS }}
        azureResourceGroup: your-resource-group
        k8sCluster: your-cluster-name
        namespace: default
        images: |
          your-container-registry.azurecr.io/your-image-name:$GITHUB_SHA
        manifests: |
          ./deployment.yaml
          ./service.yaml
```

Ricorda di sostituire `your-...` con i tuoi valori specifici.

### 5. Esecuzione:

Ora, ogni volta che commetti e spingi sul branch principale, il flusso di lavoro GitHub Actions verrà attivato. Questo eseguirà il build dell'immagine Docker, la push nel tuo Azure Container Registry e il rilascio sull'AKS. Puoi monitorare lo stato del flusso di lavoro GitHub Actions nella tua interfaccia GitHub.
