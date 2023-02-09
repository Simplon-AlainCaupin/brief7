# Executive Summary

* Deployer outil de CI/CD

**Choix de l'outil** : 
AzureDevops,
Permet de rester dans un environnement "Azure", théoriquement réduit les risques d'incompatibilités.

**Autres outils** : 
repo gitHub pour les manifestes Kubernetes et AzureDevops,
Plateforme Azure pour le déploiement des ressources nécessaires
Azure Pricing Calculator pour la gestion des coûts
https://azure.microsoft.com/en-us/pricing/calculator/

**Explication Azure Pipeline / CICD** :  

Le principe, dans notre cas, du "continuous delivery" est d'assurer la stabilité d'une application, en faisant en sorte que la version la plus récente soit déployée en temps réel.  
Dans une situation plus pratique, cela permet de respecter les normes de conformité en s'assurant que certaines applications restent en version stable, avec le moins de failles possibles (MCO/MCS)  

Azure Devops et le pipeline créé via cet outil permettent donc de faire une vérification de version et la mise à jour d'un node en déployant, ou pas selon la version, le container de l'application depuis DockerHub.

**Gestion de l'application / Infra** :  

Un cluster AKS a été déployé avec un ingress,
Sur 2 nodes ont été déployés l'application et une base redis, ainsi que le secret redis via un manifeste en local
L'application est accessible via l'ingress de l'application gateway du cluster AKS et le fqdn associé.
Un certificat TLS a été mis en place, intégré dans l'infra via un écouteur sur le port 443 et une règle de redirection associée.
Le ceertificat a été déployé via un manifeste en local, encodé en base64
