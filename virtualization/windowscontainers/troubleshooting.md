---
title: Résolution des problèmes liés aux conteneurs Windows
description: Conseils de dépannage, scripts automatisés et informations de journal pour les conteneurs Windows et Docker
keywords: Docker, conteneurs, résolution des problèmes, journaux
author: PatrickLang
ms.date: 12/19/2016
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
ms.assetid: ebd79cd3-5fdd-458d-8dc8-fc96408958b5
ms.openlocfilehash: 16d2794688d60757ef1321d687f6a987ccf0b581
ms.sourcegitcommit: 62fff5436770151a28b6fea2be3a8818564f3867
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "10147232"
---
# <a name="troubleshooting"></a>Résolution des problèmes

Vous rencontrez des difficultés pour configurer votre ordinateur ou exécuter un conteneur? Nous avons créé un script PowerShell pour rechercher les problèmes courants. Commencez par le tester pour voir ce qu’il trouve, puis partagez vos résultats.

```PowerShell
Invoke-WebRequest https://aka.ms/Debug-ContainerHost.ps1 -UseBasicParsing | Invoke-Expression
```
La liste de tous les tests qu’il exécute avec des solutions courantes est disponible dans le [fichier Lisez-moi](https://github.com/Microsoft/Virtualization-Documentation/blob/live/windows-server-container-tools/Debug-ContainerHost/README.md) du script.

Si cela ne permet pas de trouver la source du problème, publiez la sortie de votre script sur le [Forum sur les conteneurs](https://social.msdn.microsoft.com/Forums/home?forum=windowscontainers). C’est l’endroit idéal pour obtenir de l’aide de la Communauté, notamment des Windows Insiders et des développeurs Windows.


## <a name="finding-logs"></a>Où trouver les journaux
Plusieurs services sont utilisés pour gérer les conteneurs Windows. Les sections suivantes indiquent où obtenir les journaux de chaque service.

# <a name="docker-engine"></a>Moteur Docker
Les enregistrements du moteur Docker sont consignés dans le journal des événements des applications Windows, plutôt que dans un fichier. Ces enregistrements peuvent facilement être lus, triés et filtrés à l’aide de Windows PowerShell

Par exemple, cette commande affiche les enregistrements du moteur Docker des 5dernières minutes en commençant par le plus ancien.

```
Get-EventLog -LogName Application -Source Docker -After (Get-Date).AddMinutes(-5) | Sort-Object Time 
```

Ces enregistrements peuvent aussi être facilement redirigés dans un fichier CSV pour être lus par un autre outil ou une feuille de calcul.

```
Get-EventLog -LogName Application -Source Docker -After (Get-Date).AddMinutes(-30)  | Sort-Object Time | Export-CSV ~/last30minutes.CSV
```

## <a name="enabling-debug-logging"></a>Activation de la journalisation du débogage
Vous pouvez également activer la journalisation au niveau du débogage sur le moteur Docker. Cette opération peut être utile pour la résolution des problèmes si les journaux ordinaires ne contiennent pas suffisamment de détails.

Tout d’abord, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez `sc.exe qc docker` pour obtenir la ligne de commande actuelle pour le service Docker.
Exemple:
```
C:\> sc.exe qc docker
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: docker
        TYPE               : 10  WIN32_OWN_PROCESS
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : "C:\Program Files\Docker\dockerd.exe" --run-service
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Docker Engine
        DEPENDENCIES       :
        SERVICE_START_NAME : LocalSystem
```

Prenez la valeur `BINARY_PATH_NAME` actuelle, puis modifiez-la:
- Ajoutez -D à la fin
- Placez chaque" dans une séquence d’échappement avec\
- Placez l’ensemble de la commande entre"

Exécutez ensuite la commande `sc.exe config docker binpath=` suivie de la nouvelle chaîne. Exemple: 
```
sc.exe config docker binpath= "\"C:\Program Files\Docker\dockerd.exe\" --run-service -D"
```


Redémarrez maintenant le service Docker
```
sc.exe stop docker
sc.exe start docker
```

Beaucoup plus d’informations sont ainsi consignées dans le journal des événements de l’application. Il est donc préférable de supprimer l’option `-D` une fois la résolution des problèmes terminée. Suivez la même procédure que celle mentionnée ci-dessus sans`-D` et redémarrez le service pour désactiver la journalisation du débogage.

Vous pouvez aussi exécuter le démon Docker en mode débogage à partir d’une invite PowerShell avec élévation de privilèges, capturant ainsi la sortie directement dans un fichier.
```PowerShell
sc.exe stop docker
<path\to\>dockerd.exe -D > daemon.log 2>&1
```

## <a name="obtaining-stack-dump"></a>Obtention du vidage de la pile.

En règle générale, cette fonction n’est utile que pour les demandes explicitement formulées par le support technique Microsoft ou les développeurs de l’arrimateur. Elle peut être utilisée pour faciliter le diagnostic d’une situation dans laquelle l’arrimateur semble avoir raccroché. 

Télécharger [docker-signal.exe](https://github.com/jhowardmsft/docker-signal).

Utilisation:
```PowerShell
docker-signal --pid=$((Get-Process dockerd).Id)
```

Le fichier de sortie sera localisé dans le répertoire de la racine des données. Le répertoire par défaut est `C:\ProgramData\Docker`. Le répertoire réel peut être confirmé en exécutant `docker info -f "{{.DockerRootDir}}"`.

Le fichier est `goroutine-stacks-<timestamp>.log`.

Notez que `goroutine-stacks*.log` ne contient pas d’informations personnelles.


# <a name="host-compute-service"></a>Service de calcul hôte
Le moteur Docker dépend d’un service de calcul hôte spécifique à Windows. Il a des journaux distincts: 
- Microsoft-Windows-Hyper-V-Compute-Admin
- Microsoft-Windows-Hyper-V-Compute-Operational

Ils sont visibles dans l’Observateur d’événements et peuvent également être interrogés avec PowerShell.

Par exemple:
```PowerShell
Get-WinEvent -LogName Microsoft-Windows-Hyper-V-Compute-Admin
Get-WinEvent -LogName Microsoft-Windows-Hyper-V-Compute-Operational 
```

## <a name="capturing-hcs-analyticdebug-logs"></a>Enregistrement des journaux d’analyse/débogage HCS

Pour activer les journaux d’analyse/débogage de calcul Hyper-V et les enregistrer sur `hcslog.evtx`.

```PowerShell
# Enable the analytic logs
wevtutil.exe sl Microsoft-Windows-Hyper-V-Compute-Analytic /e:true /q:true

# <reproduce your issue>

# Export to an evtx
wevtutil.exe epl Microsoft-Windows-Hyper-V-Compute-Analytic <hcslog.evtx>

# Disable
wevtutil.exe sl Microsoft-Windows-Hyper-V-Compute-Analytic /e:false /q:true
```

## <a name="capturing-hcs-verbose-tracing"></a>Capturer le suivi HCS en clair

En règle générale, cela n'est utile que si le support Microsoft le demande. 

Télécharger [HcsTraceProfile.wprp](https://gist.github.com/jhowardmsft/71b37956df0b4248087c3849b97d8a71)

```PowerShell
# Enable tracing
wpr.exe -start HcsTraceProfile.wprp!HcsArgon -filemode

# <reproduce your issue>

# Capture to HcsTrace.etl
wpr.exe -stop HcsTrace.etl "some description"
```

Fournir `HcsTrace.etl` à votre contact de support.
