# Descrizione

Codice del capitolo relativo alle risorse di configurazione, come ConfigMap e Secret. 

> I dettagli riguardo il codice e il suo funzionamento sono riportati all'interno del libro edito da Apogeo: 
> https://amzn.to/3AMpQj2
----------------------------------------------------------------------------------------------------------------------------
# CONFIGMAP
Cap 6/configmap.yaml
------------------------------------------------------
```yaml
apiVersion: v1
data:
  TIMEZONE: "IT"
kind: ConfigMap
metadata:
  name: myconfigmap
```
Questo specifico oggetto è una ConfigMap con il nome "myconfigmap" e contiene una variabile chiamata "TIMEZONE" impostata su "IT". 

Per ottenere ulteriori informazioni sull'oggetto ConfigMap "myconfigmap" in Kubernetes:
```bash
kubectl describe configmap myconfigmap
```

OUTPUT
```bash
Name:         myconfigmap
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
TIMEZONE:
----
IT

BinaryData
====

Events:  <none>
```

Cap 6/configmap-for-file.yaml
-------------------------------------------
```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: myconfigmap-files
data:
  locale.properties: |-
    # ###################################################################
    # Locale properties compliant with the standard Java i18n mechanism
    #
    # see https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html
    # for extensive explanations
    #
    # the file provides default empty values for each property  
    # ###################################################################
    
    language = @locale.properties.language@
    
    country = @locale.properties.country@
    
    variant = @locale.properties.variant@

Per ottenere ulteriori informazioni sull'oggetto ConfigMap "myconfigmap-files" in Kubernetes:
```bash
kubectl describe configmap myconfigmap-files
```
OUTPUT
```bash
Name:         myconfigmap-files
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
locale.properties:
----
# ###################################################################
# Locale properties compliant with the standard Java i18n mechanism
#
# see https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html
# for extensive explanations
#
# the file provides default empty values for each property
# ###################################################################

language = @locale.properties.language@

country = @locale.properties.country@

variant = @locale.properties.variant@

BinaryData
====

Events:  <none>
```

**Aggiorno il file prec. inserendo il file lorotate.conf:**
Cap 6/configmap-for-file2.yaml
------------------------------
```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: myconfigmap-files
data:
  logrotate.conf: |-
    # see "man logrotate" for details
    # rotate log files weekly
      weekly
      
      # use the adm group by default, since this is the owning group
      # of /var/log/syslog.
      su root adm
      
      # keep 4 weeks worth of backlogs
      rotate 4
      
      # create new (empty) log files after rotating old ones
      create
      
      # use date as a suffix of the rotated file
      #dateext
      
      # uncomment this if you want your log files compressed
      #compress
      
      # packages drop log rotation information into this directory
      include /etc/logrotate.d
    
    # system-specific logs may be also be configured here.
```
Il frammento di codice YAML rappresenta un oggetto di configurazione Kubernetes di tipo ConfigMap chiamato "myconfigmap-files". Questo ConfigMap contiene un campo "data" che contiene un file di configurazione denominato "logrotate.conf". 

Il contenuto di "logrotate.conf" è formattato come un documento di configurazione Logrotate. Logrotate è uno strumento utilizzato per la gestione dei log in sistemi Linux. Nel tuo caso, il file di configurazione definisce diverse opzioni per la rotazione dei log, tra cui:

- La rotazione dei log settimanale (`weekly`).
- L'uso del gruppo "adm" (`su root adm`) come gruppo predefinito.
- La conservazione delle ultime 4 settimane di log (`rotate 4`).
- La creazione di nuovi file di log vuoti dopo la rotazione (`create`).
- La possibile compressione dei file di log (`compress`).
- L'inclusione di altre configurazioni di rotazione dei log dalla directory `/etc/logrotate.d`.

Inoltre, è possibile configurare log specifici del sistema in questa sezione.

Questo ConfigMap può essere utilizzato per fornire la configurazione Logrotate a un'applicazione all'interno di un cluster Kubernetes in modo che l'applicazione possa utilizzarla per gestire i propri log.

**COMANDI**
1. Applicare il ConfigMap al tuo cluster:
```bash
kubectl apply -f <nome-del-tuo-file.yaml>
```
Questo comando creerà o aggiornarà il ConfigMap nel tuo cluster.

2. Visualizzare il ConfigMap:
```bash
kubectl get configmap myconfigmap-files
OUTPUT
NAME                DATA   AGE
myconfigmap-files   1      12m
```
Questo comando ti permette di visualizzare i dettagli del ConfigMap nel tuo cluster.

3. Visualizzare il contenuto del ConfigMap:
```bash
kubectl get configmap myconfigmap-files -o json
OUTPUT
(dati in json)
```
Questo comando ti permette di visualizzare il contenuto del ConfigMap nel formato JSON.

4. Ottenere un valore specifico dal ConfigMap:
Se vuoi ottenere il valore di una chiave specifica (ad esempio, "logrotate.conf") dal ConfigMap, puoi farlo utilizzando il comando `jq` (un'applicazione per l'analisi JSON) in combinazione con `kubectl`. Prima di tutto, assicurati di avere `jq` installato.
```bash
kubectl get configmap myconfigmap-files -o json | jq -r .data.logrotate.conf
OUTPUT
null
```
Questo comando ti restituirà il contenuto del campo "logrotate.conf" dal ConfigMap.
(Per usare il precedente comando installare jq --> $ sudo apt  install jq)

Cap 6/configmap2.yaml
---------------------------------------------------
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  port: "3000"
  environments: "production, development, stage"
```
Il frammento di codice YAML rappresenta un oggetto ConfigMap in Kubernetes chiamato "app-config". Questo ConfigMap contiene due coppie chiave-valore sotto il campo "data":

1. `port` con il valore `"3000"`.
2. `environments` con il valore `"production, development, stage"`.

Queste chiavi possono essere utilizzate per archiviare configurazioni che possono essere utilizzate da applicazioni all'interno del cluster Kubernetes. Ad esempio, un'applicazione potrebbe leggere il valore di `port` da questo ConfigMap per determinare su quale porta deve ascoltare.

**COMANDI**
1. Applicare il ConfigMap al tuo cluster:
```bash
kubectl apply -f <nome-del-tuo-file.yaml>
```
Sostituisci `<nome-del-tuo-file.yaml>` con il nome del file che contiene il tuo oggetto ConfigMap, oppure puoi applicare direttamente il frammento YAML che hai fornito.

2. Visualizzare il ConfigMap:
```bash
kubectl get configmap app-config
```
Questo comando ti permette di visualizzare i dettagli del ConfigMap nel tuo cluster.

3. Visualizzare il contenuto del ConfigMap:
```bash
kubectl get configmap app-config -o yaml
```
Questo comando ti permette di visualizzare il contenuto del ConfigMap nel formato YAML.

4. Ottenere un valore specifico dal ConfigMap:
Per ottenere il valore di una chiave specifica dal ConfigMap, puoi utilizzare il comando `kubectl` in combinazione con `jq` (se disponibile) o `yq`. Ad esempio, per ottenere il valore di `port`:
Utilizzando `jq`:
```bash
kubectl get configmap app-config -o json | jq -r .data.port
```
Utilizzando `yq`:
```bash
kubectl get configmap app-config -o json | yq -r .data.port
```

Questi comandi ti permetteranno di ottenere il valore della chiave specifica dal ConfigMap. Puoi fare lo stesso per la chiave "environments" se desideri ottenere i diversi ambienti elencati.

**Alternativa: creare una configmap con file (app-config.yaml)**
Ecco un esempio del file "app-config.yaml" che potrebbe essere utilizzato per creare un ConfigMap:
```yaml
# File: app-config.yaml

port: "3000"
environments: "production, development, stage"
database_url: "mysql://username:password@mysql-server:3306/mydatabase"
api_key: "your_api_key_here"
```
Questo file YAML contiene le stesse chiavi e valori che hai menzionato nel tuo ConfigMap originale. Ad esempio, "port" con il valore "3000" e "environments" con il valore "production, development, stage". Inoltre, ho aggiunto un paio di altre chiavi come "database_url" e "api_key" per dimostrare come potresti includere diverse configurazioni nel tuo ConfigMap. Puoi creare il tuo file "app-config.yaml" con queste chiavi e valori, quindi utilizzare il comando `kubectl create configmap` come descritto nei messaggi precedenti per creare il ConfigMap nel tuo cluster Kubernetes.

Se hai creato un ConfigMap utilizzando il comando:
`kubectl create configmap --from-file=app-config.yaml`

significa che hai creato il ConfigMap utilizzando un file YAML esterno chiamato "app-config.yaml". 

Il contenuto del file "app-config.yaml" sarà utilizzato per popolare il ConfigMap. Ecco come puoi utilizzare il comando `kubectl create configmap` con il flag `--from-file`:
```bash
kubectl create configmap app-config --from-file=app-config.yaml
```
In questo caso, il nome del ConfigMap è "app-config", e il suo contenuto è stato preso dal file "app-config.yaml". Il ConfigMap sarà quindi creato nel cluster Kubernetes.

Per visualizzare il contenuto del ConfigMap creato con questo comando, puoi utilizzare il seguente comando:
```bash
kubectl get configmap app-config -o yaml
```
Questo comando ti mostrerà il contenuto del ConfigMap, che dovrebbe corrispondere al contenuto del file "app-config.yaml" da cui è stato creato. Puoi quindi accedere alle chiavi e ai valori come spiegato nei miei messaggi precedenti.

Cap 6/pod-with-configmap.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: test-container
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "env" ]
    env:
      - name: PORT
        valueFrom:
          configMapKeyRef:
            name: app-config
            key: port
      - name: ENVIRONMENTS
        valueFrom:
          configMapKeyRef:
            name: app-config
            key: environments
  restartPolicy: Never
```

Il frammento di codice YAML rappresenta un oggetto Pod in Kubernetes che utilizza un ConfigMap per impostare variabili d'ambiente all'interno del suo contenitore. In questo caso, il Pod è chiamato "busybox" e utilizza un contenitore chiamato "test-container" basato sull'immagine BusyBox. Questo contenitore eseguirà il comando `env` all'avvio.

Le variabili d'ambiente `PORT` e `ENVIRONMENTS` nel contenitore del Pod sono impostate utilizzando i dati dal ConfigMap "app-config". Ecco come funziona:

- La variabile d'ambiente `PORT` è impostata utilizzando il valore della chiave "port" dal ConfigMap "app-config".
- La variabile d'ambiente `ENVIRONMENTS` è impostata utilizzando il valore della chiave "environments" dal ConfigMap "app-config".

In questo modo, il Pod può accedere a queste variabili d'ambiente all'interno del contenitore e utilizzarle nell'esecuzione del comando `env`.

Quando avvii questo Pod, il contenitore "test-container" eseguirà il comando `/bin/sh -c "env"` e visualizzerà le variabili d'ambiente impostate. Ad esempio, vedrai che `PORT` avrà il valore "3000" e `ENVIRONMENTS` avrà il valore "production, development, stage", in base ai dati nel ConfigMap "app-config".

**COMANDI**
Ecco alcuni comandi da terminale relativi all'utilizzo di un Pod con un ConfigMap come descritto nel tuo frammento YAML:

1. **Creazione del Pod**:
  Per creare il Pod nel tuo cluster Kubernetes utilizzando il frammento YAML, puoi eseguire il seguente comando:
   ```bash
   kubectl apply -f <nome-del-tuo-file.yaml>
   ```
   Assicurati di sostituire `<nome-del-tuo-file.yaml>` con il nome del file che contiene il tuo oggetto Pod.

2. **Visualizzazione dei Pod**:
   Per vedere lo stato dei Pod nel tuo cluster, puoi eseguire il comando:
   ```bash
   kubectl get pods
   ```
   Questo ti mostrerà l'elenco dei Pod, incluso il tuo "busybox" se è stato creato con successo.

3. **Visualizzazione dei dettagli del Pod**:
   Puoi ottenere dettagli specifici sul tuo Pod con il comando:
   ```bash
   kubectl describe pod busybox
   ```
   Questo comando ti fornirà informazioni dettagliate sullo stato del tuo Pod, inclusi i dettagli relativi ai contenitori e alle variabili d'ambiente.

4. **Accesso al contenitore del Pod**:
   Se desideri accedere al terminale del contenitore all'interno del Pod "busybox", puoi utilizzare il comando `kubectl exec`. Ad esempio, per accedere al terminale del contenitore "test-container":
   ```bash
   kubectl exec -it busybox -c test-container -- /bin/sh
   ```
   Questo ti consentirà di eseguire comandi all'interno del contenitore del Pod.

5. **Verifica delle variabili d'ambiente**:
   Una volta all'interno del contenitore, puoi eseguire il comando `env` per verificare che le variabili d'ambiente `PORT` e `ENVIRONMENTS` siano state impostate correttamente dal ConfigMap:
   ```bash
   env
   ```
   Verificherà le variabili d'ambiente e dovresti vedere i valori impostati in base ai dati nel ConfigMap "app-config".

Questi comandi ti consentono di gestire e monitorare il tuo Pod e verificare l'impostazione delle variabili d'ambiente dall'oggetto ConfigMap all'interno del Pod.

**Visualizzare i log**
Il comando `kubectl logs` viene utilizzato per visualizzare i log di un contenitore all'interno di un Pod. 
Tuttavia, poiché il tuo Pod "busybox" sembra eseguire un comando `env` e non genera log diretti, potresti non vedere molta uscita da questo comando.

Se vuoi visualizzare l'output dei comandi effettuati all'interno del contenitore del tuo Pod "busybox", dovresti eseguire il comando `kubectl exec` all'interno del contenitore, come indicato nel mio messaggio precedente:
```bash
kubectl exec -it busybox -c test-container -- /bin/sh
```
Una volta all'interno del contenitore, puoi eseguire il comando `env` per vedere le variabili d'ambiente impostate dal ConfigMap. Il comando `kubectl logs` è più utile quando desideri visualizzare i log generati da un'applicazione all'interno di un contenitore. Nel tuo caso, il contenitore "busybox" esegue solo il comando `env`, quindi dovresti vedere le variabili d'ambiente quando esegui `env` all'interno del contenitore.

> Nota: se il comando sopra da un mex di errore:
> Il messaggio di errore "error: cannot exec into a container in a completed pod; current phase is Succeeded" indica che il tuo Pod "busybox" è già stato completato con successo (la sua fase è "Succeeded"), il che  significa che il comando `env` all'interno del contenitore è stato eseguito e il Pod è terminato.

Poiché il Pod è già completato, non puoi più eseguire comandi al suo interno con `kubectl exec`. Se desideri visualizzare le variabili d'ambiente impostate all'interno del Pod, puoi farlo attraverso il registro dell'evento o le risorse create in base all'output del comando `env` eseguito all'interno del contenitore quando il Pod era in esecuzione.

Ecco come puoi accedere all'output di un contenitore completato:
1. **Verifica lo stato del Pod**:

   Puoi utilizzare il comando `kubectl get pods` per ottenere il nome del Pod:
   ```bash
   kubectl get pods
	NAME                       READY   STATUS        RESTARTS      AGE
	busybox                    0/1     Completed     0             10m
   ```

2. **Visualizza l'output del contenitore completato**:

   Una volta che hai il nome del Pod, puoi utilizzare il comando `kubectl logs` per visualizzare l'output del contenitore:
   ```bash
   kubectl logs busybox -c test-container
   ```
Questo comando dovrebbe restituire l'output del comando `env` eseguito all'interno del contenitore quando il Pod era in esecuzione.

Cap 6/configmap-for-multiple-files.yaml
---------------------------------------
```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: my-files
data:
  user.properties: >-
    myproperty=value
  simple-text.txt: >-
    Hello world!
```

Il tuo frammento di codice YAML rappresenta un oggetto ConfigMap in Kubernetes chiamato "my-files". Questo ConfigMap contiene due file di testo: "user.properties" e "simple-text.txt", ognuno con il proprio contenuto. 

**Posso usare questi dati in un pod:**

Cap 6/pod-with-configmap-for-files.yaml
---------------------------------------
```yaml
# Definizione di un Pod in Kubernetes
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  # Specifica dei contenitori all'interno del Pod
  containers:
    - name: test-container
      image: gcr.io/google_containers/busybox
      command: [ "/bin/sh", "-c", "ls -la /etc/config" ]
      # Monta il volume del ConfigMap nel percorso /etc/config all'interno del contenitore
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  # Definizione dei volumi utilizzati nel Pod
  volumes:
    - name: config-volume
      # Monta il ConfigMap chiamato "my-files" come volume
      configMap:
        name: my-files
  # Politica di riavvio del Pod (in questo caso, mai riavviato manualmente)
  restartPolicy: Never
```

Abbiamo quindi un Pod con un contenitore chiamato "test-container" e un volume montato da un ConfigMap chiamato "my-files". In questo caso, puoi utilizzare il comando `kubectl logs` per vedere l'output del tuo contenitore e verificare se i file del ConfigMap sono presenti nel percorso specificato.

Puoi applicare questo pod nel tuo cluster Kubernetes con il comando:
```bash
kubectl apply -f <nome-del-tuo-file.yaml>
```
Puoi eseguire il seguente comando per ottenere l'output del tuo contenitore:

```bash
kubectl logs busybox
```
Questo comando dovrebbe mostrare l'output del comando `ls -la /etc/config` all'interno del tuo contenitore, che elencherà i file presenti nel percorso `/etc/config`. Se il volume è stato montato correttamente e il ConfigMap è stato applicato correttamente, dovresti vedere i file "user.properties" e "simple-text.txt" elencati tra gli altri file del ConfigMap.

Prova questo comando e verifica se l'output contiene i file che ti aspetti.

Cap 6/configmap-for-multiple-files2.yaml
----------------------------------------------
```yaml
# Definizione di un oggetto ConfigMap in Kubernetes
kind: ConfigMap
apiVersion: v1
metadata:
  # Nome del ConfigMap
  name: my-files
data:
  # Definizione dei dati nel ConfigMap
  nginx.conf: >-
    # Il riferimento "Backend" è interno a nginx e serve a identificare questo upstream specifico
    upstream Backend {
      # "dsp" è il nome DNS interno utilizzato dal backend Service all'interno di Kubernetes
      server dsp;
    }
    
    server {
      listen 80;
      
      location / {
        # Questa istruzione instradamento il traffico all'upstream denominato Backend
        proxy_pass http://Backend;
      }
    }
  simple-text.txt: >-
    Hello world!
```
Questo frammento di codice rappresenta un oggetto ConfigMap in Kubernetes chiamato "my-files". Il ConfigMap contiene due elementi di dati: uno è un file di configurazione Nginx chiamato "nginx.conf", e l'altro è un file di testo semplice chiamato "simple-text.txt". La configurazione Nginx all'interno del file "nginx.conf" definisce un upstream chiamato "Backend" che instrada il traffico al server denominato "dsp" sulla porta 80.

Il file di testo semplice "simple-text.txt" contiene il messaggio "Hello world!".

**COMANDI**
Ecco alcuni comandi da terminale correlati all'utilizzo di un ConfigMap in Kubernetes, basati sul ConfigMap che hai fornito:

1. **Creazione o Aggiornamento del ConfigMap**:
   Per creare o aggiornare il ConfigMap nel tuo cluster Kubernetes, puoi utilizzare il comando:
   ```bash
   kubectl apply -f <nome-del-tuo-file.yaml>
   ```
   Assicurati di sostituire `<nome-del-tuo-file.yaml>` con il nome del file YAML che contiene il tuo oggetto ConfigMap.

2. **Visualizzazione dei dettagli del ConfigMap**:
   Puoi visualizzare i dettagli del ConfigMap con il comando:
   ```bash
   kubectl get configmap my-files
   ```
   Questo ti mostrerà i dettagli del ConfigMap, inclusi i dati al suo interno.

3. **Visualizzazione del contenuto del ConfigMap**:
   Per visualizzare il contenuto del ConfigMap nel formato YAML, puoi eseguire:
   ```bash
   kubectl get configmap my-files -o yaml
   ```
   Questo comando ti mostrerà il contenuto del ConfigMap nel formato YAML.

4. **Creazione di un Pod utilizzando il ConfigMap**:
   Puoi creare un Pod che utilizza il ConfigMap come volume con il comando:
   ```bash
   kubectl apply -f <nome-del-tuo-file-pod.yaml>
   ```
   Sostituisci `<nome-del-tuo-file-pod.yaml>` con il nome del file YAML contenente la definizione del tuo Pod.

5. **Visualizzazione dei Logs di un Pod**:
   Puoi visualizzare i log di un Pod che utilizza il ConfigMap con il comando:
   ```bash
   kubectl logs <nome-del-tuo-pod>
   ```
   Sostituisci `<nome-del-tuo-pod>` con il nome del tuo Pod.

6. **Esecuzione di Comandi all'interno di un Pod**:
   Puoi eseguire comandi all'interno del tuo Pod con il comando `kubectl exec`. Ad esempio:
   ```bash
   kubectl exec -it <nome-del-tuo-pod> -- /bin/sh
   ```
   Sostituisci `<nome-del-tuo-pod>` con il nome del tuo Pod.

Questi sono alcuni comandi base che puoi utilizzare per interagire con il tuo ConfigMap e il Pod che lo utilizza all'interno di un cluster Kubernetes.

Cap 6/d-nginx-with-configmap.yaml
----------------------------------------------
```yaml
# Definizione di un oggetto Deployment in Kubernetes
apiVersion: apps/v1
kind: Deployment
metadata:
  # Nome del Deployment
  name: nginx
  # Namespace in cui viene creato il Deployment
  namespace: sandbox
spec:
  # Selettore per individuare i Pod associati a questo Deployment
  selector:
    matchLabels:
      run: nginx
      app: dsp
      tier: frontend
  # Definizione del template per i Pod
  template:
    spec:
      # Contenitori all'interno del Pod
      containers:
      - name: nginx
        image: nginx
        # Mount del volume del ConfigMap nel percorso /etc/nginx all'interno del contenitore
        volumeMounts:
        - mountPath: /etc/nginx
          name: nginx-conf
      # Definizione dei volumi utilizzati nel Pod
      volumes:
      - name: nginx-conf
        # Mount del ConfigMap chiamato "nginx-conf"
        configMap:
          name: nginx-conf
          # Specifica l'item (chiave) nel ConfigMap da montare
          items:
          - key: nginx.conf
            path: nginx.conf
```
Questo frammento di codice rappresenta un oggetto Deployment in Kubernetes chiamato "nginx". Il Deployment gestisce l'esecuzione di due repliche di un Pod che utilizza l'immagine Nginx. Il contenitore all'interno del Pod monta un volume del ConfigMap chiamato "nginx-conf" nel percorso `/etc/nginx`. Il ConfigMap contiene un elemento denominato "nginx.conf", che viene montato come file "nginx.conf" nel percorso specificato.

Il Deployment è definito all'interno del namespace "sandbox" e utilizza un selettore per associare i Pod creati da questo Deployment in base alle etichette specificate.

**COMANDI**
Ecco alcuni comandi correlati che puoi utilizzare con il tuo oggetto Deployment in Kubernetes:

1. **Creazione o Aggiornamento del Deployment**:
   Per creare o aggiornare il Deployment nel tuo namespace "sandbox", puoi utilizzare il comando:
   ```bash
   kubectl apply -f <nome-del-tuo-file.yaml>
   ```
   Assicurati di sostituire `<nome-del-tuo-file.yaml>` con il nome del file YAML che contiene la definizione del tuo oggetto Deployment.

2. **Visualizzazione dei Deployment nel Namespace**:
   Puoi visualizzare tutti i Deployment nel namespace "sandbox" con il comando:
   ```bash
   kubectl get deployments -n sandbox
   ```
   Questo ti mostrerà una lista dei Deployment nel namespace specificato.

3. **Visualizzazione dei Pod associati al Deployment**:
   Puoi visualizzare i Pod associati al tuo Deployment con il comando:
   ```bash
   kubectl get pods -n sandbox --selector=run=nginx,app=dsp,tier=frontend
   ```
   Questo ti mostrerà l'elenco dei Pod che soddisfano il selettore specificato.

4. **Visualizzazione dei dettagli del Deployment**:
   Puoi visualizzare i dettagli del tuo Deployment con il comando:
   ```bash
   kubectl describe deployment nginx -n sandbox
   ```
   Questo ti fornirà informazioni dettagliate sul tuo Deployment.

5. **Scaling del Deployment**:
   Puoi regolare il numero di repliche del tuo Deployment con il comando:
   ```bash
   kubectl scale deployment nginx --replicas=<numero-repliche> -n sandbox
   ```
   Sostituisci `<numero-repliche>` con il numero desiderato di repliche.

6. **Visualizzazione dei Logs di un Pod associato al Deployment**:
   Puoi visualizzare i log di uno dei Pod associati al tuo Deployment con il comando:
   ```bash
   kubectl logs <nome-del-tuo-pod> -n sandbox
   ```
   Sostituisci `<nome-del-tuo-pod>` con il nome di uno dei Pod associati.

Questi sono alcuni comandi che puoi utilizzare per gestire e monitorare il tuo oggetto Deployment e i Pod associati nel tuo namespace "sandbox".

Apportiamo una piccola modifica al file `Cap 6/configmap-for-multiple-files2.yaml`, ovvero il valore della porta da 80 a 443:
```yaml
.....
    server {
      listen 443;
.....
```

**Recuperare il contenuto del file "nginx.conf" dal tuo ConfigMap all'interno di un Pod**
1. Trova il nome del tuo Pod:
   ```bash
   kubectl get pods
   ```
   Trova il nome del tuo Pod associato a questo ConfigMap.

2. Esegui il comando `cat` all'interno del tuo Pod per visualizzare il contenuto del file "nginx.conf". Ci sono 2 modi:
2.1. **Con opzione `-it`:**
   ```bash
   kubectl exec -it <nome-del-tuo-pod> -- cat /etc/my-files/nginx.conf
   ```
   - L'opzione `-it` permette di interagire in modo interattivo con il terminale del container.
   - Può essere utile quando si desidera inserire comandi manualmente o lavorare interattivamente all'interno del container.
Questo comando `kubectl exec` ti permette di eseguire il comando `cat` all'interno del contenitore del tuo Pod e visualizzare il contenuto del file "nginx.conf" dal ConfigMap montato nel percorso `/etc/my-files`.

2.2. **Senza opzione `-it` e con specifica del container:**
   ```bash
   kubectl exec <nome-del-tuo-pod> -c <nome-del-tuo-container> -- cat /etc/nginx/nginx.conf
   ```
   - Senza l'opzione `-it`, il comando viene eseguito senza la modalità interattiva.
   - Con l'opzione `-c`, specifica il nome del container all'interno del pod. Utile se ci sono più container nel pod e si desidera specificare uno specifico container.

Entrambi i comandi otterranno il risultato desiderato, ma la scelta tra i due dipenderà dalle tue esigenze specifiche. 
Se non hai più container all'interno del tuo pod, puoi optare per la prima forma più concisa. Se hai più container e vuoi specificare uno specifico container, puoi utilizzare la seconda forma.

# SECRET
Cap 6/secret.yaml
-----------------------------------------------
```yaml
kind: Secret
apiVersion: v1
metadata:
  name: mysecret
data:
  https.cert: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCekNDQ...
type: Opaque
```
Il tuo frammento di codice YAML rappresenta un oggetto Secret in Kubernetes chiamato "mysecret". Ecco alcune considerazioni sulla tua configurazione:

- **Dati Sicreti:** Il Secret contiene un campo "data" con una chiave chiamata "https.cert". La chiave "https.cert" sembra contenere dati codificati in Base64. In genere, questo tipo di dati potrebbe essere un certificato SSL/TLS o una chiave privata associata.

- **Tipo Opaque:** Il tipo del Secret è impostato su "Opaque". Questo tipo di Secret è utilizzato quando il contenuto dei dati non deve essere interpretato dal sistema Kubernetes. È spesso utilizzato per dati binari, come chiavi private, certificati, o altri dati sensibili.

- **Codifica Base64:** I dati all'interno del Secret sono codificati in Base64. Quando utilizzi questi dati, dovrai decodificarli da Base64. Ad esempio, puoi utilizzare il comando `echo` e `base64` in un ambiente Unix per farlo:
  ```bash
  echo "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCekNDQ..." | base64 --decode
  ```
  Questo restituirà il testo originale contenuto nella chiave "https.cert" del tuo Secret.

Ricorda che i Secret contengono informazioni sensibili e dovrebbero essere gestiti con attenzione, soprattutto se vengono utilizzati per memorizzare chiavi private, certificati, o altri dati sensibili all'interno di un cluster Kubernetes.

Nel creare un esempio di stringa è possibile inserire i dati usando un formato semplice: questo può avvenire sostituendo al posto del campo `data`, il campo `stringData`.
Cap 6/secret-stringdata.yaml
--------------------------------------------------
```yaml
kind: Secret
apiVersion: v1
metadata:
  name: my-secret
stringData:
    foo: "test"
```
Questo frammento di codice YAML rappresenta un oggetto Secret in Kubernetes chiamato "my-secret". Ecco alcune considerazioni sulla tua configurazione:

- **Dati Stringa:** Il campo "stringData" è utilizzato per specificare dati come stringhe dirette invece di dati codificati in Base64 come nel campo "data" del Secret standard.

- **Chiave e Valore:** Nell'esempio, è definita una chiave chiamata "foo" con il valore "test".

- **Tipo Opaque:** Se il campo "type" non è specificato, il tipo del Secret sarà impostato su "Opaque" per impostazione predefinita.

Questo Secret può essere utilizzato per memorizzare stringhe dirette, come ad esempio le credenziali di un'applicazione o altre informazioni sensibili. Quando accedi a questi dati da un pod, il sistema Kubernetes gestirà la decodifica delle stringhe in modo trasparente, eliminando la necessità di decodificarle manualmente come dovresti fare con i dati codificati in Base64.

**Visualizzare l'output di un Secret in Kubernetes**
```bash
kubectl get secret my-secret -o yaml
```
Sostituisci "my-secret" con il nome del tuo Secret. Questo comando restituirà l'output YAML del tuo Secret, inclusi i dati all'interno di esso. Puoi vedere il valore della chiave "foo" direttamente nel campo "stringData".

Se vuoi visualizzare solo il valore della chiave "foo", puoi utilizzare il seguente comando:
```bash
kubectl get secret my-secret -o jsonpath='{.stringData.foo}'
```
Questo restituirà solo il valore associato alla chiave "foo".

**Esempio di Secret per l'istanza di PostgreSQL**

Cap 6/secret-multiple-values.yaml
-----------------------------------
```yaml
kind: Secret
apiVersion: v1
metadata:
  name: postgresql
data:
  database-name: c2FtcGxlZGI=
  database-password: ZFRXYlZPZWtwTkpjZU5EZw==
  database-user: dXNlckdKTg==
type: Opaque
```
Questo frammento di codice YAML rappresenta un oggetto Secret in Kubernetes chiamato "postgresql". Ecco alcune considerazioni sulla tua configurazione:

- **Dati Codificati in Base64:** I dati all'interno del Secret sono codificati in Base64. In particolare, ci sono tre chiavi:
  - "database-name" con il valore codificato "c2FtcGxlZGI=" (che, se decodificato, potrebbe rappresentare una stringa come "sampledb").
  - "database-password" con il valore codificato "ZFRXYlZPZWtwTkpjZU5EZw==" (che, se decodificato, potrebbe rappresentare una password segreta).
  - "database-user" con il valore codificato "dXNlckdKTg==" (che, se decodificato, potrebbe rappresentare un nome utente come "userGJN").

- **Tipo Opaque:** Il tipo del Secret è impostato su "Opaque". Questo è spesso usato per indicare che il contenuto del Secret non è interpretato dal sistema Kubernetes.

Quando utilizzi questi dati in un'applicazione o in un pod, dovrai decodificarli dalla codifica Base64. Ad esempio, puoi utilizzare il comando `echo` e `base64` in un ambiente Unix:
```bash
echo "c2FtcGxlZGI=" | base64 --decode
```
Questo restituirà la stringa decodificata "sampledb", e puoi applicare lo stesso approccio per decodificare gli altri valori del tuo Secret. Assicurati di gestire con attenzione i Secret, in quanto contengono informazioni sensibili come nomi utente e password.

Ecco alcuni comandi relativi all'utilizzo di un Secret in Kubernetes:
1. **Creazione o Aggiornamento del Secret:**
   Per creare o aggiornare un Secret nel tuo cluster Kubernetes, puoi utilizzare il comando:
   ```bash
   kubectl apply -f <nome-del-tuo-file.yaml>
   ```
   Assicurati di sostituire `<nome-del-tuo-file.yaml>` con il nome del file YAML contenente la definizione del tuo oggetto Secret.

2. **Visualizzazione dei dettagli del Secret:**
   Puoi visualizzare i dettagli di un Secret con il comando:
   ```bash
   kubectl get secret postgresql
   ```
   Questo ti mostrerà i dettagli del Secret, inclusi il tipo, la data di creazione, e altre informazioni.

3. **Visualizzazione del contenuto del Secret:**
   Puoi visualizzare il contenuto del Secret nel formato YAML con il comando:
   ```bash
   kubectl get secret postgresql -o yaml
   ```
   Questo ti mostrerà i dettagli del Secret, inclusi i dati codificati in Base64.

4. **Decodifica dei Dati in Base64:**
   Puoi decodificare i dati codificati in Base64 all'interno del Secret con il comando:
   ```bash
   echo "c2FtcGxlZGI=" | base64 --decode
   ```
   Questo ti restituirà la stringa decodificata associata alla chiave specificata.

Ricorda di trattare i Secret con attenzione, poiché contengono informazioni sensibili. Assicurati di proteggere l'accesso ai Secret e di utilizzarli in modo sicuro nelle tue applicazioni e nei tuoi pod.

**Esempio di Deployment per l'istanza di PostgreSQL**

Cap 6/d-postgres-with-secret.yaml
--------------------------------------------------
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgresql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgresql
  template:
    metadata:
      labels:
        app: postgresql
    spec:
      volumes:
      - name: postgresql-data
        emptyDir: {}
      containers:
        - name: postgres
          env:
          - name: POSTGRES_DB
            valueFrom:
              secretKeyRef:
                  name: postgresql
                  key: database-name
          - name: POSTGRES_USER
            valueFrom:
              secretKeyRef:
                  name: postgresql
                  key: database-user
          - name: POSTGRES_PASSWORD
            valueFrom:
              secretKeyRef:
                  name: postgresql
                  key: database-password
          ports:
            - containerPort: 5432
              protocol: TCP
          volumeMounts:
            - name: postgresql-data
              mountPath: /var/lib/pgsql/data
          image: postgres
```
Questo frammento di codice YAML rappresenta un oggetto Deployment in Kubernetes per il deployment di un database PostgreSQL. Ecco alcune considerazioni sulla tua configurazione:

- **Repliche:** Il Deployment è configurato con 1 replica del tuo pod PostgreSQL.

- **Selezione del Pod:** Il pod viene selezionato in base alle etichette `app: postgresql`.

- **Definizione del Pod:** La definizione del pod include la configurazione del volume "postgresql-data" come `emptyDir` (un volume vuoto temporaneo che persiste solo durante il ciclo di vita del pod).

- **Contenitore PostgreSQL:**
  - L'immagine del contenitore è "postgres", il che significa che stai utilizzando un'immagine ufficiale di PostgreSQL.
  - Le variabili d'ambiente per il nome del database, l'utente e la password sono prese da un oggetto Secret chiamato "postgresql".

- **Montaggio del Volume:** Il volume "postgresql-data" viene montato nel percorso "/var/lib/pgsql/data" all'interno del container. Questo è il percorso predefinito per i dati di PostgreSQL all'interno del contenitore.

- **Porta del Container:** Il contenitore espone la porta 5432 per la comunicazione con il database PostgreSQL.

Questo Deployment crea un'istanza di PostgreSQL utilizzando le credenziali fornite dal Secret "postgresql". Quando il pod viene avviato, il volume "postgresql-data" è utilizzato per memorizzare i dati del database PostgreSQL in modo persistente.

Per utilizzare questo Deployment, puoi applicarlo nel tuo cluster Kubernetes utilizzando il comando:
```bash
kubectl apply -f <nome-del-tuo-file.yaml>
```
Assicurati di sostituire `<nome-del-tuo-file.yaml>` con il nome del file YAML contenente la definizione del tuo oggetto Deployment.

**Connessione al database**

Per connetterti al database PostgreSQL all'interno del pod, puoi utilizzare il client psql (PostgreSQL). Ecco come puoi farlo:

1. **Esegui un Pod Interattivo:**

   Puoi eseguire un pod interattivo con un terminale all'interno del cluster Kubernetes. Utilizza il comando `kubectl exec -it`:

   ```bash
   kubectl exec -it <nome-del-tuo-pod> -- /bin/bash
   ```

   Sostituisci `<nome-del-tuo-pod>` con il nome del pod in cui è in esecuzione il tuo database PostgreSQL.

2. **Connessione al Database con psql:**

   Una volta all'interno del pod, puoi utilizzare il comando psql per connetterti al database. Utilizza le variabili d'ambiente precedentemente definite:
   ```bash
   psql -U $POSTGRES_USER -d $POSTGRES_DB -h localhost -p 5432
   ```
   Questo comando utilizza le variabili d'ambiente definite nel tuo Deployment per il nome utente (`$POSTGRES_USER`), il nome del database (`$POSTGRES_DB`), e la porta del database (5432). Puoi anche dover inserire la password quando richiesto.

   Se hai impostato la password come un file di ambiente, puoi utilizzare:
   ```bash
   PGPASSWORD=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token) psql -U $POSTGRES_USER -d $POSTGRES_DB -h localhost -p 5432
   ```
   Questo esempio suppone che la password sia memorizzata in un segreto di Kubernetes e venga montata come un file.

Ricorda di sostituire `<nome-del-tuo-pod>` con il nome effettivo del tuo pod. Questo ti consentirà di interagire con il database PostgreSQL all'interno del tuo pod Kubernetes.

**Elenco variabili di ambiente**
Nel tuo Deployment, le variabili d'ambiente sono definite per PostgreSQL. Ecco un elenco delle variabili d'ambiente utilizzate nel tuo caso:

1. **POSTGRES_DB:**
   - Nome del database a cui connettersi.
   - Nel tuo caso, questa variabile è ottenuta dal Secret "postgresql" con la chiave "database-name".

2. **POSTGRES_USER:**
   - Nome utente utilizzato per la connessione al database.
   - Nel tuo caso, questa variabile è ottenuta dal Secret "postgresql" con la chiave "database-user".

3. **POSTGRES_PASSWORD:**
   - Password dell'utente per la connessione al database.
   - Nel tuo caso, questa variabile è ottenuta dal Secret "postgresql" con la chiave "database-password".

Queste variabili d'ambiente vengono utilizzate all'interno del tuo container PostgreSQL per configurare le credenziali di accesso al database. Quando esegui comandi o script all'interno del container, puoi fare riferimento a queste variabili per ottenere le informazioni di connessione corrette.

Se vuoi visualizzare le variabili d'ambiente all'interno del tuo pod Kubernetes e filtrare solo quelle correlate a PostgreSQL, puoi utilizzare il comando `env` insieme a `grep`. Ecco come farlo:
```bash
kubectl exec <nome-del-tuo-pod> -- env | grep -i postgres_
```
Sostituisci `<nome-del-tuo-pod>` con il nome del tuo pod PostgreSQL. Questo comando restituirà tutte le variabili d'ambiente all'interno del tuo pod che contengono "postgres_" nel nome. Potrai vedere i valori corrispondenti a `POSTGRES_DB`, `POSTGRES_USER`, e `POSTGRES_PASSWORD` tra le altre eventuali variabili PostgreSQL.
