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
 
 Créer un service pour chaque déploiment
 
| Deploiment     |     2 header    |  
| -------------  |: -------------: | 
| WORDPRESS      |     nodeport    |    
| MYSQL          |     clusterIP   |      
