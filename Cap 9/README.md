# Descrizione

Codice del capitolo relativo alle risorse applicative più complesse . 

> I dettagli riguardo il codice e il suo funzionamento sono riportati all'interno del libro edito da Apogeo: 
> https://amzn.to/3AMpQj2
-----------------------------------------------------------------------------------------------------------

Cap 9/statefulset-base.yaml
---------------------------

# DESCRIZIONE
Il codice che hai fornito è un esempio di definizione di un oggetto StatefulSet in Kubernetes per un'applicazione MongoDB. Vediamo una spiegazione dettagliata:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongo
```

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto. In questo caso, è `apps/v1`.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è un `StatefulSet`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: mongo`), che identifica univocamente l'oggetto all'interno del cluster.

```yaml
spec:
  selector:
    matchLabels:
      app: mongo
```

- `spec`: Contiene le specifiche dell'oggetto, che definiscono il suo comportamento e il suo stato desiderato.

- `selector`: Specifica un insieme di etichette che il StatefulSet userà per individuare i pod di appartenenza.

- `matchLabels`: Indica che i pod devono avere l'etichetta `app: mongo` per essere gestiti da questo StatefulSet.

```yaml
  serviceName: "mongo"
```

- `serviceName`: Specifica il nome del servizio Kubernetes che fornisce un servizio di rete stabile per la selezione dei pod. In questo caso, è "mongo".

```yaml
  replicas: 3
```

- `replicas`: Specifica il numero desiderato di repliche dei pod gestite da questo StatefulSet. In questo caso, sono desiderate 3 repliche.

```yaml
  template:
    metadata:
      labels:
        app: mongo
```

- `template`: Definisce il modello per la creazione dei pod.

- `metadata`: Specifica le etichette da applicare a ciascun pod creato.

```yaml
    spec:
      containers:
        - name: mongodb
          image: mongo:6.0.4
          command:
            - mongod
            - --replSet
            - rs0
          ports:
            - containerPort: 27017
              name: peer
```

- `spec`: Definisce le specifiche del pod.

- `containers`: Elenco dei container presenti nel pod.

  - `name`: Nome del container, in questo caso, "mongodb".

  - `image`: Specifica l'immagine Docker da utilizzare per il container, in questo caso, "mongo:6.0.4".

  - `command`: Specifica il comando da eseguire quando il container viene avviato. In questo caso, avvia `mongod` con l'opzione `--replSet rs0` per configurare il server MongoDB come parte di un set di repliche.

  - `ports`: Specifica le porte esposte dal container.

    - `containerPort`: La porta del container esposta, in questo caso, 27017, che è la porta predefinita per MongoDB.

    - `name`: Nome della porta, in questo caso, "peer".

In breve, questo StatefulSet definisce un'applicazione MongoDB composta da 3 repliche, ciascuna con un container MongoDB configurato come parte di un set di repliche (`rs0`). Il servizio "mongo" fornisce una stabile interfaccia di rete per accedere a tali repliche.

# COMANDI
Per lavorare con questo StatefulSet in Kubernetes, puoi utilizzare vari comandi da linea di comando (kubectl). Ecco alcuni comandi utili:

1. **Creare o Aggiornare il StatefulSet:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna il StatefulSet utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del StatefulSet:**
   ```bash
   kubectl get statefulsets
   ```

   Questo comando restituisce lo stato attuale di tutti i StatefulSet nel tuo cluster.

3. **Verificare lo stato dei pod del StatefulSet:**
   ```bash
   kubectl get pods -l app=mongo
   ```

   Questo comando mostra lo stato dei pod associati al tuo StatefulSet.

4. **Descrivere un StatefulSet:**
   ```bash
   kubectl describe statefulset mongo
   ```

   Questo comando fornisce dettagli più approfonditi sullo stato del tuo StatefulSet.

5. **Scaling del numero di repliche:**
   ```bash
   kubectl scale statefulset mongo --replicas=5
   ```

   Questo comando aumenta o diminuisce il numero di repliche gestite dal tuo StatefulSet.

6. **Accedere a uno dei pod:**
   ```bash
   kubectl exec -it mongo-0 -- /bin/bash
   ```

   Questo comando consente di accedere a una shell interattiva all'interno di uno dei pod del StatefulSet.

Ricorda di sostituire "mongo" con il nome del tuo StatefulSet e adattare gli altri parametri secondo la tua configurazione. Questi sono solo esempi e potrebbero richiedere modifiche in base alla tua specifica configurazione dell'ambiente Kubernetes.


Cap 9/statefulset-base.yaml + aggiunta di volumi
------------------------------------------------
# DESCRIZIONE
Per aggiungere volumi al tuo StatefulSet MongoDB, puoi modificare la definizione del pod all'interno del template del tuo StatefulSet. Di seguito, un esempio di come aggiungere volumi persistenti al tuo StatefulSet:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongo
spec:
  selector:
    matchLabels:
      app: mongo
  serviceName: "mongo"
  replicas: 3
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongodb
          image: mongo:6.0.4
          command:
            - mongod
            - --replSet
            - rs0
          ports:
            - containerPort: 27017
              name: peer
          volumeMounts:
            - name: data
              mountPath: /data/db
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

In questo esempio:

- `volumeMounts` all'interno del container specifica il mount path all'interno del container (`/data/db`) e il nome del volume (`data`).
- `volumeClaimTemplates` all'interno del `spec` del StatefulSet definisce il modello per i volumi persistenti da reclamare. In questo caso, ogni pod avrà un volume persistente da 1Gi montato in `/data/db`.

Questo esempio presume che tu stia utilizzando un provider di archiviazione che supporta Persistent Volumes (PV) e Persistent Volume Claims (PVC). Puoi adattare le specifiche del volume in base alle tue esigenze, inclusi parametri come la capacità dello storage, la classe di storage, ecc.

Ricorda di eseguire `kubectl apply -f nome_file.yaml` dopo aver apportato le modifiche per applicare le nuove configurazioni al tuo StatefulSet.

# COMANDI
Ecco alcuni comandi di Kubernetes che potresti utilizzare in relazione al tuo StatefulSet con volumi persistenti:

1. **Creare o Aggiornare il StatefulSet con Volumi Persistenti:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando applica o aggiorna le modifiche al tuo StatefulSet utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del StatefulSet:**
   ```bash
   kubectl get statefulsets
   ```

   Questo comando mostra lo stato attuale di tutti i StatefulSet nel tuo cluster.

3. **Verificare lo stato dei pod del StatefulSet:**
   ```bash
   kubectl get pods -l app=mongo
   ```

   Questo comando mostra lo stato dei pod associati al tuo StatefulSet.

4. **Verificare lo stato dei volumi persistenti:**
   ```bash
   kubectl get persistentvolumeclaims
   ```

   Questo comando mostra lo stato corrente delle richieste di volumi persistenti, se sono state create.

5. **Verificare i dettagli del pod:**
   ```bash
   kubectl describe pod nome-del-pod
   ```

   Puoi utilizzare questo comando per ottenere dettagli specifici sul tuo pod, inclusi i volumi montati.

6. **Accedere a uno dei pod:**
   ```bash
   kubectl exec -it nome-del-pod -- /bin/bash
   ```

   Questo comando consente di accedere a una shell interattiva all'interno di uno dei pod del tuo StatefulSet.

Ricorda di sostituire "nome_file.yaml" con il nome del tuo file di configurazione, "app=mongo" con le etichette appropriate e "nome-del-pod" con il nome di uno dei tuoi pod.

Assicurati di aver configurato correttamente i provider di archiviazione per supportare Persistent Volumes e Persistent Volume Claims e che il tuo cluster Kubernetes abbia accesso a queste risorse.


Cap 9/service.yaml
------------------
# DESCRIZIONE
Il codice che hai fornito è un esempio di definizione di un oggetto Servizio (Service) in Kubernetes per un'applicazione MongoDB. Vediamo una spiegazione dettagliata:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo
```

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto. In questo caso, è `v1`, che è la versione principale dell'API.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è un `Service`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: mongo`), che identifica univocamente l'oggetto all'interno del cluster.

```yaml
spec:
  ports:
  - port: 27017
    name: peer
  clusterIP: None
  selector:
    app: mongo
```

- `spec`: Contiene le specifiche dell'oggetto, che definiscono il suo comportamento e il suo stato desiderato.

- `ports`: Specifica le porte che il servizio esporrà.

  - `port`: La porta del servizio, in questo caso, 27017, che è la porta predefinita di MongoDB.

  - `name`: Nome della porta, in questo caso, "peer".

- `clusterIP`: Specifica l'indirizzo IP del servizio all'interno del cluster. Con il valore `None`, il servizio è accessibile solo dall'interno del cluster e non viene assegnato un indirizzo IP stabile.

- `selector`: Specifica un insieme di etichette che il servizio userà per instradare il traffico ai pod corrispondenti.

  - `app: mongo`: Indica che il servizio instraderà il traffico verso i pod che hanno l'etichetta `app: mongo`.

In sintesi, questo Servizio Kubernetes denominato "mongo" espone la porta 27017, che è la porta di default di MongoDB, e instrada il traffico ai pod con l'etichetta `app: mongo`. Inoltre, il servizio non ha un indirizzo IP di cluster stabile (`clusterIP: None`), rendendolo accessibile solo all'interno del cluster. Questo può essere utile in scenari in cui si desidera che i servizi siano accessibili solo internamente, senza esporre un indirizzo IP esterno.

# COMANDI
Ecco alcuni comandi di Kubernetes che puoi utilizzare per interagire con il Servizio (Service) che hai definito:

1. **Creare o Aggiornare il Servizio:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna il servizio utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del Servizio:**
   ```bash
   kubectl get services
   ```

   Questo comando restituisce lo stato attuale di tutti i servizi nel tuo cluster.

3. **Verificare i dettagli del Servizio:**
   ```bash
   kubectl describe service mongo
   ```

   Questo comando fornisce dettagli più approfonditi sul tuo servizio, inclusi i dettagli della configurazione.

4. **Testare la connessione al Servizio:**
   Puoi testare la connessione al servizio eseguendo un comando come `telnet` o `nc` sulla porta del servizio. Ad esempio, per la porta 27017:
   ```bash
   telnet <indirizzo-ip-servizio> 27017
   ```

5. **Eliminare il Servizio:**
   ```bash
   kubectl delete service mongo
   ```

   Questo comando elimina il servizio dal cluster.

Ricorda di sostituire "mongo" con il nome del tuo servizio e adattare gli altri parametri secondo la tua configurazione. Questi sono solo esempi e potrebbero richiedere modifiche in base alla tua specifica configurazione dell'ambiente Kubernetes.


Esempio di definizione di un Job
-------------------------------
# DESCRIZIONE
Ecco un esempio di definizione di un oggetto Job in Kubernetes. Un Job rappresenta un'unità di lavoro indipendente che esegue una determinata attività in un pod e completa con successo prima di terminare.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    metadata:
      name: my-job
    spec:
      containers:
      - name: my-job-container
        image: busybox
        command: ["echo", "Hello, Kubernetes!"]
      restartPolicy: Never
```

In questo esempio:

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto Job. In questo caso, è `batch/v1`.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è un `Job`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: my-job`), che identifica univocamente l'oggetto all'interno del cluster.

- `spec`: Contiene le specifiche dell'oggetto, che definiscono il comportamento e lo stato desiderato.

  - `template`: Definisce il modello per i pod che il Job creerà.

    - `metadata`: Specifica le informazioni del pod.

      - `name: my-job`: Nome del pod.

    - `spec`: Specifica la configurazione del pod.

      - `containers`: Elenco dei container presenti nel pod.

        - `name: my-job-container`: Nome del container.

        - `image: busybox`: Immagine Docker utilizzata per il container. In questo caso, si tratta di una semplice immagine BusyBox.

        - `command: ["echo", "Hello, Kubernetes!"]`: Comando da eseguire nel container.

      - `restartPolicy: Never`: Indica che il pod non deve essere riavviato in caso di errore o terminazione. I Job terminano quando il loro pod termina.

Puoi applicare questo Job nel tuo cluster Kubernetes eseguendo il comando:

```bash
kubectl apply -f nome_file.yaml
```

Questo Job eseguirà un pod con un container BusyBox, che eseguirà il comando "echo Hello, Kubernetes!". Una volta completato, il pod e il Job si chiuderanno.

# COMANDI
Ecco alcuni comandi di Kubernetes che puoi utilizzare in relazione al tuo oggetto Job:

1. **Creare o Aggiornare il Job:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna il Job utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del Job:**
   ```bash
   kubectl get jobs
   ```

   Questo comando restituisce lo stato attuale di tutti i Job nel tuo cluster.

3. **Verificare i dettagli del Job:**
   ```bash
   kubectl describe job nome-del-job
   ```

   Questo comando fornisce dettagli più approfonditi sul tuo Job, inclusi i dettagli della configurazione e lo stato corrente.

4. **Verificare lo stato dei pod creati dal Job:**
   ```bash
   kubectl get pods -l job-name=nome-del-job
   ```

   Questo comando mostra lo stato dei pod creati dal tuo Job.

5. **Accedere a uno dei pod creati dal Job:**
   ```bash
   kubectl exec -it nome-del-pod -- /bin/sh
   ```

   Questo comando consente di accedere a una shell interattiva all'interno di uno dei pod creati dal Job.

6. **Visualizzare i log del pod creato dal Job:**
   ```bash
   kubectl logs nome-del-pod
   ```

   Questo comando mostra i log generati dal container nel pod creato dal tuo Job.

7. **Eliminare il Job:**
   ```bash
   kubectl delete job nome-del-job
   ```

   Questo comando elimina il Job dal cluster. I pod creati dal Job verranno automaticamente eliminati.

Ricorda di sostituire "nome_file.yaml" con il nome del tuo file di configurazione, "nome-del-job" con il nome del tuo Job e "nome-del-pod" con il nome di uno dei pod creati dal tuo Job.


Cap 9/cronjob.yaml
------------------
# DESCRIZIONE
Il codice che hai fornito rappresenta la definizione di un oggetto `CronJob` in Kubernetes. Un `CronJob` è un controller che esegue lavori basati su una programmazione cron, cioè consente di eseguire le attività in modo pianificato. Di seguito una spiegazione dettagliata:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "0 0 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello World
          restartPolicy: OnFailure
```

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto. In questo caso, è `batch/v1`.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è un `CronJob`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: hello`), che identifica univocamente l'oggetto all'interno del cluster.

- `spec`: Contiene le specifiche dell'oggetto, che definiscono il comportamento e lo stato desiderato.

  - `schedule`: Specifica la pianificazione cron per il lavoro. In questo caso, il cron è impostato per eseguire l'attività ogni giorno a mezzanotte (`"0 0 * * *"`).

  - `jobTemplate`: Definisce il modello per i lavori creati da questo `CronJob`.

    - `spec`: Contiene le specifiche del lavoro creato dal `CronJob`.

      - `template`: Definisce il modello per i pod creati dal lavoro.

        - `spec`: Contiene la configurazione del pod.

          - `containers`: Elenco dei container presenti nel pod.

            - `name: hello`: Nome del container.

            - `image: busybox:1.28`: Immagine Docker utilizzata per il container.

            - `imagePullPolicy: IfNotPresent`: Specifica che Kubernetes deve utilizzare l'immagine localmente se disponibile, altrimenti scaricarla.

            - `command`: Specifica il comando da eseguire nel container.

              - `/bin/sh -c date; echo Hello World`: Esegue due comandi, mostra la data e stampa "Hello World".

          - `restartPolicy: OnFailure`: Indica che il pod deve essere riavviato solo in caso di fallimento.

In sintesi, questo `CronJob` pianifica l'esecuzione di un lavoro che crea un pod ogni giorno a mezzanotte. Il pod eseguirà un container BusyBox, stampando la data e la stringa "Hello World".

# COMANDI
Ecco alcuni comandi di Kubernetes che puoi utilizzare in relazione al tuo oggetto `CronJob`:

1. **Creare o Aggiornare il CronJob:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna il `CronJob` utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del CronJob:**
   ```bash
   kubectl get cronjobs
   ```

   Questo comando restituisce lo stato attuale di tutti i `CronJob` nel tuo cluster.

3. **Verificare i dettagli del CronJob:**
   ```bash
   kubectl describe cronjob nome-del-cronjob
   ```

   Questo comando fornisce dettagli più approfonditi sul tuo `CronJob`, inclusi i dettagli della configurazione e lo stato corrente.

4. **Verificare i lavori creati dal CronJob:**
   ```bash
   kubectl get jobs -l cronjob=nome-del-cronjob
   ```

   Questo comando mostra lo stato dei lavori creati dal tuo `CronJob`.

5. **Accedere a uno dei pod creati dai lavori:**
   ```bash
   kubectl get pods -l job-name=nome-del-lavoro
   ```

   Questo comando mostra lo stato dei pod creati dai lavori del tuo `CronJob`.

6. **Visualizzare i log del pod creato dai lavori:**
   ```bash
   kubectl logs nome-del-pod
   ```

   Questo comando mostra i log generati dal container nel pod creato dal tuo lavoro.

7. **Eliminare il CronJob:**
   ```bash
   kubectl delete cronjob nome-del-cronjob
   ```

   Questo comando elimina il `CronJob` dal cluster. I lavori e i pod creati dal `CronJob` verranno automaticamente eliminati.

Ricorda di sostituire "nome_file.yaml" con il nome del tuo file di configurazione, "nome-del-cronjob" con il nome del tuo `CronJob`, "nome-del-lavoro" con il nome del tuo lavoro e "nome-del-pod" con il nome di uno dei pod creati dal tuo `CronJob`.


Cap 9/cronjob2.yaml
------------------
# DESCRIZIONE
Il codice che hai fornito rappresenta la definizione di un oggetto `CronJob` in Kubernetes. Un `CronJob` è un controller che esegue lavori basati su una programmazione cron. Di seguito una spiegazione dettagliata:

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: batch-job
spec:
  schedule: "0,15,30,45 * * * *"
  jobTemplate:
    spec:
      template:
        metadata:
          labels:
            app: periodic-batch-job
        spec:
          restartPolicy: OnFailure
          containers:
          - name: main
            image: check-webserver
```

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto. In questo caso, è `batch/v1beta1`.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è un `CronJob`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: batch-job`), che identifica univocamente l'oggetto all'interno del cluster.

- `spec`: Contiene le specifiche dell'oggetto, che definiscono il comportamento e lo stato desiderato.

  - `schedule`: Specifica la pianificazione cron per il lavoro. In questo caso, il cron è impostato per eseguire l'attività ogni 0, 15, 30 e 45 minuti di ogni ora (`"0,15,30,45 * * * *"`).

  - `jobTemplate`: Definisce il modello per i lavori creati da questo `CronJob`.

    - `spec`: Contiene le specifiche del lavoro creato dal `CronJob`.

      - `template`: Definisce il modello per i pod creati dal lavoro.

        - `metadata`: Specifica le etichette del pod.

          - `labels`: Assegna un'etichetta all'applicazione (`app: periodic-batch-job`).

        - `spec`: Contiene la configurazione del pod.

          - `restartPolicy: OnFailure`: Indica che il pod deve essere riavviato solo in caso di fallimento.

          - `containers`: Elenco dei container presenti nel pod.

            - `name: main`: Nome del container.

            - `image: check-webserver`: Immagine Docker utilizzata per il container.

In sintesi, questo `CronJob` pianifica l'esecuzione di un lavoro che crea un pod ogni 0, 15, 30 e 45 minuti di ogni ora. Il pod avrà un container chiamato "main" con l'immagine "check-webserver". Se il pod fallisce, verrà riavviato, ma se riesce, il lavoro è considerato completato.

# COMANDI
Ecco alcuni comandi di Kubernetes che puoi utilizzare in relazione al tuo oggetto `CronJob`:

1. **Creare o Aggiornare il CronJob:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna il `CronJob` utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del CronJob:**
   ```bash
   kubectl get cronjobs
   ```

   Questo comando restituisce lo stato attuale di tutti i `CronJob` nel tuo cluster.

3. **Verificare i dettagli del CronJob:**
   ```bash
   kubectl describe cronjob nome-del-cronjob
   ```

   Questo comando fornisce dettagli più approfonditi sul tuo `CronJob`, inclusi i dettagli della configurazione e lo stato corrente.

4. **Verificare i lavori creati dal CronJob:**
   ```bash
   kubectl get jobs -l cronjob=nome-del-cronjob
   ```

   Questo comando mostra lo stato dei lavori creati dal tuo `CronJob`.

5. **Accedere a uno dei pod creati dai lavori:**
   ```bash
   kubectl get pods -l job-name=nome-del-lavoro
   ```

   Questo comando mostra lo stato dei pod creati dai lavori del tuo `CronJob`.

6. **Visualizzare i log del pod creato dai lavori:**
   ```bash
   kubectl logs nome-del-pod
   ```

   Questo comando mostra i log generati dal container nel pod creato dal tuo lavoro.

7. **Eliminare il CronJob:**
   ```bash
   kubectl delete cronjob nome-del-cronjob
   ```

   Questo comando elimina il `CronJob` dal cluster. I lavori e i pod creati dal `CronJob` verranno automaticamente eliminati.

Ricorda di sostituire "nome_file.yaml" con il nome del tuo file di configurazione, "nome-del-cronjob" con il nome del tuo `CronJob`, "nome-del-lavoro" con il nome del tuo lavoro e "nome-del-pod" con il nome di uno dei pod creati dal tuo `CronJob`.


Cap 9/statefulset-init.yaml
---------------------------
# DESCRIZIONE
Il codice che hai fornito rappresenta la definizione di un oggetto StatefulSet in Kubernetes, che è utilizzato per gestire l'esecuzione di un'applicazione stateful come MongoDB. Di seguito una spiegazione dettagliata del tuo codice:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongo
spec:
  selector:
    matchLabels:
      app: mongo
  serviceName: "mongo"
  replicas: 3
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongodb
          image: mongo:6.0.4
          command:
          - mongod
          - --replSet
          - rs0
          - "--bind_ip"
          - "0.0.0.0"
          ports:
            - containerPort: 27017
              name: web
        - name: init-mongo
          image: mongo:6.0.4
          command:
          - bash
          - /config/init.sh
          volumeMounts:
          - name: config
            mountPath: /config
      volumes:
       - name: config
         configMap:
           name: "mongo-init"
```

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto. In questo caso, è `apps/v1`.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è uno `StatefulSet`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: mongo`), che identifica univocamente l'oggetto all'interno del cluster.

- `spec`: Contiene le specifiche dell'oggetto, che definiscono il comportamento e lo stato desiderato.

  - `selector`: Specifica un set di etichette che il tuo StatefulSet userà per selezionare i pod associati.

  - `serviceName`: Specifica il nome del servizio che il tuo StatefulSet utilizzerà per indirizzare il traffico.

  - `replicas`: Specifica il numero di repliche desiderate del tuo StatefulSet. In questo caso, sono configurate 3 repliche.

  - `template`: Definisce il modello per i pod creati dal tuo StatefulSet.

    - `metadata`: Specifica le etichette da applicare ai pod.

    - `spec`: Contiene la configurazione del pod.

      - `containers`: Elenco dei container presenti nel pod.

        - `name: mongodb`: Nome del primo container.

        - `image: mongo:6.0.4`: Immagine Docker utilizzata per il container.

        - `command`: Specifica il comando da eseguire nel container. In questo caso, sta avviando il processo mongod con la configurazione di replica set (`--replSet rs0`) e l'indirizzo IP di binding specificato (`--bind_ip 0.0.0.0`).

        - `ports`: Specifica le porte esposte dal container. In questo caso, la porta 27017 è esposta con il nome "web".

        - `name: init-mongo`: Nome del secondo container.

        - `image: mongo:6.0.4`: Immagine Docker utilizzata per il secondo container.

        - `command`: Specifica il comando da eseguire nel secondo container. In questo caso, sta avviando uno script di inizializzazione (`/config/init.sh`) all'interno del container.

        - `volumeMounts`: Specifica i volumi montati nel container. In questo caso, è montato il volume "config" nel percorso "/config".

      - `volumes`: Specifica i volumi disponibili per i pod del tuo StatefulSet.

        - `name: config`: Nome del volume.

        - `configMap`: Specifica la ConfigMap da utilizzare per fornire i dati di configurazione. In questo caso, utilizza la ConfigMap chiamata "mongo-init".

In sintesi, questo StatefulSet gestisce un'applicazione MongoDB con tre repliche. Ogni pod ha due container: uno per il processo MongoDB e uno per l'inizializzazione con uno script specificato. Un volume ConfigMap viene utilizzato per fornire la configurazione iniziale al pod.

# COMANDI
Ecco alcuni comandi di Kubernetes che puoi utilizzare in relazione al tuo oggetto `StatefulSet`:

1. **Creare o Aggiornare lo StatefulSet:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna lo `StatefulSet` utilizzando il file di configurazione YAML fornito.

2. **Verificare lo stato del StatefulSet:**
   ```bash
   kubectl get statefulsets
   ```

   Questo comando restituisce lo stato attuale di tutti gli `StatefulSet` nel tuo cluster.

3. **Verificare i dettagli dello StatefulSet:**
   ```bash
   kubectl describe statefulset nome-del-statefulset
   ```

   Questo comando fornisce dettagli più approfonditi sul tuo `StatefulSet`, inclusi i dettagli della configurazione e lo stato corrente.

4. **Verificare lo stato dei pod creati dallo StatefulSet:**
   ```bash
   kubectl get pods -l app=mongo
   ```

   Questo comando mostra lo stato dei pod associati al tuo `StatefulSet`.

5. **Accedere a uno dei pod creati dallo StatefulSet:**
   ```bash
   kubectl exec -it nome-del-pod -- /bin/bash
   ```

   Questo comando consente di accedere a una shell interattiva all'interno di uno dei pod del tuo `StatefulSet`.

6. **Visualizzare i log del pod creato dallo StatefulSet:**
   ```bash
   kubectl logs nome-del-pod
   ```

   Questo comando mostra i log generati dal container nel pod creato dal tuo `StatefulSet`.

7. **Eliminare lo StatefulSet e i relativi pod:**
   ```bash
   kubectl delete statefulset nome-del-statefulset
   ```

   Questo comando elimina lo `StatefulSet` dal cluster, insieme a tutti i pod associati.

Ricorda di sostituire "nome_file.yaml" con il nome del tuo file di configurazione, "nome-del-statefulset" con il nome del tuo `StatefulSet`, e "nome-del-pod" con il nome di uno dei pod creati dal tuo `StatefulSet`.


Cap 9/configmap.yaml
--------------------
# DESCRIZIONE
Il codice che hai fornito rappresenta la definizione di un oggetto `ConfigMap` in Kubernetes. Una `ConfigMap` in Kubernetes è un oggetto che fornisce un modo per inserire dati di configurazione nei contenitori senza dover modificare le immagini dei contenitori stessi. Di seguito una spiegazione dettagliata del tuo codice:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-init
data:
  init.sh: |
    #!/bin/bash
    until /usr/bin/mongosh --eval 'printjson(db.serverStatus())'; do
      echo "Sto controllando lo stato del database….."
      sleep 2
    done
    echo "OK."
    HOST=mongo-0.mongo:27017
    until /usr/bin/mongosh --host=${HOST} --eval 'printjson(db.serverStatus())'; do
      echo "Mi sto collegando all’istanza Mongo..."
      sleep 2
    done
    echo "Connesso."
    if [[ "${HOSTNAME}" != 'mongo-0' ]]; then
      until /usr/bin/mongosh --host=${HOST} --eval="printjson(rs.status())" | grep -v "no replset config has been received"; do
        echo "In attesa di configurazione"
        sleep 2
      done
      echo "Sto aggiungendo le repliche."
      /usr/bin/mongosh --host=${HOST} --eval="printjson(rs.add('${HOSTNAME}.mongo'))"
    fi
    if [[ "${HOSTNAME}" == 'mongo-0' ]]; then
      echo "Configurazione del nodo primario"
      /usr/bin/mongosh --eval="printjson(rs.initiate(\
      {'_id': 'rs0', 'members': [{'_id': 0, \
      'host': 'mongo-0.mongo:27017'}]}))"
    fi
    
    echo "Configurazione completata."
    while true; do
      sleep 3600
    done
```

- `apiVersion`: Specifica la versione dell'API Kubernetes utilizzata per definire questo oggetto. In questo caso, è `v1`.

- `kind`: Specifica il tipo di oggetto Kubernetes che stai creando. In questo caso, è una `ConfigMap`.

- `metadata`: Contiene le informazioni di base sull'oggetto, come il nome (`name: mongo-init`), che identifica univocamente l'oggetto all'interno del cluster.

- `data`: Contiene i dati di configurazione effettivi. In questo caso, c'è un'unica chiave `init.sh` associata a uno script di inizializzazione.

  - `init.sh`: Contiene uno script Bash che verrà eseguito come parte dell'inizializzazione del contenitore. Questo script sembra essere un'applicazione specifica per MongoDB che viene eseguita all'avvio del contenitore. Una breve spiegazione dello script:

    - Utilizza un ciclo `until` per attendere che il server MongoDB sia pronto ad accettare connessioni (`db.serverStatus()`).

    - Successivamente, si connette all'istanza MongoDB specificata (`HOST=mongo-0.mongo:27017`) e attende che sia pronta a ricevere connessioni.

    - Controlla il nome dell'host (`HOSTNAME`) e se non è "mongo-0", attende che la configurazione del set di repliche sia pronta e quindi aggiunge il nodo corrente al set di repliche.

    - Se l'host è "mongo-0", inizializza il set di repliche con un solo membro (il nodo corrente).

    - Infine, entra in un ciclo infinito (`while true`) per mantenere il contenitore in esecuzione, eseguendo una pausa di 3600 secondi tra i cicli.

Questo script è progettato per configurare un nodo MongoDB all'avvio del contenitore, garantendo che sia in grado di partecipare a un set di repliche, se necessario.

# COMANDI
Poiché lo script fornito è progettato per essere eseguito all'interno di un contenitore, non ci sono comandi Kubernetes direttamente associati ad esso. Tuttavia, puoi utilizzare comandi Kubernetes per gestire la `ConfigMap` e gli oggetti che la utilizzano. Ecco alcuni comandi utili:

1. **Creare o Aggiornare la ConfigMap:**
   ```bash
   kubectl apply -f nome_file.yaml
   ```

   Questo comando crea o aggiorna la `ConfigMap` utilizzando il file di configurazione YAML fornito.

2. **Visualizzare i dettagli della ConfigMap:**
   ```bash
   kubectl get configmap nome-della-configmap -o yaml
   ```

   Questo comando mostra i dettagli della tua `ConfigMap`, inclusi i dati di configurazione.

3. **Visualizzare i dati di una specifica chiave nella ConfigMap:**
   ```bash
   kubectl get configmap nome-della-configmap -o jsonpath='{.data.nome-chiave}' 
   ```

   Questo comando consente di estrarre specificamente il valore associato a una determinata chiave nella tua `ConfigMap`.

4. **Creare un pod utilizzando la ConfigMap:**
   ```bash
   kubectl run nome-del-pod --image=immagine --restart=Never --dry-run=client -o yaml --command -- /bin/sh -c "comando-utilizzando-configmap"
   ```

   Questo comando crea il manifesto di un pod che utilizza la `ConfigMap` come variabile d'ambiente o file di configurazione.

5. **Eseguire uno script nella ConfigMap in un pod esistente:**
   ```bash
   kubectl exec nome-del-pod -- /bin/bash -c "cat /config/init.sh | bash"
   ```

   Questo comando esegue uno script contenuto nella `ConfigMap` all'interno di un pod esistente.

6. **Eliminare la ConfigMap:**
   ```bash
   kubectl delete configmap nome-della-configmap
   ```

   Questo comando elimina la `ConfigMap` dal cluster.

Ricorda di sostituire "nome_file.yaml" con il nome del tuo file di configurazione, "nome-della-configmap" con il nome della tua `ConfigMap`, "nome-chiave" con il nome della chiave specifica nella tua `ConfigMap`, "nome-del-pod" con il nome del tuo pod, e "immagine" con l'immagine del tuo contenitore.


## Note sulla Configmap

Le `ConfigMap` in Kubernetes possono essere create e applicate in qualsiasi momento, anche dopo che i pod associati sono già in esecuzione. La creazione o l'aggiornamento di una `ConfigMap` non influisce direttamente sui pod esistenti; questi ultimi devono essere riavviati o gestiti manualmente per applicare le nuove configurazioni fornite dalla `ConfigMap`.

Ecco alcuni punti importanti da considerare:

1. **Prima dell'avvio dei pod:**
   Puoi creare la `ConfigMap` prima di creare i pod. In questo modo, quando i pod vengono avviati, possono utilizzare la configurazione fornita dalla `ConfigMap`.

2. **Durante l'esecuzione dei pod:**
   Puoi anche creare o aggiornare una `ConfigMap` mentre i pod sono in esecuzione. Tuttavia, i pod esistenti non vedranno automaticamente le modifiche apportate alla `ConfigMap`. Devi riavviare o gestire manualmente i pod per applicare le nuove configurazioni.

Ad esempio, se hai un pod che utilizza una variabile d'ambiente fornita da una `ConfigMap`, puoi aggiornare la `ConfigMap` e quindi riavviare il pod per applicare la nuova configurazione.

```bash
# Esempio di aggiornamento della ConfigMap
kubectl apply -f nome_file_configmap.yaml

# Esempio di eliminazione e ricreazione del pod per applicare le nuove configurazioni
kubectl delete pod nome-del-pod
```

3. **Pod già in esecuzione:**
   Se hai pod già in esecuzione e desideri applicare una `ConfigMap` a cui fanno riferimento, devi considerare come gestire la transizione. Un modo può essere eliminare e ricreare i pod o gestire manualmente l'applicazione delle nuove configurazioni.

In generale, la flessibilità di applicare `ConfigMap` in qualsiasi momento è una caratteristica utile che consente di aggiornare la configurazione dei pod senza doverli interrompere. Tuttavia, è importante comprendere il comportamento dei pod esistenti e pianificare le azioni di conseguenza per evitare interruzioni indesiderate.

