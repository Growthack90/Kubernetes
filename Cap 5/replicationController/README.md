# Descrizione

Esempi di file che definiscono i ReplicationController di un cluster Kubernetes.

I dati sono puramente indicativi, così come le immagini utilizzate, e servono a mostrare la struttura del file.

> I dettagli riguardo il codice e il suo funzionamento sono riportati all'interno del libro edito da Apogeo:
> https://amzn.to/3AMpQj2
-----------------------------------------------------------------------------------------------------------------
> Cap 5/replicationController/rc-nginx.yaml

# DESCRIZIONE
Il codice è un frammento di un file di configurazione in formato YAML per creare un oggetto di tipo ReplicationController in Kubernetes. 
Questo frammento definisce un ReplicationController chiamato "nginx" che gestisce un'applicazione di tipo Nginx con 3 repliche.
In sintesi, questo codice configura un ReplicationController che garantisce che ci siano sempre 3 repliche in esecuzione dell'applicazione Nginx. 
Ogni replica utilizza l'immagine di Nginx e espone il contenitore sulla porta 80. 
Le repliche sono selezionate in base all'etichetta app: nginx.

Ecco una spiegazione dettagliata del codice:

`apiVersion: v1`: Questo campo specifica la versione dell'API di Kubernetes che verrà utilizzata per interpretare questo oggetto. In questo caso, si tratta della versione "v1" dell'API, che è la versione principale.

`kind`: ReplicationController: Questo campo specifica il tipo di oggetto che stai definendo, che è un ReplicationController. Un ReplicationController è un oggetto di Kubernetes che garantisce un numero specifico di repliche di un'istanza di un'applicazione in esecuzione.

`metadata`: Questa sezione contiene metadati per l'oggetto, come il nome del ReplicationController.

`name`: nginx: Questo è il nome del ReplicationController, che è "nginx" in questo caso.

`spec`: Questa sezione definisce le specifiche del ReplicationController, cioè come deve comportarsi.

`replicas: 3`: Qui si specifica che il ReplicationController deve garantire che ci siano sempre 3 repliche in esecuzione dell'applicazione. Se il numero di repliche dovesse scendere al di sotto di 3, il ReplicationController creerà automaticamente nuove repliche.

`selector`: Questa sezione definisce un criterio di selezione per le repliche che il ReplicationController deve gestire. In questo caso, le repliche sono selezionate in base a un'etichetta (app: nginx). Le repliche che abbiano questa etichetta saranno gestite da questo ReplicationController.

`template`: Qui si specifica il modello per le pod che il ReplicationController deve creare.

`metadata`: Questa sezione definisce i metadati per le pod create dal ReplicationController. Il nome è "nginx" e le etichette sono impostate su app: nginx.

`spec`: Questa sezione definisce le specifiche delle pod create.

`containers`: Qui si definisce il contenitore che verrà eseguito all'interno di ciascuna pod.

`name: nginx`: Il nome del contenitore è "nginx".

`image: nginx`: Questo specifica l'immagine del contenitore da utilizzare, che è "nginx" in questo caso. Quindi, ogni pod creata da questo ReplicationController eseguirà un contenitore Nginx.

`ports`: Qui si specifica la porta su cui la pod del contenitore deve essere esposta.

`containerPort: 80`: La pod esporrà il contenitore sulla porta 80.

------------------------------------------------------------------------------------------------------
# ESEMPIO PRATICO:

Applica il file YAML al tuo cluster Kubernetes:
```bash
$ kubectl apply -f rc-nginx.yaml
```

Verifica lo stato del ReplicationController:
```bash
$ kubectl get replicationcontroller nginx
```

Puoi vedere le pod create dal ReplicationController:
```bash
$ kubectl get pods -l app=nginx
```

Per esporre le pod all'esterno del cluster, puoi creare un servizio di tipo LoadBalancer. 
Ad esempio, puoi creare un servizio LoadBalancer con il seguente file YAML (chiamato nginx-service.yaml):
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
  type: LoadBalancer
```

Applica il servizio LoadBalancer con il comando kubectl apply:

```bash
$ kubectl apply -f nginx-service.yaml
```
Il servizio creerà un bilanciatore di carico che espone le pod Nginx all'esterno del cluster sulla porta 80.

Ora puoi accedere all'applicazione Nginx da un browser o da un client utilizzando l'indirizzo IP del servizio LoadBalancer fornito dal tuo provider cloud.
Verifica lo stato del servizio LoadBalancer eseguendo il comando:
```bash
$ kubectl get service nginx-service
```
Attendere un momento finché il servizio non riceverà un indirizzo IP esterno assegnato (questo può richiedere un po' di tempo, a seconda della tua infrastruttura cloud).

Una volta che il servizio ha un indirizzo IP esterno assegnato, puoi accedere all'applicazione Nginx utilizzando questo indirizzo IP e la porta 80. 
Ad esempio, se l'indirizzo IP esterno assegnato fosse EXTERNAL_IP, puoi accedere all'applicazione Nginx da un browser o da un client utilizzando http://EXTERNAL_IP.
Assicurati che il tuo ambiente Kubernetes sia configurato correttamente con un provider cloud che supporta i servizi LoadBalancer per rendere funzionante questo esempio.


