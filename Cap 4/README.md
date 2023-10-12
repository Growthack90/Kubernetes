# Descrizione

"Hello world" realizzato con l'immagine _docker/getting-started_ disponibile su DockerHub tramite un Deployment e un 
Service.

# Installazione

```
kubectl apply -f helloworld.yaml
```
APPLICAZIONE PRATICA

```bash
$ k apply -f hello-world.yaml
deployment.apps/tutorial created
service/tutorial created

$ k get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
tutorial   1/1     1            1           10s

$ k get pods
NAME                       READY   STATUS     RESTARTS   AGE
tutorial-5cd466d7b-68fd8   1/1     Running    0          15s

$ k exec -it tutorial-5cd466d7b-68fd8 -c tutorial -- /bin/sh
/ #
```
