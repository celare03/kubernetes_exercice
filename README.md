# kubernetes_exercice

Le but de cet exercice est de s'entrainer avec les commandes kubectl.

Vous pouvez faire cet exercice avec votre cluster kubernetes local (MINIKUBE), avec RANCHER ou tout autre cluster KUBERNETES.


Cet exemple a été réalisé avec MINIKUBE.

# Installation de KUBECTL
voir le lien [kubectl](https://kubernetes.io/fr/docs/tasks/tools/install-kubectl/)

# Installation de MINIKUBE
Voir le lien [minikube](https://kubernetes.io/fr/docs/tasks/tools/install-minikube/)

# Créer une application WORDPRESS
Vous trouverez ci-dessous les diffénts opérateurs que nous allons utiliser:

1. PVC
2. Service
3. Config
4. Secret (TLS / Generic)
5. Deployment
6. Ingress

Nous créerons:
- deux déploiments (mysql et wordpress)
- un persistent volume pour persister les données au niveau de MYSQL (FACULTATIF)


## Nampespace (namespace.txt)
Créer le namespace dev

```shell
kubectl create namespace dev
```

## Persistent Volume :
Nous utilisons le storageclass **standard** de minikube.
Pour connaitre les storagesclass, lancer la commande:
```shell
kubectl get sc 
```


Créer un persistent volume (demande de 2Gi)
Vous avez le choix de créer :
- un persisent volume puis ensuite un persistantVolumeClaim ou de créer directement un persistentVolumeClaim via une storageClassName
 
 Dans notre exemple, nous créeons directement un PERSISTENTVOLUMECLAIM lié à une STORAGE CLASS.
 
 ## SERVICE 
 voir fichiers:
 - svc-wordpress.yaml
 - svc-mysql.yaml
 
 Créer un service pour chaque déploiment
 
| Deploiment     |   type de port  | Port |  
| -------------  | -------------   |:---: |
| WORDPRESS      |     nodeport    |33090 |  
| MYSQL          |     clusterIP   |3306  |    

Dans cet exemple, la base de données ne sera pas accessible à l'extérieur du cluster KUBERNETES
Nous pourrons accéder à WORDPRESS à l'extérieur du cluster à partir du nodeport

Lancer la commande:
``` shell
kubectl apply -f svc-wordpress.yaml
kubectl apply -f svc-mysql.yaml
```

## Configmap
voir fichier cmAndSecret.txt
Pour éviter d'entrer les informations manuellement dans le fichier yaml, nous créeons un configMap.
Au lieu d'utiliser un fichier YAML, nous utiliserons l'écriture impérative.

```shell
kubectl create configmap generic cm_wordpress --from-literal=WORDPRESS_DB_HOST=mysql
```

## Secret
### WORDPRESS_DB_PASSWORD
kubectl create secret generic s-mysql --from-literal=MYSQL_ROOT_PASSWORD=plrtspa

### MYSQL_ROOT_PASSWORD
kubectl create secret generic s-wordpress --from-literal=WORDPRESS_DB_PASSWORD=plrtspa

## Deploiment

### MYSQL
Version: **5.7**
Si vous le souhaitez, vous pouvez utiliser une autre version.
Pour la création du déploiment, nous utiliserons la commande --record pour visualiser l'historique

Dans le déploiement, nous ajouterons:
- PVC (si créé)
- SECRET

Lancer la commande:
``` shell
kubectl apply -f mysql.yaml --record
```

#### Vérification de l'historique de déploiment

```shell
kubectl rollout history deployment.v1.apps/mysql -n dev
``` 


### WORDPRESS
Version: **wordpress:4.9-apache**.
Si vous le souhaitez, vous pouvez utiliser une autre version.
Pour la création du déploiment, nous utiliserons la commande --record pour visualiser l'historique

Dans le déploiement, nous ajouterons:
- CONFIGMAP
- SECRET

Lancer la commande:
``` shell
kubectl apply -f wordpress.yaml --record
```


#### Vérification de l'historique de déploiment
```shell
kubectl rollout history deployment.v1.apps/wordpress -n dev
```

## Acces à l'application
Lancer la commande ci-dessous pour récupérer l'adresse du cluster MINIKUBE

```shell
minikube ip
```

Pour récupérer le port du service svc-wordpress du namespace *dev*:
```shell
kubect get services -n dev
```
Ouvrir votre navigateur et indiquer l'adresse de la commande ci-dessus:

http://minikupe_ip:svc_wodpress

Il faut remplacer les variables par les valeurs retournées.

## ERREUR
Lors du lancement du deploiement de MYSQL, dans les logs de la console ou via la commande:
``` shell
kubectl log <nomPod> -n dev
```
 vous avez l'erreur ***[Entrypoint] MySQL Docker Image 5.7.21-1.1.3
[Entrypoint] Initializing database
2018-06-14T20:59:11.481713Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-06-14T20:59:11.484034Z 0 [ERROR] --initialize specified but the data directory has files in it. Aborting.
2018-06-14T20:59:11.484087Z 0 [ERROR] Aborting***

Créer un pod debug qui accédera au pvc (voir pod my-pvc-debugger.yaml)
lancer la commande pour se connecter au container:
```shell
kubectl get pod -n dev
kubectl exec -ti podname -n dev -- sh
cd /data
ls -l
```
Dans le pvc, vous trouverez un dossier qui se nomme "lost+found".
Supprimer-le et relancer le déploiement de mysql.

