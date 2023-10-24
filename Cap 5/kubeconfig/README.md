# Descrizione

Esempi di file _kubeconfig_ di un cluster Kubernetes.

I dati sono puramente indicativi, servono a mostrare la struttura del file.

> I dettagli riguardo il codice e il suo funzionamento sono riportati all'interno del libro edito da Apogeo:
> https://amzn.to/3AMpQj2
------------------------------------------------------------------------------------------------------------------
> Cap 5/kubeconfig/kubeconfig-esempio.yaml

# DESCRIZIONE
Il file yaml è un file di configurazione di Kubernetes noto come "kubeconfig". 
Questo file viene utilizzato per definire variabili di configurazione per l'interazione con un cluster 
Kubernetes specifico. 
Il tuo file sembra avere una configurazione molto semplice, con un cluster, un contesto e un utente definiti. 
Di seguito, spiegherò brevemente le sezioni chiave del tuo file:

1. `apiVersion`: Specifica la versione del file di configurazione. In questo caso, è impostato su "v1", che è la versione principale del kubeconfig.

2. `clusters`: Definisce una lista di cluster Kubernetes a cui il tuo kubeconfig può fare riferimento. Nell'esempio, è definito un unico cluster chiamato "my-cluster". Questo cluster è configurato con un server e un'autorità di certificazione.

3. `contexts`: Definisce una lista di contesti che collegano cluster e utenti. In questo caso, c'è un contesto chiamato "my-cluster" che utilizza il cluster "my-cluster" e l'utente "myuser".

4. `current-context`: Specifica il contesto predefinito da utilizzare quando si interagisce con il cluster. Nel tuo caso, il contesto predefinito è "my-cluster".

5. `kind`: Indica il tipo di oggetto. In questo caso, è "Config", il che significa che questo è un file di configurazione Kubernetes.

6. `preferences`: Questa sezione può essere utilizzata per specificare preferenze personalizzate per il tuo ambiente, ma nel tuo esempio è vuota.

7. `users`: Definisce una lista di utenti che possono essere utilizzati con il tuo kubeconfig. Nell'esempio, c'è un unico utente chiamato "myuser" con un campo "token", che potrebbe essere utilizzato per l'autenticazione tramite token.

Tutto sommato, il tuo kubeconfig configura l'accesso a un cluster Kubernetes chiamato "my-cluster" utilizzando l'utente "myuser" e specificando il server e l'autorità di certificazione. Questo kubeconfig può essere utilizzato con il comando `kubectl` o altre utilità per interagire con il cluster "my-cluster". Assicurati di avere il token o le credenziali necessarie per autenticarti con successo.

Questo file viene utilizzato per definire variabili di configurazione per l'interazione 
con un cluster Kubernetes specifico.
Tutto sommato, il tuo kubeconfig configura l'accesso a un cluster Kubernetes chiamato "my-cluster" 
utilizzando l'utente "myuser" e specificando il server e l'autorità di certificazione. 
Questo kubeconfig può essere utilizzato con il comando kubectl o altre utilità per interagire 
con il cluster "my-cluster". 
Assicurati di avere il token o le credenziali necessarie per autenticarti con successo.

__Nota:__ Assicurati che il campo certificate-authority-data nel tuo cluster sia correttamente codificato 
in base64. È importante che i dati siano codificati in base64 in modo corretto e che non ci siano 
spazi bianchi o caratteri speciali accidentali.

Il valore base64_encoded_certificate_data nella sezione certificate-authority-data del tuo kubeconfig è privato 
e rappresenta il certificato dell'autorità di certificazione (CA) utilizzato per autenticarsi presso 
il cluster Kubernetes. Questo certificato contiene informazioni sensibili e dovrebbe essere trattato come 
informazione riservata. Esponendo il certificato dell'autorità di certificazione, qualcuno potrebbe 
potenzialmente impersonare il tuo cluster o ottenere l'accesso non autorizzato.
Assicurati di mantenere il valore di certificate-authority-data nel tuo kubeconfig riservato e di condividerlo 
solo con le persone autorizzate.

**Come ottenere il base64_encoded_certificate_data?** 
Il valore `base64_encoded_certificate_data` nella sezione `certificate-authority-data` del tuo kubeconfig 
dovrebbe essere fornito a te dalla persona o dall'amministratore che ha configurato il cluster Kubernetes. 
Di solito, questo valore rappresenta il certificato dell'autorità di certificazione (CA) del cluster e 
viene utilizzato per verificare l'autenticità del server Kubernetes quando ci si connette a esso.
Per ottenere questo valore, segui questi passi:

1. Chiedi all'amministratore del cluster Kubernetes di fornirti il certificato dell'autorità di certificazione (CA) e di indicarti come codificarlo in base64. Di solito, il CA è un file con estensione ".crt".

2. Una volta in possesso del certificato CA, puoi codificarlo in base64 utilizzando uno strumento come `base64`. Ecco un esempio di come farlo:
```bash
cat ca_certificate.crt | base64
```
Questo genererà la rappresentazione in base64 del certificato CA. Copia questo valore e utilizzalo nella sezione `certificate-authority-data` del tuo kubeconfig.

3. Inserisci il valore in base64 nel tuo kubeconfig sotto `certificate-authority-data`:
```yaml
certificate-authority-data: base64_encoded_certificate_data
```
Dopo aver aggiornato il tuo kubeconfig con il valore corretto in base64 del certificato CA, dovresti essere in grado di utilizzare il kubeconfig per connetterti al cluster in modo sicuro. Assicurati di tenere il tuo kubeconfig e il certificato CA al sicuro e di condividerli solo con persone autorizzate.

# ESEMPIO PRATICO
Non bisogna applicare il tuo kubeconfig come se fosse un oggetto Kubernetes. 
Tuttavia, il kubeconfig è un file di configurazione per l'accesso ai cluster Kubernetes e non è destinato 
a essere applicato direttamente con `kubectl apply`.
Per utilizzare un kubeconfig con `kubectl`, è necessario specificare il kubeconfig come file di configurazione 
quando si eseguono comandi. 
Ecco come farlo:

1. Per ottenere il tuo kubeconfig dal repository GitHub, puoi scaricarlo localmente con `curl` o `wget` 
o utilizzare un client Git. 
Ad esempio, con `curl`:
```bash
$ curl -O <raw_url>
```

2. Ora puoi utilizzare il kubeconfig come file di configurazione specificando l'opzione `--kubeconfig` 
in tutti i comandi `kubectl`. 
Ad esempio, per visualizzare i nodi con il tuo kubeconfig:
```bash
$ kubectl get nodes --kubeconfig=kubeconfig-esempio.yaml
```
Assicurati che il tuo kubeconfig sia nella directory corrente o fornisci il percorso completo al file se 
si trova altrove. 
Non è necessario applicare il kubeconfig come risorsa Kubernetes, poiché è già utilizzato per configurare 
l'accesso al cluster.

Ecco un esempio pratico di come utilizzare il tuo kubeconfig per interagire con un cluster 
Kubernetes utilizzando il comando `kubectl`. 
Assumeremo che il tuo kubeconfig sia memorizzato in un file chiamato "kubeconfig-esempio.yaml". 
Assicurati di aver configurato il tuo kubeconfig con le credenziali corrette prima di eseguire questi comandi.

1. Verifica la configurazione del tuo kubeconfig:
```bash
$ kubectl config view --kubeconfig-esempio.yaml
```
Questo comando visualizzerà la configurazione del tuo kubeconfig, inclusi i cluster, i contesti e gli 
utenti definiti nel file.

2. Imposta il contesto predefinito per il tuo kubeconfig (nel tuo caso, "my-cluster"):
```bash
$ kubectl config use-context my-cluster --kubeconfig=kubeconfig-esempio.yaml
```
Questo comando imposta il contesto predefinito nel tuo kubeconfig in modo da poter interagire con il 
cluster "my-cluster".

3. Verifica il contesto attualmente utilizzato:
```bash
$ kubectl config current-context --kubeconfig=kubeconfig-esempio.yaml
```
Questo comando dovrebbe restituire "my-cluster" come contesto attualmente utilizzato.

4. Esegui comandi su cluster Kubernetes:
Ora che hai impostato il contesto predefinito, puoi eseguire comandi `kubectl` per interagire con il 
cluster "my-cluster". 
Ad esempio, per ottenere una lista di nodi nel cluster:
```bash
$ kubectl get nodes
```
Questo comando restituirà la lista dei nodi nel cluster "my-cluster".

Ricorda che dovrai avere i permessi e le credenziali corrette per eseguire con successo i comandi sul cluster. 
Il tuo kubeconfig definisce solo le impostazioni di configurazione, mentre l'autenticazione effettiva 
potrebbe richiedere l'uso di token, chiavi SSH o altri metodi, a seconda della configurazione del cluster.

------------------------------------------------------------------------------------------------------------------
> Cap 5/kubeconfig/kubeconfig-esempio-cluster-multipli.yaml

# DESCRIZIONE
Il file di configurazione che hai pubblicato è un file di configurazione di Kubernetes nel formato YAML, di solito memorizzato in un file chiamato `kubeconfig`. Questo file di configurazione specifica informazioni sui cluster, i contesti e gli utenti di Kubernetes. Ecco una descrizione dettagliata degli elementi chiave di questa configurazione:

1. `apiVersion`: Specifica la versione del formato del file di configurazione di Kubernetes. In questo caso, è impostato su `v1`, indicando che si tratta del formato standard di configurazione.

2. `clusters`: Definisce un elenco di cluster di Kubernetes. Ogni cluster include le seguenti informazioni:
   - `name`: Un nome leggibile dall'utente per il cluster.
   - `cluster`: Specifica dettagli specifici del cluster.
     - `certificate-authority-data`: Dati dell'autorità di certificazione in formato Base64 per la verifica del certificato del server del cluster.
     - `server`: L'URL del server dell'API di Kubernetes per il cluster.

   In questa configurazione, sono definiti due cluster: `my-cluster` e `docker-desktop`. Entrambi i cluster sembrano utilizzare lo stesso URL del server, `https://kubernetes.docker.internal:6443`, ma hanno nomi diversi e potenzialmente dati di autorità di certificazione diversi.

3. `contexts`: Definisce un elenco di contesti, che vengono utilizzati per associare utenti e cluster. Ogni contesto include le seguenti informazioni:
   - `name`: Un nome leggibile dall'utente per il contesto.
   - `context`: Specifica dettagli specifici del contesto.
     - `cluster`: Il nome del cluster dalla sezione `clusters`.
     - `user`: Il nome dell'utente dalla sezione `users`.

   In questa configurazione, sono definiti due contesti: `my-cluster` e `docker-desktop`. Il contesto `my-cluster` associa il cluster `my-cluster` all'utente `myuser`, mentre il contesto `docker-desktop` associa il cluster `docker-desktop` all'utente `docker-desktop`.

4. `current-context`: Specifica quale contesto è attualmente attivo. In questo caso, il contesto attivo è `my-cluster`.

5. `kind`: Indica il tipo di file di configurazione. Dovrebbe sempre essere impostato su "Config" per un file kubeconfig.

6. `preferences`: Questa sezione è attualmente vuota, ma può essere utilizzata per memorizzare le preferenze dell'utente per kubectl.

7. `users`: Definisce un elenco di utenti di Kubernetes. Ogni utente include le seguenti informazioni:
   - `name`: Un nome amichevole per l'utente.
   - `user`: Specifica dettagli specifici dell'utente.
     - `token`: Token di autenticazione o token di autorizzazione per l'autenticazione dell'utente.
     - `client-certificate-data`: Dati del certificato del client in formato Base64 (usati per l'autenticazione del client).
     - `client-key-data`: Dati della chiave privata del client in formato Base64 (usati per l'autenticazione del client).

   In questa configurazione, sono definiti due utenti: `myuser` e `docker-desktop`. `myuser` utilizza un token per l'autenticazione, mentre `docker-desktop` utilizza dati di certificato client e dati di chiave privata client per l'autenticazione.

Nel complesso, questo file di configurazione consente di passare tra diversi cluster e utenti quando si utilizza `kubectl`, lo strumento da riga di comando di Kubernetes, impostando il `current-context` per specificare quale contesto si desidera utilizzare.

----------

Analogamente, configurazione tramite comandi:

Per ottenere una configurazione kubeconfig simile a quella che hai fornito, puoi seguire i seguenti passi:

1. **Installare Kubernetes**: Assicurati di avere Kubernetes installato nel tuo ambiente. Se stai utilizzando Kubernetes su Docker Desktop, dovresti già avere un cluster locale configurato.

2. **Ottenere i dati del cluster**: Avrai bisogno di ottenere i dati del cluster, in particolare il server API URL e il certificato dell'autorità di certificazione, per ciascun cluster a cui desideri accedere. Questi dati sono specifici per il cluster e dipendono dalla tua configurazione.

3. **Ottenere i dati dell'utente**: Per ciascun utente che vuoi configurare, dovrai ottenere le informazioni di autenticazione specifiche, come i token o i certificati client e le chiavi private.

4. **Creare il file kubeconfig**: Puoi creare il file kubeconfig manualmente utilizzando un editor di testo o generare un kubeconfig utilizzando lo strumento `kubectl`. Ecco un esempio di come puoi generare un kubeconfig utilizzando `kubectl`:
   ```bash
   $ kubectl config set-cluster my-cluster --server=https://kubernetes.docker.internal:6443 --certificate-authority=/path/to/ca.crt
   $ kubectl config set-credentials myuser --token=www
   $ kubectl config set-context my-cluster --cluster=my-cluster --user=myuser
   $ kubectl config use-context my-cluster
   ```

   Nell'esempio sopra, abbiamo creato un contesto chiamato `my-cluster` che fa riferimento al cluster `my-cluster` e all'utente `myuser`. Sostituisci `/path/to/ca.crt` con il percorso corretto del certificato dell'autorità di certificazione del tuo cluster.

5. **Salvare il kubeconfig**: Dopo aver creato o modificato il tuo kubeconfig, salvalo in un file. Di solito, il file si chiama `kubeconfig` o `config`, ma puoi usarne uno qualsiasi con un nome significativo. Assicurati che il tuo kubeconfig sia salvato nella directory predefinita `~/.kube/` o impostato la variabile d'ambiente `KUBECONFIG` per puntare al tuo file kubeconfig.

6. **Testare la configurazione**: Puoi testare la configurazione eseguendo comandi `kubectl` e assicurandoti che siano in grado di comunicare con i cluster specificati nel tuo kubeconfig.

Ricorda che i dettagli specifici della configurazione dipenderanno dalla tua installazione e dai cluster a cui stai cercando di accedere. Assicurati di avere le informazioni corrette relative ai tuoi cluster e utenti.

Per verificare la configurazione:
```bash
$ k config view
```

# ESEMPIO PRATICO
__Nota:__ Vedi anche "Cap 5/kubeconfig/kubeconfig-esempio.yaml".

Ecco un esempio pratico su come utilizzare questo file di configurazione di Kubernetes (`kubeconfig`) con `kubectl`, lo strumento da riga di comando di Kubernetes:

1. Per vedere l'elenco dei contesti disponibili nel tuo file di configurazione, puoi eseguire il seguente comando:
```bash
$ kubectl config get-contexts
```
Questo comando restituirà un elenco di contesti disponibili, in questo caso `my-cluster` e `docker-desktop`.

2. Per cambiare il contesto attivo, puoi utilizzare il comando `kubectl config use-context` seguito dal nome del contesto desiderato. Ad esempio, per passare al contesto `docker-desktop`, esegui il seguente comando:
```bash
$ kubectl config use-context docker-desktop
```
Ora sei passato al contesto `docker-desktop`.

3. Una volta che hai impostato il contesto, puoi iniziare a utilizzare `kubectl` per interagire con il cluster associato a quel contesto. Ad esempio, per ottenere l'elenco dei nodi nel cluster `docker-desktop`, esegui:
```bash
$ kubectl get nodes
```

4. Puoi anche tornare al contesto precedente in qualsiasi momento utilizzando il comando `kubectl config use-context` con il nome del contesto desiderato. Ad esempio, per tornare al contesto `my-cluster`, esegui:
```bash
$ kubectl config use-context my-cluster
```
Ora sei tornato al contesto `my-cluster` e puoi continuare a interagire con quel cluster.

Questo è solo un esempio di come puoi utilizzare il file di configurazione di Kubernetes (`kubeconfig`) per passare tra diversi cluster e contesti mentre lavori con `kubectl`. 
Il file di configurazione può contenere molte altre configurazioni personalizzate in base alle esigenze del tuo ambiente di sviluppo e delle tue applicazioni.

------------------------------------------------------------------------------------------------------------------
> Cap 5/kubeconfig/kubeconfig-esempio-docker-desktop.yaml

# DESCRIZIONE
La configurazione è un file di configurazione di Kubernetes (solitamente denominato `kubeconfig`) che definisce un cluster, un contesto e un utente per l'accesso a un cluster Kubernetes. Questa configurazione sembra essere per l'ambiente locale di Docker Desktop.
Il file di configurazione tipicamente trovato nella directory `~/.kube/config` o utilizzato da strumenti di gestione di Kubernetes come `kubectl`. 
Questo file definisce cluster, contesti e utenti per interagire con un cluster Kubernetes. 
Ecco una spiegazione dei componenti presenti nella tua configurazione:
- `apiVersion`: Questo campo specifica la versione dell'API di configurazione di Kubernetes. Nella tua configurazione è impostato su `v1`, che è la versione standard della configurazione.
  
- `clusters`: Questa sezione definisce i cluster a cui è possibile connettersi. Nella tua configurazione, c'è un unico cluster chiamato `docker-desktop`. Questo include i seguenti dettagli:
  - `cluster`: Specifica i dettagli di configurazione del cluster.
    - `certificate-authority-data`: Questo campo contiene tipicamente i dati del certificato CA (Certificate Authority) codificati in Base64 utilizzati per l'autenticazione presso il cluster Kubernetes.
    - `server`: Questo campo indica l'URL del server API del cluster Kubernetes.

- `contexts`: Questa sezione definisce contesti nominati, che consentono di passare tra diverse configurazioni di cluster e utente. Nella tua configurazione c'è un unico contesto chiamato `docker-desktop`. Questo associa un cluster e un utente a questo contesto.

- `current-context`: Questo campo specifica il contesto attualmente attivo. Nella tua configurazione è impostato su `docker-desktop`, il che significa che il contesto `docker-desktop` è attualmente in uso.
  
- `kind`: Questo campo specifica il tipo di configurazione, che è "Config" nel tuo caso.
  
- `preferences`: Questa sezione può contenere diverse preferenze specifiche dell'utente, ma è vuota nella tua configurazione.
  
- `users`: Questa sezione definisce gli utenti con le loro credenziali. Nella tua configurazione c'è un unico utente chiamato `docker-desktop`. Questo include i seguenti dettagli:
  - `name`: Il nome dell'utente, utilizzato per fare riferimento a questo utente in un contesto.
  - `user`: Specifica le credenziali dell'utente.
    - `client-certificate-data`: Tipicamente contiene i dati del certificato del client codificati in Base64 utilizzati per l'autenticazione.
    - `client-key-data`: Contiene la chiave privata del client codificata in Base64 utilizzata per l'autenticazione.

Tieni presente che i valori `xxx` e `yyy` nella tua configurazione sono segnaposto per i dati effettivi del certificato e della chiave codificati in Base64. Per lavorare con questa configurazione, dovrai sostituire `xxx` e `yyy` con i dati effettivi del certificato e della chiave, che di solito sono forniti dall'amministratore del cluster Kubernetes.

# ESEMPIO PRATICO
Nota: Vedi anche "Cap 5/kubeconfig/kubeconfig-esempio.yaml".

Supponiamo che tu voglia eseguire un comando `kubectl` per interagire con il cluster Docker Desktop. Ecco come puoi farlo utilizzando la configurazione fornita:

1. Elencare i cluster disponibili:
   ```
   $ kubectl config get-contexts
   ```
   Questo comando ti mostrerà una lista dei contesti disponibili nel tuo file di configurazione. Il tuo contesto è denominato "docker-desktop" poiché è definito nel tuo file `kubeconfig`.

2. Imposta il contesto attuale sul tuo cluster Docker Desktop:
   ```
   $ kubectl config use-context docker-desktop
   ```
   Questo imposterà il contesto attuale sul cluster Docker Desktop.

3. Ora puoi eseguire comandi `kubectl` come se fossi connesso al tuo cluster Docker Desktop. Ad esempio, per visualizzare i nodi del cluster:
   ```
   $ kubectl get nodes
   ```

4. Puoi anche specificare ulteriori opzioni di contesto, cluster e utente quando esegui i comandi `kubectl`:
   ```
   $ kubectl --context=docker-desktop get pods
   ```
   Questo eseguirà il comando `kubectl` specificando esplicitamente il contesto "docker-desktop".

Quando usi `kubectl`, esso utilizzerà automaticamente i certificati e le chiavi specificati nella configurazione per autenticarti al cluster Docker Desktop. Questo è utile quando si lavora con più cluster Kubernetes, poiché puoi avere una configurazione separata per ciascun cluster e passare tra di essi facilmente.
