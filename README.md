# kubernetes_exercice

Le but de cet exercice est de s'entrainer avec les commandes kubectl.

Vous pouvez faire cet exercice avec votre cluster kubernetes local (MINIKUBE), avec RANCHER ou tout autre cluster KUBERNETES.
Pour les autres cluster KUBERNETES, je ne pourrais pas vous aider car je n'ai pas d'expérience.

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

## Persistent Volume :
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
| -------------  | :-------------: |      |
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


