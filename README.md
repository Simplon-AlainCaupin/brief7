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
