# Descrizione

Codice del capitolo relativo alle best practices in ambito enterprise.

> Gli esempi riportati sono da intendersi a scopo illustrativo, mentre tutti i dettagli riguardo il codice e il suo 
funzionamento sono riportati all'interno del libro edito da Apogeo: 
> https://amzn.to/3AMpQj2
-------------------------------------------------------------------------------------------------------------------
Cap 14/autoscaler/hpa-cpu-avg.yaml
----------------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando YAML. L'HPA regola dinamicamente il numero di repliche di un Deployment in base al carico di lavoro. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v2`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample-hpa`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `minReplicas`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `metrics`: Specifica le metriche utilizzate per adattare dinamicamente il numero di repliche.
    - `type: Resource`: Indica che la metrica è basata sulle risorse, come CPU o memoria.
      - `resource`: Specifica la risorsa su cui basare la metrica, in questo caso, `cpu`.
        - `name: cpu`: Specifica che la metrica è basata sulla CPU.
        - `target`: Specifica il tipo di target, che è l'utilizzazione della risorsa (`type: Utilization`).
          - `averageUtilization: 50`: Imposta l'utilizzazione media della CPU target al 50%.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample-hpa
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base all'utilizzazione della CPU, mantenendo l'utilizzazione media al 50% come specificato nella configurazione.


Cap 14/autoscaler/hpa-cpu-target.yaml
-------------------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando la versione 1 dell'API di autoscaling. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v1`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `minReplicas`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `targetCPUUtilizationPercentage`: L'utilizzazione della CPU target a cui l'HPA mira. In questo caso, è impostato al 75%.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base all'utilizzazione della CPU, mantenendo l'utilizzazione media al 75% come specificato nella configurazione.


Cap 14/autoscaler/hpa-custom-metrics-object.yaml
------------------------------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando YAML. Questo HPA monitora la metrica "requests-per-second" di un oggetto di tipo Ingress e regola dinamicamente il numero di repliche di un Deployment in base a questa metrica. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v2`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample-hpa`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `minReplicas`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `metrics`: Specifica le metriche utilizzate per adattare dinamicamente il numero di repliche.
    - `type: Object`: Indica che la metrica è basata su un oggetto specifico.
      - `object`: Specifica le informazioni sull'oggetto.
        - `metric`: Specifica il nome della metrica, in questo caso, "requests-per-second".
        - `describedObject`: Specifica l'oggetto Kubernetes che fornisce la metrica. In questo caso, è di tipo Ingress con il nome `main-route` dell'API di networking.k8s.io/v1.
        - `target`: Specifica il tipo di target, che è il valore della metrica (`type: Value`).
          - `value: 2k`: Imposta il valore target della metrica a 2000 richieste al secondo.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample-hpa
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base alla metrica "requests-per-second" dell'Ingress, mantenendo il valore medio target a 2000 richieste al secondo come specificato nella configurazione.


Cap 14/autoscaler/hpa-custom-metrics.yaml
-----------------------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando YAML. L'HPA monitora la metrica "packets-per-second" a livello di pod e regola dinamicamente il numero di repliche di un Deployment in base a questa metrica. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v2`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample-hpa`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `minReplicas`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `metrics`: Specifica le metriche utilizzate per adattare dinamicamente il numero di repliche.
    - `type: Pods`: Indica che la metrica è basata sui pod.
      - `pods`: Specifica le informazioni sulla metrica pod.
        - `metric`: Specifica il nome della metrica, in questo caso, "packets-per-second".
        - `target`: Specifica il tipo di target, che è il valore medio della metrica (`type: AverageValue`).
          - `averageValue: 1k`: Imposta il valore medio target della metrica a 1000 pacchetti al secondo.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample-hpa
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base alla metrica "packets-per-second" a livello di pod, mantenendo il valore medio target a 1000 pacchetti al secondo come specificato nella configurazione.


Cap 14/autoscaler/hpa-stabilizationWindowSeconds-avg.yaml
---------------------------------------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando YAML, con l'aggiunta di specifiche per il comportamento del ridimensionamento. L'HPA monitora la metrica della memoria (`memory`) e regola dinamicamente il numero di repliche di un Deployment in base a questa metrica. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v2`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample-hpa`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `behavior`: Specifica il comportamento del ridimensionamento.
    - `scaleDown`: Specifica le regole per il ridimensionamento verso il basso.
      - `stabilizationWindowSeconds: 60`: Imposta una finestra di stabilizzazione di 60 secondi durante la quale il numero di repliche non viene modificato.
      - `selectPolicy: Max`: Usa la politica Max per selezionare la metrica da utilizzare per il ridimensionamento verso il basso.
      - `policies`: Specifica le politiche di ridimensionamento verso il basso.
        - `- type: Pods`: Specifica una politica basata sul numero di pod.
          - `value: 4`: Imposta il valore a 4 pod.
          - `periodSeconds: 60`: Specifica il periodo di valutazione della politica a 60 secondi.
        - `- type: Percent`: Specifica una politica basata sulla percentuale di riduzione.
          - `value: 10`: Imposta la percentuale a 10%.
          - `periodSeconds: 60`: Specifica il periodo di valutazione della politica a 60 secondi.
  - `minReplicas: 1`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas: 3`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `metrics`: Specifica le metriche utilizzate per adattare dinamicamente il numero di repliche.
    - `- type: Resource`: Indica che la metrica è basata sulle risorse, come la memoria.
      - `resource`: Specifica la risorsa su cui basare la metrica, in questo caso, `memory`.
        - `name: memory`: Specifica che la metrica è basata sulla memoria.
        - `target`: Specifica il tipo di target, che è il valore medio della metrica (`type: AverageValue`).
          - `averageValue: 500Mi`: Imposta il valore medio target della metrica a 500 megabyte di memoria.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample-hpa
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base alla metrica della memoria, mantenendo il valore medio target a 500 megabyte di memoria come specificato nella configurazione. Inoltre, sono specificate politiche di ridimensionamento verso il basso per garantire che il ridimensionamento verso il basso avvenga gradualmente e in modo controllato.


Cap 14/autoscaler/hpa-stabilizationWindowSeconds-scaleDown.yaml
---------------------------------------------------------------
# DESCRIZIONE
Questa è un'altra definizione di Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando YAML, con specifiche per il comportamento del ridimensionamento. In questo caso, l'HPA monitora la metrica della CPU e regola dinamicamente il numero di repliche di un Deployment in base a questa metrica. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v2`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample-hpa`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `behavior`: Specifica il comportamento del ridimensionamento.
    - `scaleDown`: Specifica le regole per il ridimensionamento verso il basso.
      - `stabilizationWindowSeconds: 60`: Imposta una finestra di stabilizzazione di 60 secondi durante la quale il numero di repliche non viene modificato.
      - `selectPolicy: Max`: Usa la politica Max per selezionare la metrica da utilizzare per il ridimensionamento verso il basso.
      - `policies`: Specifica le politiche di ridimensionamento verso il basso.
        - `- type: Pods`: Specifica una politica basata sul numero di pod.
          - `value: 4`: Imposta il valore a 4 pod.
          - `periodSeconds: 60`: Specifica il periodo di valutazione della politica a 60 secondi.
        - `- type: Percent`: Specifica una politica basata sulla percentuale di riduzione.
          - `value: 10`: Imposta la percentuale a 10%.
          - `periodSeconds: 60`: Specifica il periodo di valutazione della politica a 60 secondi.
  - `minReplicas: 1`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas: 3`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `metrics`: Specifica le metriche utilizzate per adattare dinamicamente il numero di repliche.
    - `- type: Resource`: Indica che la metrica è basata sulle risorse, in questo caso, la CPU.
      - `resource`: Specifica la risorsa su cui basare la metrica, in questo caso, `cpu`.
        - `name: cpu`: Specifica che la metrica è basata sulla CPU.
        - `target`: Specifica il tipo di target, che è l'utilizzazione della risorsa (`type: Utilization`).
          - `averageUtilization: 50`: Imposta l'utilizzazione media della CPU target al 50%.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample-hpa
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base all'utilizzazione della CPU, mantenendo l'utilizzazione media al 50% come specificato nella configurazione. Inoltre, sono specificate politiche di ridimensionamento verso il basso per garantire che il ridimensionamento verso il basso avvenga gradualmente e in modo controllato.


Cap 14/autoscaler/hpa-stabilizationWindowSeconds.yaml
-----------------------------------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Horizontal Pod Autoscaler (HPA) in Kubernetes utilizzando YAML. L'HPA monitora la metrica della CPU e regola dinamicamente il numero di repliche di un Deployment in base a questa metrica. Inoltre, sono specificate le regole per il ridimensionamento verso l'alto. Ecco una spiegazione del codice:

- `apiVersion: autoscaling/v2`: Specifica la versione dell'API dell'Horizontal Pod Autoscaler.
- `kind: HorizontalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Horizontal Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'HPA (`sample-hpa`).
- `spec`: Contiene le specifiche dell'HPA.
  - `scaleTargetRef`: Specifica il riferimento al quale l'HPA si applica. In questo caso, l'HPA è associato a un Deployment dell'API di Apps/v1 con il nome `sample`.
  - `behavior`: Specifica il comportamento del ridimensionamento.
    - `scaleUp`: Specifica le regole per il ridimensionamento verso l'alto.
      - `stabilizationWindowSeconds: 60`: Imposta una finestra di stabilizzazione di 60 secondi durante la quale il numero di repliche non viene modificato.
  - `minReplicas: 1`: Il numero minimo di repliche che l'HPA può ridurre il Deployment.
  - `maxReplicas: 3`: Il numero massimo di repliche che l'HPA può aumentare il Deployment.
  - `metrics`: Specifica le metriche utilizzate per adattare dinamicamente il numero di repliche.
    - `- type: Resource`: Indica che la metrica è basata sulle risorse, in questo caso, la CPU.
      - `resource`: Specifica la risorsa su cui basare la metrica, in questo caso, `cpu`.
        - `name: cpu`: Specifica che la metrica è basata sulla CPU.
        - `target`: Specifica il tipo di target, che è l'utilizzazione della risorsa (`type: Utilization`).
          - `averageUtilization: 50`: Imposta l'utilizzazione media della CPU target al 50%.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo HPA, ecco alcuni esempi:

1. **Creazione dell'HPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato dell'HPA:**
   ```bash
   kubectl get hpa
   ```

3. **Monitoraggio degli eventi associati all'HPA:**
   ```bash
   kubectl describe hpa sample-hpa
   ```

4. **Risorse correlate al Deployment:**
   Puoi monitorare anche le repliche e il carico di lavoro del tuo Deployment associato all'HPA:
   ```bash
   kubectl get deployment sample
   kubectl get pods
   ```

L'HPA inizierà a regolare automaticamente il numero di repliche del tuo Deployment in base all'utilizzazione della CPU, mantenendo l'utilizzazione media al 50% come specificato nella configurazione. La finestra di stabilizzazione di 60 secondi specificata per il ridimensionamento verso l'alto aiuta a evitare oscillazioni indesiderate nel numero di repliche.


Cap 14/autoscaler/vpa.yaml
--------------------------
# DESCRIZIONE
Questo è un esempio di definizione di un Vertical Pod Autoscaler (VPA) in Kubernetes utilizzando YAML. Il VPA regola dinamicamente le richieste e i limiti delle risorse dei container all'interno di un pod. Ecco una spiegazione del codice:

- `apiVersion: autoscaling.k8s.io/v1beta2`: Specifica la versione dell'API del Vertical Pod Autoscaler.
- `kind: VerticalPodAutoscaler`: Definisce il tipo di oggetto, in questo caso, un Vertical Pod Autoscaler.
- `metadata`: Contiene i metadati associati all'oggetto, come il nome del VPA (`sample`).
- `spec`: Contiene le specifiche del VPA.
  - `targetRef`: Specifica il riferimento al quale il VPA si applica. In questo caso, il VPA è associato a un pod di tipo `v1` con il nome `sample`.
  - `resourcePolicy`: Specifica le politiche per le risorse dei container all'interno del pod.
    - `containerPolicies`: Specifica le politiche per i singoli container nel pod.
      - `- containerName: '*'`: Applica la politica a tutti i container nel pod.
        - `minAllowed`: Imposta i limiti minimi consentiti per le risorse.
          - `cpu: 100m`: Imposta il limite minimo CPU a 100 milliCPU (0.1 core).
          - `memory: 50Mi`: Imposta il limite minimo di memoria a 50 megabyte.
        - `maxAllowed`: Imposta i limiti massimi consentiti per le risorse.
          - `cpu: 1`: Imposta il limite massimo CPU a 1 core.
          - `memory: 500Mi`: Imposta il limite massimo di memoria a 500 megabyte.
        - `controlledResources: ["cpu", "memory"]`: Specifica quali risorse devono essere controllate, in questo caso, CPU e memoria.
  - `updatePolicy`: Specifica la politica di aggiornamento del pod.
    - `updateMode: Recreate`: Specifica che i pod devono essere ricreati durante l'aggiornamento delle risorse.

# COMANDI
Per quanto riguarda i comandi per utilizzare questo VPA, ecco alcuni esempi:

1. **Creazione del VPA:**
   ```bash
   kubectl apply -f nome-del-tuo-file.yaml
   ```

2. **Visualizzazione dello stato del VPA:**
   ```bash
   kubectl get vpa
   ```

3. **Monitoraggio degli eventi associati al VPA:**
   ```bash
   kubectl describe vpa sample
   ```

4. **Risorse correlate al Pod:**
   Puoi monitorare le risorse del tuo pod:
   ```bash
   kubectl get pod sample
   kubectl describe pod sample
   ```

Il VPA inizierà a regolare dinamicamente le richieste e i limiti delle risorse dei container nel pod in base alle politiche specificate. Inoltre, l'aggiornamento del pod sarà effettuato tramite ricreazione quando le risorse sono aggiornate. Questo aiuta a garantire che le modifiche alle risorse abbiano effetto durante la ricreazione del pod.


Cap 14/dr/backup-etcd.yaml
--------------------------
# DESCRIZIONE
Il codice fornito è una definizione di un lavoro periodico (CronJob) in Kubernetes, progettato per eseguire il backup di un'istanza di etcd. Di seguito una spiegazione del codice:

1. **`apiVersion: batch/v1beta1`**: Specifica la versione dell'API Kubernetes e il tipo di risorsa, che in questo caso è un lavoro periodico (`CronJob`).

2. **`kind: CronJob`**: Indica che questa risorsa è un lavoro periodico.

3. **`metadata`**: Contiene informazioni di base sulla risorsa, come il nome (`backup`) e il namespace (`kube-system`).

4. **`spec`**: Contiene la configurazione del lavoro periodico.

   - **`schedule: "* * * * *"`**: Specifica la pianificazione del lavoro periodico. In questo caso, il cron job viene eseguito ogni minuto (`* * * * *`).

   - **`jobTemplate`**: Definisce il template per i lavori effettivi che vengono eseguiti secondo la pianificazione.

     - **`spec`**: Contiene la specifica del pod che viene eseguito come lavoro.

       - **`template`**: Contiene la specifica del pod.

         - **`spec`**: Contiene la specifica dei container e delle risorse del pod.

           - **`containers`**: Contiene la definizione del container del pod.

             - **`name: backup`**: Il nome del container è "backup".

             - **`image: k8s.gcr.io/etcd:3.2.24`**: Specifica l'immagine del container, che è l'immagine di etcd.

             - **`env`**: Specifica le variabili d'ambiente per il container.

               - **`ETCDCTL_API`**: Imposta la versione dell'API etcd su 3.

             - **`command` e `args`**: Definiscono il comando e gli argomenti eseguiti nel container. In questo caso, viene eseguito un comando `etcdctl` per creare uno snapshot di etcd.

             - **`volumeMounts`**: Monta i volumi all'interno del container.

               - `/etc/kubernetes/pki/etcd`: Contiene i certificati etcd necessari per l'accesso sicuro.

               - `/backup`: Il percorso in cui verrà salvato lo snapshot di etcd.

           - **`volumes`**: Definisce i volumi utilizzati dal pod.

             - **`etcd-certs`**: Monta i certificati etcd dal nodo host nel pod.

             - **`backup`**: Monta la directory di backup dal nodo host nel pod.

       - **`restartPolicy: OnFailure`**: Specifica la politica di riavvio del pod. Il pod viene riavviato solo se il container fallisce.

       - **`hostNetwork: true`**: Abilita il networking dell'host per il pod.

5. **`volumes`**: Definisce i volumi utilizzati dal pod.

   - **`etcd-certs`**: Un volume hostPath che monta i certificati etcd dal nodo host.

   - **`backup`**: Un volume hostPath che monta la directory di backup dal nodo host.

6. Infine, c'è una piccola discrepanza nel tuo codice: la chiusura della parentesi di `env` dovrebbe essere prima di `command` e `args`.
La parentesi di chiusura per la sezione `env` dovrebbe trovarsi dopo la definizione delle variabili d'ambiente. Ecco la correzione:
Ecco la correzione:
```yaml
env:
  - name: ETCDCTL_API
    value: "3"
command: ["/bin/sh"]
args: ["-c", "etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /backup/etcd-snapshot-$(date +%Y-%m-%d_%H:%M:%S_%Z).db"]
```

In questo modo, la chiusura della parentesi per `env` è posizionata correttamente. Assicurati di applicare questa correzione prima di applicare il manifest al tuo cluster Kubernetes.

# COMANDI
Per eseguire questo lavoro periodico, devi applicare questo manifest al tuo cluster Kubernetes usando il comando `kubectl apply`:

```bash
kubectl apply -f nome-del-tuo-file.yaml
```

Assicurati di avere i permessi necessari per creare risorse nel namespace specificato.


Cap 14/kube-green/sleepinfo.yaml
--------------------------------
# DESCRIZIONE
Il codice fornito è un documento di definizione di risorsa Kubernetes scritto in formato YAML. Descrive un oggetto di tipo "SleepInfo" personalizzato, definito nel gruppo di risorse "kube-green.com" nella versione "v1alpha1". Questo oggetto è chiamato "esempio" e ha alcune specifiche definite sotto la chiave "spec".

Ecco una breve spiegazione delle chiavi nel documento:

- `apiVersion`: Specifica la versione dell'API per il tipo di risorsa. In questo caso, la versione è "kube-green.com/v1alpha1".
- `kind`: Specifica il tipo di risorsa. Qui, è "SleepInfo".
- `metadata`: Contiene i metadati associati all'oggetto, come il nome dell'oggetto ("esempio").
- `spec`: Contiene i dettagli specifici dell'oggetto "SleepInfo". Questi dettagli includono:
  - `weekdays`: Specifica i giorni della settimana in cui si desidera applicare le informazioni sul sonno. In questo caso, "1-5" indica i giorni lavorativi da lunedì a venerdì.
  - `sleepAt`: Indica l'ora a cui si vuole andare a dormire, qui è "19:00".
  - `wakeUpAt`: Indica l'ora a cui si vuole svegliarsi, qui è "07:00".
  - `timeZone`: Specifica il fuso orario in cui sono definite le ore di sonno, qui è "Europe/Rome".

# COMANDI
Per applicare questo documento YAML su un cluster Kubernetes, puoi utilizzare il comando `kubectl apply`. Assicurati di avere il client Kubernetes (`kubectl`) installato e configurato per il tuo cluster. Esegui il seguente comando:

```bash
kubectl apply -f nome-del-tuo-file.yaml
```

Sostituisci "nome-del-tuo-file.yaml" con il nome effettivo del tuo file YAML contenente la definizione di risorsa. Questo comando invierà la definizione di risorsa al cluster, e Kubernetes si occuperà di gestire l'istanza dell'oggetto "SleepInfo" come specificato nel documento YAML.

Puoi controllare lo stato dell'istanza dell'oggetto utilizzando il comando `kubectl get`:

```bash
kubectl get sleepinfo
```

Questo ti darà informazioni sulle istanze degli oggetti "SleepInfo" nel tuo cluster.


Cap 14/kube-green/sleepinfo2.yaml
---------------------------------
# DESCRIZIONE
Questo codice è ancora un documento di definizione di risorsa Kubernetes in formato YAML, simile al precedente, ma con alcune aggiunte. Sembrerebbe rappresentare un oggetto di tipo "SleepInfo" con il nome "working-hours". Di seguito, una spiegazione delle chiavi aggiuntive:

- `suspendCronJobs`: Un flag booleano che, se impostato su "true", indica che tutti i cron job dovrebbero essere sospesi durante le ore di sonno specificate. Questo potrebbe essere utile per ridurre l'esecuzione di attività pianificate durante le ore di sonno.

- `excludeRef`: Un elenco di riferimenti a risorse Kubernetes che dovrebbero essere escluse dall'effetto di sospensione. Nel tuo caso, viene specificato un singolo riferimento di tipo "Deployment" con apiVersion "apps/v1", nome "my-deployment". Ciò significa che il deployment chiamato "my-deployment" non sarà sospeso durante le ore di sonno.

# COMANDI
Per applicare questo documento YAML al tuo cluster Kubernetes, puoi utilizzare il comando `kubectl apply`, come spiegato nel messaggio precedente:

```bash
kubectl apply -f nome-del-tuo-file.yaml
```

Sostituisci "nome-del-tuo-file.yaml" con il nome effettivo del tuo file YAML.

Puoi controllare lo stato dell'istanza dell'oggetto "SleepInfo" utilizzando il comando `kubectl get`:

```bash
kubectl get sleepinfo
```

Questo ti darà informazioni sulle istanze degli oggetti "SleepInfo" nel tuo cluster.

Inoltre, puoi verificare lo stato specifico dell'istanza "working-hours" utilizzando:

```bash
kubectl get sleepinfo working-hours -o yaml
```

Questo comando restituirà i dettagli specifici dell'istanza "working-hours", inclusi eventuali flag aggiuntivi come `suspendCronJobs` e `excludeRef`.


Cap 14/limit e resourcequotas/limitrange.yaml
---------------------------------------------
# DESCRIZIONE
Il codice che hai fornito è una specifica di un oggetto `LimitRange` in Kubernetes. Questo oggetto è utilizzato per specificare vincoli sui limiti di risorse (come la CPU e la memoria) che possono essere richiesti e utilizzati da container all'interno di un namespace Kubernetes.

Ecco una spiegazione delle diverse parti del codice:

- `apiVersion: v1`: Specifica la versione dell'API Kubernetes utilizzata. In questo caso, si tratta della versione 1 dell'API.

- `kind: LimitRange`: Definisce il tipo di oggetto Kubernetes, che è un `LimitRange` in questo caso.

- `metadata`: Contiene metadati associati all'oggetto, come il nome (`name`) del `LimitRange`. In questo esempio, il nome è "cpu-resource-constraint".

- `spec`: Contiene la definizione effettiva dei limiti di risorse.

  - `limits`: Contiene un elenco di limiti predefiniti per le risorse. In questo caso, è specificato un limite per la CPU.

    - `default`: Il valore predefinito che verrà utilizzato se non viene specificato alcun valore per una richiesta o un limite di CPU. In questo caso, è 500 milliCPU (m significa milli, quindi 500m è 500 milliCPU).

    - `defaultRequest`: Il valore predefinito utilizzato se non viene specificato alcun valore per una richiesta di CPU.

    - `max`: Il valore massimo consentito per una richiesta o un limite di CPU. In questo caso, è "1", che rappresenta 1 CPU.

    - `min`: Il valore minimo consentito per una richiesta o un limite di CPU. In questo caso, è 100 milliCPU.

In termini pratici, questo `LimitRange` impone vincoli sulla CPU per i container all'interno del namespace a cui è applicato. Ad esempio, se un container non specifica una richiesta o un limite di CPU, verranno applicati i valori predefiniti. Se un container specifica una richiesta di CPU inferiore a 100 milliCPU o superiore a 1 CPU, il `LimitRange` lo modificherà per rispettare i limiti specificati.

# COMANDI
Per applicare questo `LimitRange` a un namespace, puoi creare un file YAML con questo contenuto e quindi applicarlo utilizzando il comando `kubectl apply`:

```bash
kubectl apply -f nome_file.yaml
```

Assicurati di sostituire "nome_file.yaml" con il nome effettivo del tuo file YAML. Una volta applicato, il `LimitRange` avrà effetto sui limiti di risorse nel namespace specificato.


Cap 14/limit e resourcequotas/resourcequota.yaml
------------------------------------------------
# DESCRIZIONE
Il codice che hai fornito è una specifica di un oggetto `ResourceQuota` in Kubernetes. Questo oggetto viene utilizzato per limitare le risorse totali (CPU e memoria) che possono essere utilizzate da tutti i container all'interno di un namespace Kubernetes. Vediamo una spiegazione delle diverse parti del codice:

- `apiVersion: v1`: Specifica la versione dell'API Kubernetes utilizzata, che è la versione 1 in questo caso.

- `kind: ResourceQuota`: Definisce il tipo di oggetto Kubernetes, che è un `ResourceQuota` in questo caso.

- `metadata`: Contiene metadati associati all'oggetto, come il nome (`name`) del `ResourceQuota` e il namespace a cui è associato (`namespace`). In questo esempio, il nome è "dev-quota" e il namespace è "dev".

- `spec`: Contiene la definizione effettiva dei limiti di risorse.

  - `hard`: Specifica i limiti massimi di risorse per il namespace.

    - `requests.cpu`: Specifica il limite massimo per le richieste di CPU a 500 milliCPU (500m).

    - `limits.cpu`: Specifica il limite massimo per le risorse di CPU a 1000 milliCPU (1000m).

    - `requests.memory`: Specifica il limite massimo per le richieste di memoria a 2 gigabyte (2G).

    - `limits.memory`: Specifica il limite massimo per le risorse di memoria a 4 gigabyte (4G).

In pratica, questo `ResourceQuota` impone vincoli sulle risorse totali (richieste e limiti di CPU e memoria) utilizzate da tutti i container all'interno del namespace "dev".

# COMANDI
Per applicare questo `ResourceQuota` al namespace "dev", puoi creare un file YAML con questo contenuto e quindi applicarlo utilizzando il comando `kubectl apply`:

```bash
kubectl apply -f nome_file.yaml
```

Assicurati di sostituire "nome_file.yaml" con il nome effettivo del tuo file YAML. Una volta applicato, il `ResourceQuota` avrà effetto sui limiti di risorse nel namespace specificato. Puoi verificare lo stato del `ResourceQuota` nel namespace "dev" utilizzando il comando:

```bash
kubectl describe resourcequota -n dev
```

Questo comando fornirà dettagli sulle risorse assegnate e utilizzate nel namespace "dev" rispetto ai limiti specificati nel `ResourceQuota`.


Cap 14/toleration e affinity/d-antiaffinity.yaml
------------------------------------------------
# DESCRIZIONE
Il codice che hai fornito è una specifica di un oggetto `Deployment` in Kubernetes. Questo oggetto viene utilizzato per gestire la distribuzione e la gestione di un'applicazione su nodi all'interno di un cluster Kubernetes. Vediamo una spiegazione delle diverse parti del codice:

- `apiVersion: apps/v1`: Specifica la versione dell'API Kubernetes utilizzata, che è la versione delle applicazioni.

- `kind: Deployment`: Definisce il tipo di oggetto Kubernetes, che è un `Deployment` in questo caso.

- `metadata`: Contiene metadati associati all'oggetto, come il nome (`name`) del `Deployment`.

- `spec`: Contiene la definizione effettiva del comportamento del `Deployment`.

  - `selector`: Specifica l'etichetta che il `Deployment` utilizzerà per selezionare i pod a cui applicare le modifiche o le azioni. In questo caso, seleziona i pod con l'etichetta `app: store`.

  - `replicas`: Specifica il numero desiderato di repliche dei pod. In questo caso, sono specificate 3 repliche.

  - `template`: Contiene la definizione del pod che il `Deployment` creerà.

    - `metadata`: Contiene le etichette del pod. In questo caso, il pod avrà l'etichetta `app: store`.

    - `spec`: Contiene la definizione dei contenitori all'interno del pod.

      - `affinity`: Specifica le regole di affinità per i pod. In questo caso, viene utilizzata l'affinità negativa (`podAntiAffinity`). I pod con l'etichetta `app: store` saranno programmati in modo che non siano nello stesso nodo (`requiredDuringSchedulingIgnoredDuringExecution`). La chiave di topologia (`topologyKey`) utilizzata per l'affinità è l'hostname del nodo Kubernetes.

      - `containers`: Contiene la definizione del contenitore all'interno del pod.

        - `name`: Il nome del contenitore, che è "redis-server" in questo caso.

        - `image`: La specifica dell'immagine del contenitore. In questo caso, l'immagine è "redis:3.2-alpine".

# COMANDI
Per applicare questo `Deployment` a un cluster Kubernetes, puoi creare un file YAML con questo contenuto e quindi applicarlo utilizzando il comando `kubectl apply`:

```bash
kubectl apply -f nome_file.yaml
```

Assicurati di sostituire "nome_file.yaml" con il nome effettivo del tuo file YAML. Una volta applicato, il `Deployment` inizierà a creare e gestire le repliche del pod conforme alla tua specifica.

Per controllare lo stato del `Deployment`, puoi utilizzare il comando:

```bash
kubectl get deployments
```

Per controllare lo stato dei pod associati al `Deployment`, puoi utilizzare il comando:

```bash
kubectl get pods -l app=store
```

Questo ti fornirà informazioni sui pod con l'etichetta `app: store`.


Cap 14/toleration e affinity/pod-toleration.yaml
------------------------------------------------
# DESCRIZIONE
Il codice che hai fornito è una specifica di un oggetto `Pod` in Kubernetes. Un `Pod` è la più piccola unità eseguibile in Kubernetes, che può contenere uno o più container. Vediamo una spiegazione delle diverse parti del codice:

- `apiVersion: v1`: Specifica la versione dell'API Kubernetes utilizzata, che è la versione 1.

- `kind: Pod`: Definisce il tipo di oggetto Kubernetes, che è un `Pod` in questo caso.

- `metadata`: Contiene metadati associati all'oggetto, come il nome (`name`) del `Pod` e le etichette (`labels`).

  - `name`: Il nome del `Pod`, che è "nginx" in questo caso.

  - `labels`: Etichette associate al `Pod`. In questo caso, è specificato che il `Pod` appartiene all'ambiente di test con l'etichetta `env: test`.

- `spec`: Contiene la definizione effettiva del `Pod`.

  - `containers`: Contiene la definizione dei container all'interno del `Pod`.

    - `name`: Il nome del container, che è "nginx" in questo caso.

    - `image`: La specifica dell'immagine del container. In questo caso, l'immagine è "nginx".

    - `imagePullPolicy`: Specifica la politica per il recupero dell'immagine. In questo caso, è impostato su "IfNotPresent", il che significa che Kubernetes userà l'immagine solo se non è già presente nel nodo.

  - `tolerations`: Specifica le tolleranze del `Pod`, che consentono di rilasciare il `Pod` su nodi che potrebbero altrimenti rifiutare il programma del `Pod`. In questo caso, è specificata una tolleranza:

    - `key`: La chiave per la tolleranza, che è "app".

    - `operator`: L'operatore per la tolleranza, che è "Exists". Questo significa che il `Pod` sarà tollerante rispetto al nodo solo se esiste la chiave specificata.

    - `effect`: L'effetto della tolleranza, che è "NoExecute". Ciò significa che se la tolleranza non è soddisfatta, il `Pod` non verrà eseguito (non eseguirà nuove operazioni).

    - `value`: Il valore associato alla chiave della tolleranza, che è "demo".

# COMANDI
Per applicare questo `Pod` a un cluster Kubernetes, puoi creare un file YAML con questo contenuto e quindi applicarlo utilizzando il comando `kubectl apply`:

```bash
kubectl apply -f nome_file.yaml
```

Assicurati di sostituire "nome_file.yaml" con il nome effettivo del tuo file YAML. Una volta applicato, il `Pod` verrà creato nel cluster.

Per controllare lo stato del `Pod`, puoi utilizzare il comando:

```bash
kubectl get pods
```

Questo ti fornirà informazioni sullo stato del `Pod` "nginx" nel tuo cluster.
