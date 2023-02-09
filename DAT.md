# Brief 7 DAT - Continuous Delivery

Schématisation du fonctionnement du pipeline DevOps :  

![](https://i.imgur.com/wGbm8CO.png)  

Resources :  

- **Kubernetes**

![](https://i.imgur.com/xeFhzT2.png)

Comprend le déploiement de l'application / loadbalancer / bdd redis / pvclaim

- **Ressources Azure** : 

![](https://i.imgur.com/7HATVYU.png)

Comprend le cluster Kubernetes hostant l'application et Redis sur 2 nodes  
l'application gateway pour redirection vers FQDN  
IP publique de l'application gateway  

- **Pipeline AzureDevops + Connexions**


![](https://i.imgur.com/tCVBiTp.png)  

![](https://i.imgur.com/eDGBNuE.png)  

# Déroulement du pipeline :  

1 - définition de la récurrence (CRON tab)
2 - déploiment de la version actuelle de l'application
3 - après premier déploiement, à chaque occurence : 
<ol>
vérification de la version,
<br>
si version différente, re-déploiement de l'application, 
<br>sinon, fin de l'exécution  
</ol>

```
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
- main

#CRON à modifier suivant récurrence voulue

schedules:
- cron: "0 18 1 1 *"
  displayName: repetition
  branches:
    include:
    - main
  always: true

pool:
  vmImage: ubuntu-latest

steps:
- task: Kubernetes@1
  inputs:
    connectionType: 'Kubernetes Service Connection'
    kubernetesServiceEndpoint: 'connectakslain'
    namespace: 'default'
    command: 'get'
    arguments: 'deployments'
  name: "kube"

# ci dessous : récupération n° de version dans les variables "vernew" et "verold"

- task: CmdLine@2
  inputs:
    script: |
      versionnew=$((curl 'https://hub.docker.com/v2/repositories/simplonasa/azure_voting_app/tags' | jq '."results"[0]["name"]')| sed 's/^.//;s/.$//')
      versionold=$(echo $KUBE_KUBECTLOUTPUT | jq '.items[1].spec.template.spec.containers[].image' | cut -d: -f2 | sed 's/"//')
      echo "##vso[task.setvariable variable=vernew]$versionnew"
      echo "##vso[task.setvariable variable=verold]$versionold"
      cd /home/vsts
      git clone https://github.com/Simplon-AlainCaupin/brief7.git
      cd brief7/app/
      sed -i 's/{{ version }}/'$versionnew'/g' app-deploy.yml
  name: 'bashver'

# bonne pratique, séparer la partie ci dessus dans un fichier isolé sur le repo github et l'appeler sur le pipeline !

# ci dessous : comparaison de la version dockerhub avec la version actuelle
- task: Kubernetes@1
  condition: ne(variables['verold'],variables['vernew'])
  inputs:
    connectionType: 'Kubernetes Service Connection'
    kubernetesServiceEndpoint: 'connectakslain'
    namespace: 'default'
    command: 'apply'
    useConfigurationFile: true
    configuration: '/home/vsts/brief7/app/'
    arguments: ''
    secretType: 'generic'
    outputFormat: 'json'
```
# Cost forecast

Dépendant de l'utilisation, exemple sur une réservation / 3 ans

![](https://i.imgur.com/6dgUgzD.png)

![](https://i.imgur.com/Er4go8q.png)
