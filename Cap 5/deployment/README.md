Cap 5/deployment/d-healthcheck.yaml
-----------------------------------
# DESCRIZIONE
Il file d-healthcheck.yaml è un manifesto di configurazione YAML utilizzato per definire un oggetto di tipo Deployment in Kubernetes. 

`liveness probe`: per il container.
> Questo è un meccanismo che Kubernetes userà per verificare se il container è in uno stato sano. Stiamo effettuando una richiesta HTTP all'interno del container al percorso "/healthcheck" e alla porta 8080. Inizieremo a effettuare questa verifica 3 secondi dopo il lancio del container (initialDelaySeconds), e la ripeteremo ogni 3 secondi (periodSeconds). Se la verifica fallisce per 3 volte consecutive (failureThreshold), Kubernetes considererà il container come non sano e potrebbe tentare di riavviarlo o prendere altre misure appropriate.
------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
$ kubectl apply -f d-healthcheck.yaml
$ kubectl get deployments
$ kubectl get pods
```

**Come configurare un cluster?**
Per verificare se riesci a comunicare con il cluster:
```bash
$ kubectl get nodes
```
Se riscontri problemi, verifica il registro degli eventi del cluster Kubernetes per ottenere informazioni dettagliate sugli errori.
```bash
$ kubectl get events
```
***********************************************
Cap 5/deployment/d-initcontainer.yaml
-----------------------------------
# DESCRIZIONE
Questo Deployment crea un pod che esegue un container NGINX con un init container che inizializza un file HTML e lo rende disponibile al container NGINX tramite un volume condiviso. Questo può essere utilizzato per personalizzare il contenuto del pod NGINX durante la sua inizializzazione.

`template`: definisce il modello dei pod che verranno creati da questo Deployment. In questo caso, i pod avranno un'etichetta app: nginx-server.

`volumes`: definisce un volume chiamato "common-disk" con tipo "emptyDir". Questo volume verrà montato sia nell'inizializzazione che nel container principale.

`initContainers`: definisce un inizializzazione (init container) chiamato "busybox". Questo init container utilizza l'immagine "busybox" e monta il volume "common-disk" nella directory "/web-files". Successivamente, esegue un comando per creare un file HTML chiamato "index.html" con il contenuto "\<h2>Init container added this line!\</h2>" all'interno del volume e attende 20 secondi prima di terminare.

`containers`: definisce il container principale chiamato "nginx" con l'immagine NGINX. Anche questo container monta il volume "common-disk," ma lo fa nella directory "/usr/share/nginx/html" all'interno del container NGINX. Questo consente al container NGINX di accedere al file "index.html" creato dall'inizializzazione e servirlo come contenuto HTML.

------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
$ kubectl apply -f nginx-deployment.yaml
$ kubectl get deployments
$ kubectl get pods
```
__Nota__: Aspetta finché il pod non diventa "Running" e il Deployment mostra il numero desiderato di "Replicas".

**Come configurare il cluster?**
Se hai installato Kubernetes manualmente o stai utilizzando Kind, devi configurare kubectl manualmente per connettersi al tuo cluster. 
Per fare ciò, esegui il seguente comando:
```bash
$ kubectl config use-context <nome-del-tuo-cluster>
```
Il nome del contesto del cluster è di solito "default" o il nome specifico che hai dato al tuo cluster durante l'installazione.

Per verificare che kubectl sia configurato correttamente e che sia connesso al tuo cluster, puoi eseguire il comando:
k cluster-info (Questo dovrebbe restituire informazioni sul tuo cluster, tra cui l'indirizzo del server API di Kubernetes)
Kubernetes control plane is running at https://127.0.0.1:16443
CoreDNS is running at https://127.0.0.1:16443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
***********************************************
Cap 5/deployment/d-nginx-1.23.3-annotation.yaml
-----------------------------------
# DESCRIZIONE
Questo file YAML definisce un Deployment chiamato "nginx-deployment" che gestisce tre repliche di un pod con un container basato sull'immagine "nginx:1.23.3" e che espone la porta 80. L'aggiornamento dell'immagine è annotato con il messaggio "Aggiornamento immagine alla versione 1.23.3".

L'annotation `kubernetes.io/change-cause` all'interno del metadata della definizione del tuo Deployment è utilizzata per fornire una breve spiegazione o causa del motivo per cui è stato apportato un cambiamento al Deployment. Questo può essere utile per tenere traccia delle modifiche apportate al Deployment e per comprendere facilmente il motivo delle modifiche.

Nel tuo caso, l'annotation `kubernetes.io/change-cause` specifica il motivo dell'aggiornamento dell'immagine alla versione 1.23.3 del container nginx. L'annotation dice: "Aggiornamento immagine alla versione 1.23.3". Questa annotazione rende più chiare le motivazioni dell'aggiornamento dell'immagine ed è un buon modo per documentare le modifiche apportate ai tuoi oggetti Kubernetes.

Le annotazioni non influiscono direttamente sul comportamento del tuo Deployment, ma sono utili per scopi informativi e documentativi.

------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
Creare il Deployment:
$ kubectl apply -f d-nginx-1.23.3-annotation.yaml
deployment.apps/nginx-deployment created
```

Visualizzare lo stato del Deployment:
```bash
$ kubectl get deployment nginx-deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   0/3     3            0           65s
```

Visualizzare i pod associati al Deployment (L'opzione -l è utilizzata per selezionare i pod in base alle etichette):
```bash
$ kubectl get pods -l app=nginx
NAME                                READY   STATUS             RESTARTS   AGE
nginx-deployment-6596f58687-4w8s6   0/1     ImagePullBackOff   0          3m2s
nginx-deployment-6596f58687-dg956   0/1     ImagePullBackOff   0          3m2s
nginx-deployment-6596f58687-p2vd8   0/1     ErrImagePull       0
```

Aggiornare l'immagine Docker nel Deployment:
```bash
$ kubectl config use-context <nome-del-tuo-cluster>
```
***********************************************
Cap 5/deployment/d-nginx-1.23.3.yaml
-----------------------------------
# DESCRIZIONE
Questa distribuzione crea tre repliche di un contenitore Nginx, ciascuna in ascolto sulla porta 80 e ciascun pod è etichettato con 'app: nginx'.

------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
Applicare la configurazione:
$ kubectl apply -f nginx-deployment.yaml
```

Verifica stato del Deployment creato ed i Pods:
```bash
$ kubectl get deployment nginx-deployment
$ kubectl get pods -l app=nginx
```

**Accedi al servizio Nginx**

Se desideri accedere al servizio Nginx esposto dai pod, potrebbe essere necessario configurare un servizio o un Ingress a seconda della configurazione del cluster. Per impostazione predefinita, la configurazione che hai fornito espone la porta 80 sui pod, ma potresti dover creare un servizio per accedervi esternamente.
Per accedere al servizio Nginx creato nel tuo cluster Kubernetes, puoi farlo seguendo questi passaggi:

1. **Assicurati che il Deployment sia attivo**: Prima di tutto, assicurati che il tuo Deployment Nginx sia attivo e che i pod siano in esecuzione. Puoi verificarlo con il comando:
   ```bash
   kubectl get deployment nginx-deployment
   ```
   Assicurati che il campo "AVAILABLE" abbia un valore diverso da zero.

2. **Crea un Servizio (Service)**: Per accedere ai pod del tuo Deployment, dovrai creare un servizio. Ecco un esempio di come creare un servizio per i tuoi pod Nginx:
   ```bash
   kubectl expose deployment nginx-deployment --type=NodePort --name=nginx-service
   ```
   Questo comando crea un servizio NodePort chiamato "nginx-service," che consente di accedere ai pod tramite una porta specifica sul nodo.

3. **Verifica il Servizio**:
   Per vedere la porta sulla quale il servizio è stato esposto, puoi utilizzare il seguente comando:
   ```bash
   kubectl get service nginx-service
   ```
   Troverai l'informazione nella colonna "PORT(S)".

4. **Accedi al Servizio**:
   Ora puoi accedere al tuo servizio Nginx utilizzando l'indirizzo IP del tuo nodo Kubernetes e la porta esposta dal servizio. Puoi ottenere l'indirizzo IP del nodo con questo comando:
   ```bash
   kubectl get nodes -o wide
   ```
   Trova l'indirizzo IP del nodo su cui vuoi accedere. Supponiamo che l'indirizzo IP sia `NODO_IP` e la porta esposta dal servizio sia `PORTA`.

   Puoi quindi accedere al tuo servizio Nginx nel browser o tramite curl utilizzando l'URL `http://NODO_IP:PORTA`.

Questi sono i passaggi di base per accedere al tuo servizio Nginx su un cluster Kubernetes. Tieni presente che la configurazione del servizio potrebbe variare a seconda del tuo ambiente Kubernetes, ad esempio, se stai utilizzando Minikube o un cluster su un cloud provider.

**In alternativa il Servizio (Service) si può creare per il tuo Deployment Nginx in formato YAML:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx  # Assicurati che corrisponda all'etichetta dei tuoi pod nel Deployment
  ports:
    - protocol: TCP
      port: 80  # La porta del servizio
      targetPort: 80  # La porta a cui i pod Nginx rispondono
  type: NodePort  # Tipo di servizio NodePort
```

Assicurati di salvare questo YAML in un file, ad esempio `nginx-service.yaml`. Poi puoi applicare il servizio al tuo cluster Kubernetes usando il comando `kubectl apply`:
```bash
$ kubectl apply -f nginx-service.yaml
```
Questo creerà un servizio chiamato "nginx-service" che instrada il traffico alla porta 80 dei pod del tuo Deployment Nginx. Il tipo di servizio è NodePort, il che significa che il servizio sarà accessibile tramite la porta NodePort del tuo cluster Kubernetes.

Assicurati che l'etichetta "app: nginx" nei tuoi pod del Deployment corrisponda all'etichetta specificata nella sezione "selector" del servizio in modo che i pod giusti siano presi in considerazione dal servizio.
***********************************************
Cap 5/deployment/d-nginx-latest.yaml
-----------------------------------
# DESCRIZIONE
Questo è un esempio di un file di configurazione di Kubernetes in formato YAML per creare un oggetto Deployment che gestisce un'applicazione web Nginx.
Quando questo file di configurazione viene applicato a un cluster Kubernetes, verranno create tre repliche di pod Nginx, ciascuna in esecuzione nell'immagine Nginx:latest e esposte sulla porta 80. Il Deployment si assicurerà che il numero desiderato di repliche sia sempre in esecuzione e gestirà eventuali aggiornamenti o ridimensionamenti.

------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
$ kubectl apply -f d-nginx-latest.yaml
deployment.apps/nginx-deployment created

$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           22s
(Dovresti vedere il tuo Deployment "nginx-deployment" elencato e potresti vedere che le repliche stanno passando dallo stato "0/3" al "3/3", il che significa che il Deployment sta creando tre repliche di pod Nginx)

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS      AGE
nginx-deployment-57d84f57dc-p78l6   1/1     Running   0             31s
nginx-deployment-57d84f57dc-7mg6r   1/1     Running   0             31s
nginx-deployment-57d84f57dc-j5ktc   1/1     Running   0             3
(Dovresti vedere tre pod in esecuzione con nomi simili a "nginx-deployment-xxxxx-xxxxx.")
```

Ora, puoi esporre il tuo Deployment come un servizio per accedere all'applicazione Nginx.
Ecco un esempio di come esporre il Deployment come servizio di tipo LoadBalancer:
```bash
$ kubectl expose deployment nginx-deployment --type=LoadBalancer --name=nginx-service
service/nginx-service exposed
```

Dopo aver esposto il servizio, puoi ottenere l'indirizzo IP del servizio LoadBalancer utilizzando il comando:
```bash
$ kubectl get svc nginx-service
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx-service   LoadBalancer   10.152.183.89   <pending>     80:31680/TCP   65s
(Ora puoi utilizzare l'indirizzo IP del servizio per accedere all'applicazione Nginx in esecuzione nei pod)
```
***********************************************
Cap 5/deployment/d-nginx-recreate.yaml
-----------------------------------
# DESCRIZIONE
Questo file di definizione del Deployment crea un controller che gestisce l'aggiornamento di un'applicazione basata su un'immagine di Nginx, assicurandosi che ci siano sempre 3 repliche dell'applicazione in esecuzione. Quando viene effettuato un aggiornamento, i pod esistenti vengono terminati e sostituiti con nuovi pod basati sull'immagine "nginx:1.23.3".

> strategy: Specifica la strategia di aggiornamento per il Deployment. In questo caso, la strategia è "Recreate". Ciò significa che quando viene effettuato un aggiornamento dell'applicazione, i pod esistenti vengono terminati e nuovi pod vengono creati con la nuova versione dell'immagine. Questo approccio può causare un breve periodo di inattività durante l'aggiornamento.
------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
$ kubectl apply -f d-nginx-recreate.yaml

$ kubectl get deployments

$ kubectl get pods
```

Per eseguire l'applicazione Nginx, puoi creare un servizio che espone l'applicazione all'esterno del cluster. Ad esempio, puoi creare un servizio di tipo "NodePort" per rendere Nginx accessibile su una porta specifica:
```bash
$ kubectl expose deployment nginx-deployment --type=NodePort --name=nginx-service
```

Ora puoi ottenere il numero di porta assegnato al servizio NodePort:
```bash
$ kubectl get svc nginx-service
```

Per avere un'applicazione Nginx in esecuzione all'interno del tuo cluster Kubernetes gestita dal Deployment,
accedi a Nginx utilizzando l'IP del tuo nodo Kubernetes e la porta NodePort assegnata:
```bash
$ kubectl get nodes -o wide (per l'IP del nodo - vedi INTERNAL-IP)
$ kubectl get svc nginx-service (per la porta associata)

Ad esempio, se il tuo nodo ha l'indirizzo IP 192.168.1.100 e la porta NodePort è 30000, 
puoi accedere a Nginx nel tuo browser utilizzando http://192.168.1.100:30000.

Puoi successivamente aggiornare l'immagine Nginx nel tuo file d-nginx-recreate.yaml e applicare 
l'aggiornamento utilizzando:
$ kubectl apply -f d-nginx-recreate.yaml
```

Tieni presente che il numero di porta NodePort potrebbe essere diverso ogni volta che esegui l'esposizione 
del servizio. 
Se desideri assegnare una porta specifica, puoi specificarla nel comando "kubectl expose deployment" utilizzando il flag --port. 
In questo modo, puoi assegnare manualmente la porta NodePort a 30000, ad esempio, invece di ottenere una porta casuale. Ad esempio:
```bash
$ kubectl expose deployment nginx-deployment --type=NodePort --name=nginx-service --port=80 --target-port=80 --node-port=30000
```

Per cancellare un servizio in un cluster Kubernetes:
```bash
$ kubectl delete service <nome-del-servizio>
```
Puoi anche utilizzare il flag --namespace per specificare il namespace in cui si trova il servizio se non è nel namespace predefinito --> k delete service my-service --namespace my-namespace
***********************************************
Cap 5/deployment/d-nginx-rolling.yaml
-----------------------------------
# DESCRIZIONE
Un file di configurazione in formato YAML utilizzato per definire un oggetto Kubernetes di tipo "Deployment". Questo oggetto è utilizzato per gestire il rilascio e la gestione delle applicazioni in un cluster Kubernetes.

> strategy: Specifica la strategia di aggiornamento. In questo caso, è impostata su "RollingUpdate", il che significa che Kubernetes eseguirà un aggiornamento graduale delle repliche durante il roll-out.
> rollingUpdate: Questo campo contiene ulteriori informazioni sulla strategia di aggiornamento. maxSurge specifica il numero massimo di nuovi pod in eccesso rispetto al numero desiderato di repliche durante l'aggiornamento. maxUnavailable specifica il numero massimo di pod che possono essere non disponibili durante l'aggiornamento.

------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Verrà creato il Deployment, e inizierà a gestire il rilascio dell'applicazione. Inizierà con tre repliche dell'app Nginx:
```bash
$ kubectl create -f d-nginx-rolling.yaml
$ kubectl get deployments
$ kubectl get pods
```

Ora puoi esporre il tuo servizio in modo che sia accessibile dall'esterno. 
Puoi farlo creando un servizio di tipo LoadBalancer o NodePort o utilizzando un Ingress Controller, a seconda delle esigenze del tuo ambiente.

Ecco come puoi esporre il tuo servizio in modo che sia accessibile dall'esterno in Kubernetes utilizzando diversi tipi di servizi:

1. **NodePort**:
   Un servizio di tipo NodePort espone il servizio su una porta specifica su ciascun nodo del cluster. È utile quando desideri accedere al servizio da fuori il cluster. Ecco come puoi crearlo:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-service
   spec:
     selector:
       app: nginx
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
     type: NodePort
   ```
   Puoi creare il servizio con il comando `kubectl create -f nginx-service.yaml`. Successivamente, puoi trovare la porta assegnata eseguendo `kubectl get svc nginx-service`. Ora il tuo servizio Nginx sarà accessibile su ciascun nodo del cluster sulla porta specificata.

2. **LoadBalancer**:
   Un servizio di tipo LoadBalancer è utile quando si è in un ambiente cloud (come AWS, GCP o Azure) e si desidera un bilanciamento del carico dedicato. In questo caso, il bilanciatore di carico cloud assegnerà un indirizzo IP pubblico al servizio. Ecco un esempio:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-service-lb
   spec:
     selector:
       app: nginx
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
     type: LoadBalancer
   ```
   Puoi creare il servizio con il comando `kubectl create -f nginx-service-lb.yaml`. Il bilanciatore di carico cloud assegnerà un indirizzo IP pubblico per accedere al servizio Nginx.

3. **Ingress Controller**:
   Un'alternativa è utilizzare un Ingress Controller, che consente di configurare regole di routing per indirizzare il traffico HTTP e HTTPS ai servizi interni. Ecco un esempio:
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: nginx-ingress
   spec:
     rules:
       - host: example.com
         http:
           paths:
             - path: /
               pathType: Prefix
               backend:
                 service:
                   name: nginx-service
                   port:
                     number: 80
   ```
   Questo esempio configura un Ingress per reindirizzare il traffico in ingresso da `example.com` al servizio `nginx-service`. Per utilizzare un Ingress Controller, dovresti installare e configurare un controller specifico per il tuo ambiente.

__Nota__: Ricorda che la scelta del tipo di servizio da utilizzare dipende dalle esigenze del tuo ambiente e dalle funzionalità offerte dal tuo provider cloud o dal tuo Ingress Controller.
***********************************************
Cap 5/deployment/d-requests-limits.yaml
-----------------------------------
# DESCRIZIONE
Questo Deployment crea tre repliche dell'applicazione "nginx", ognuna con due container (container1 e container2) che richiedono e hanno limiti specifici per la memoria e la CPU. Le repliche sono associate all'etichetta "app: nginx" e sono gestite dal Deployment, il che significa che se una replica dovesse fallire o essere terminata, il Deployment ne creerà automaticamente una nuova per garantire che il numero specificato di repliche rimanga attivo nel cluster Kubernetes.

> image: L'immagine Docker da utilizzare per il container. In questo caso, entrambi i container utilizzano l'immagine "busybox".
> resources: Questa sezione definisce le risorse di cui il container ha bisogno.
> requests: Specifica le richieste di risorse minime per il container. Ad esempio, "memory: 2Gi" significa che il container richiede almeno 2 gigabyte di memoria e "cpu: 500m" significa che richiede almeno 500 milliCPU (mezzo core).
> limits: Specifica i limiti massimi di risorse che il container può utilizzare. Ad esempio, "memory: 4Gi" significa che il container può utilizzare al massimo 4 gigabyte di memoria e "cpu: 800m" significa che può utilizzare al massimo 800 milliCPU.
------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO
Applicazione e verifica del Deployment:
```bash
$ kubectl apply -f d-requests-limits.yaml
$ kubectl get deployments
$ kubectl get pods
```

Per ottenere ulteriori dettagli sul tuo Deployment:
```bash
$ kubectl describe deployment nginx-deployment
```

Per accedere ai pod, puoi eseguire un comando kubectl exec per eseguire comandi all'interno del container:
```bash
$ kubectl exec -it <pod-name> -c container1 -- /bin/sh
```
Questo ti consente di entrare nel container "container1" in uno dei pod e interagire con esso.
