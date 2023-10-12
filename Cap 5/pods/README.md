# Descrizione

Esempi di file che definiscono i Pod di un cluster Kubernetes.

I dati sono puramente indicativi, così come le immagini utilizzate, e servono a mostrare la struttura del file.

> I dettagli riguardo il codice e il suo funzionamento sono riportati all'interno del libro edito da Apogeo:
> https://amzn.to/3AMpQj2
------------------------------------------------------------------------------------------------------
APPLICAZIONE PRATICA:

```bash
$ k apply -f pod-nginx.yaml
pod/nginx created

$ k apply -f pod-sidecar.yaml
pod/two-containers created

$ k get pods
NAME                       READY   STATUS              RESTARTS      AGE
nginx                      1/1     Running             0             51s
two-containers             0/2     ContainerCreating   0             6s

$ k exec -it two-containers -c nginx-container -- /bin/sh
#
```
