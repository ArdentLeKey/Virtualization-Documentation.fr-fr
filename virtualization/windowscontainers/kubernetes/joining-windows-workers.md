---
title: Joindre des nœuds Windows
author: daschott
ms.author: daschott
ms.date: 11/02/2018
ms.topic: get-started-article
ms.prod: containers
description: Connexion d’un nœud Windows à un cluster Kubernetes avec la version 1.14.
keywords: kubernetes, 1,14, Windows, mise en route
ms.assetid: 3b05d2c2-4b9b-42b4-a61b-702df35f5b17
ms.openlocfilehash: c9dbfec968d52d9fbc528892f0e3749270e3ff70
ms.sourcegitcommit: c4a3f88d1663dd19336bfd4ede0368cb18550ac7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "9882982"
---
# <a name="joining-windows-server-nodes-to-a-cluster"></a>Joindre des nœuds Windows Server à un cluster #
Une fois que vous avez [configuré un nœud maître Kubernetes](./creating-a-linux-master.md) et que vous avez [sélectionné la solution réseau de votre choix](./network-topologies.md), vous pouvez joindre des nœuds Windows Server pour former un cluster. Pour cela, il est nécessaire [de préparer les nœuds Windows avant de procéder à la](#preparing-a-windows-node) réunion.

## <a name="preparing-a-windows-node"></a>Préparation d’un nœud Windows ##
> [!NOTE]  
> Tous les extraits de code dans les sections Windows doivent être exécutés dans PowerShell avec _élévation_ de privilèges.

### <a name="install-docker-requires-reboot"></a>Installation de l’ancrage (nécessite un redémarrage) ###
Kubernetes utilise [dockers](https://www.docker.com/) comme moteur de conteneur, c’est pourquoi nous devons l’installer. Vous pouvez suivre les [instructions Docs officielles](../manage-docker/configure-docker-daemon.md#install-docker), les [instructions Docker](https://store.docker.com/editions/enterprise/docker-ee-server-windows), ou essayer les étapes suivantes:

```powershell
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
Install-Package -Name Docker -ProviderName DockerMsftProvider
Restart-Computer -Force
```

Si vous vous trouvez derrière un serveur proxy, vous devez définir les variables d’environnement PowerShell suivantes:
```powershell
[Environment]::SetEnvironmentVariable("HTTP_PROXY", "http://proxy.example.com:80/", [EnvironmentVariableTarget]::Machine)
[Environment]::SetEnvironmentVariable("HTTPS_PROXY", "http://proxy.example.com:443/", [EnvironmentVariableTarget]::Machine)
```

Si vous vous trouvez après le redémarrage, le message d’erreur suivant apparaît:

![texte](media/docker-svc-error.png)

Démarrez ensuite le service d’ancrage manuellement:

```powershell
Start-Service docker
```

### <a name="create-the-pause-infrastructure-image"></a>Créer l’image «suspendre» (infrastructure) ###
> [!Important]
> Il est important de veiller à ce qu’il y ait un conflit entre les images conteneurs; le fait de ne pas avoir la balise attendue risque de provoquer une `docker pull` image de conteneur incompatible et provoquer [des problèmes de déploiement](./common-problems.md#when-deploying-docker-containers-keep-restarting) tels que l' `ContainerCreating` état de l’inexistence.

Maintenant que `docker` est installé, vous devez préparer une image «pause» qui est utilisée par Kubernetes pour préparer les pods d’infrastructure. Vous pouvez effectuer les trois étapes suivantes: 
  1. [extraire l’image](#pull-the-image)
  2. [balisage](#tag-the-image) en tant que Microsoft/serveur: les plus récents
  3. et l' [exécuter](#run-the-container)


#### <a name="pull-the-image"></a>Extraire l’image ####     
 Tirez l’image pour votre version de Windows spécifique. Par exemple, si vous exécutez Windows Server 2019:

 ```powershell
docker pull mcr.microsoft.com/windows/nanoserver:1809
 ```

#### <a name="tag-the-image"></a>Marquer l’image ####
Le Dockerfiles que vous utiliserez plus loin dans ce guide recherchez `:latest` la balise d’image. Marquez l’image du serveur que vous venez d’extraire comme suit:

```powershell
docker tag mcr.microsoft.com/windows/nanoserver:1809 microsoft/nanoserver:latest
```

#### <a name="run-the-container"></a>Exécuter le conteneur ####
Vérifiez que le conteneur s’exécute réellement sur votre ordinateur:

```powershell
docker run microsoft/nanoserver:latest
```

Vous devez voir ce qui suit:

![texte](./media/docker-run-sample.png)

> [!tip]
> Si vous ne parvenez pas à exécuter le conteneur, voir: [version de l’hôte de conteneur correspondant avec l’image du conteneur](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#matching-container-host-version-with-container-image-versions)


#### <a name="prepare-kubernetes-for-windows-directory"></a>Préparer le répertoire Kubernetes pour Windows ####
Créez un répertoire «Kubernetes pour Windows» pour stocker des fichiers binaires Kubernetes ainsi que les scripts de déploiement et les fichiers de configuration.

```powershell
mkdir c:\k
```

#### <a name="copy-kubernetes-certificate"></a>Copier le certificat Kubernetes #### 
Copiez le fichier de certificat`$HOME/.kube/config`Kubernetes () [du maître](./creating-a-linux-master.md#collect-cluster-information) vers `C:\k` ce nouvel annuaire.

> [!tip]
> Vous pouvez utiliser des outils tels que [xcopy](https://docs.microsoft.com/windows-server/administration/windows-commands/xcopy) ou [WinSCP](https://winscp.net/eng/download.php) pour transférer le fichier de configuration entre les nœuds.

#### <a name="download-kubernetes-binaries"></a>Télécharger des fichiers binaires Kubernetes ####
Pour pouvoir exécuter Kubernetes, vous devez d’abord télécharger les `kubectl`fichiers binaires `kubelet`, et `kube-proxy` . Vous pouvez les télécharger à partir des liens figurant `CHANGELOG.md` dans le fichier des [dernières versions](https://github.com/kubernetes/kubernetes/releases/).
 - Par exemple, Voici les [fichiers binaires de nœud v 1.14](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.14.md#node-binaries).
 - Utilisez un outil tel que [développe-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) pour extraire l’archive et importer les fichiers binaires `C:\k\`.

#### <a name="optional-setup-kubectl-on-windows"></a>Facultatif Installation de kubectl sur Windows ####
Si vous souhaitez contrôler le cluster à partir de Windows, vous pouvez le faire à `kubectl` l’aide de la commande. Tout d’abord, `kubectl` pour rendre disponible en `C:\k\` dehors de l’annuaire `PATH` , modifiez la variable d’environnement:

```powershell
$env:Path += ";C:\k"
```

Si vous souhaitez que cette modification soit permanente, modifiez la variable dans la cible d’ordinateur:

```powershell
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\k", [EnvironmentVariableTarget]::Machine)
```

Nous allons ensuite vérifier que le [certificat de cluster](#copy-kubernetes-certificate) est valide. Pour définir l’emplacement où `kubectl` Rechercher le fichier de configuration, vous pouvez transmettre le `--kubeconfig` paramètre ou modifier la `KUBECONFIG` variable d’environnement. Par exemple, si la configuration se trouve dans `C:\k\config`:

```powershell
$env:KUBECONFIG="C:\k\config"
```

Pour rendre ce paramètre permanent pour l’étendue de l’utilisateur actuel:

```powershell
[Environment]::SetEnvironmentVariable("KUBECONFIG", "C:\k\config", [EnvironmentVariableTarget]::User)
```

Enfin, pour vérifier que la configuration a été correctement découverte, vous pouvez utiliser les éléments suivants:

```powershell
kubectl config view
```

Si vous recevez une erreur de connexion,

```
Unable to connect to the server: dial tcp [::1]:8080: connectex: No connection could be made because the target machine actively refused it.
```

Vérifiez l’emplacement kubeconfig ou essayez de le copier à nouveau.

Si vous ne voyez aucune erreur, le nœud est désormais prêt à rejoindre le cluster.

## <a name="joining-the-windows-node"></a>Accès au nœud Windows ##
En fonction de la [solution réseau que vous avez choisie](./network-topologies.md), vous pouvez:
1. [Joindre des nœuds Windows Server à un cluster Flannel (vxlan ou Host-GW)](#joining-a-flannel-cluster)
2. [Joindre des nœuds Windows Server à un cluster à l’aide d’un commutateur TDR](#joining-a-tor-cluster)

### <a name="joining-a-flannel-cluster"></a>Rejoindre un groupe Flannel ###
Il existe une collection de scripts de déploiement Flannel sur [ce référentiel Microsoft](https://github.com/Microsoft/SDN/tree/master/Kubernetes/flannel/overlay) qui vous permet de joindre ce nœud au cluster.

Téléchargez le script [Start. ps1 Flannel](https://github.com/Microsoft/SDN/blob/master/Kubernetes/flannel/start.ps1) dont le contenu doit être extrait pour `C:\k`:

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
wget https://raw.githubusercontent.com/Microsoft/SDN/master/Kubernetes/flannel/start.ps1 -o c:\k\start.ps1
```

En supposant que vous avez [préparé votre nœud Windows](#preparing-a-windows-node)et que votre `c:\k` annuaire ressemble à ce qui suit, vous êtes prêt à rejoindre le nœud.

![texte](./media/flannel-directory.png)

#### <a name="join-node"></a>Nœud de jointure #### 
Pour simplifier le processus de participation à un nœud Windows, il vous suffit d’exécuter un script Windows unique pour `kubelet`lancer `kube-proxy`, `flanneld`, et rejoindre le nœud.

> [!Note]
> [Start. ps1](https://github.com/Microsoft/SDN/blob/master/Kubernetes/flannel/start.ps1) référence [installe. ps1](https://github.com/Microsoft/SDN/blob/master/Kubernetes/windows/install.ps1), qui télécharge des fichiers supplémentaires, tels `flanneld` que les exécutables et [Dockerfile pour le pod d’infrastructure](https://github.com/Microsoft/SDN/blob/master/Kubernetes/windows/Dockerfile) , *et les installe pour vous*. Pour le mode réseau superposé, le [pare-feu](https://github.com/Microsoft/SDN/blob/master/Kubernetes/windows/helper.psm1#L111) est ouvert pour le port UDP local 4789. Il peut y avoir plusieurs fenêtres PowerShell ouvertes/fermées ainsi qu’après quelques secondes d’interruption réseau pendant la première création du nouveau vSwitch externe du réseau pod.

```powershell
cd c:\k
.\start.ps1 -ManagementIP <Windows Node IP> -NetworkMode <network mode>  -ClusterCIDR <Cluster CIDR> -ServiceCIDR <Service CIDR> -KubeDnsServiceIP <Kube-dns Service IP> -LogDir <Log directory>
```
# [<a name="managementip"></a>ManagementIP](#tab/ManagementIP)
Adresse IP attribuée au nœud Windows. Vous pouvez l' `ipconfig` utiliser pour retrouver ce problème.

|  |  | 
|---------|---------|
|Paramètre     | `-ManagementIP`        |
|Valeur par défaut    | n.A. **required**        |

# [<a name="networkmode"></a>NetworkMode](#tab/NetworkMode)
Le mode `l2bridge` réseau (hôte Flannel-GW) ou `overlay` (Flannel vxlan) choisi comme [solution réseau](./network-topologies.md).

> [!Important] 
> `overlay` le mode réseau (Flannel vxlan) nécessite Kubernetes v 1.14 (ou une version ultérieure) et [KB4489899](https://support.microsoft.com/help/4489899).

|  |  | 
|---------|---------|
|Paramètre     | `-NetworkMode`        |
|Valeur par défaut    | `l2bridge`        |


# [<a name="clustercidr"></a>ClusterCIDR](#tab/ClusterCIDR)
La [plage de sous-réseau de cluster](./getting-started-kubernetes-windows.md#cluster-subnet-def).

|  |  | 
|---------|---------|
|Paramètre     | `-ClusterCIDR`        |
|Valeur par défaut    | `10.244.0.0/16`        |


# [<a name="servicecidr"></a>ServiceCIDR](#tab/ServiceCIDR)
La [plage de sous-réseau de service](./getting-started-kubernetes-windows.md#service-subnet-def).

|  |  | 
|---------|---------|
|Paramètre     | `-ServiceCIDR`        |
|Valeur par défaut    | `10.96.0.0/12`        |


# [<a name="kubednsserviceip"></a>KubeDnsServiceIP](#tab/KubeDnsServiceIP)
[Adresse IP du service DNS Kubernetes](./getting-started-kubernetes-windows.md#plan-ip-addressing-for-your-cluster).

|  |  | 
|---------|---------|
|Paramètre     | `-KubeDnsServiceIP`        |
|Valeur par défaut    | `10.96.0.10`        |


# [<a name="interfacename"></a>InterfaceName](#tab/InterfaceName)
Nom de l’interface réseau de l’hôte Windows. Vous pouvez l' `ipconfig` utiliser pour retrouver ce problème.

|  |  | 
|---------|---------|
|Paramètre     | `-InterfaceName`        |
|Valeur par défaut    | `Ethernet`        |


# [<a name="logdir"></a>LogDir](#tab/LogDir)
Le répertoire dans lequel kubelet et Kube les journaux du proxy seront redirigés vers les fichiers de sortie correspondants.

|  |  | 
|---------|---------|
|Paramètre     | `-LogDir`        |
|Valeur par défaut    | `C:\k`        |


---

> [!tip]
> Vous avez déjà noté le sous-réseau de service, le sous-réseau de services et l’adresse IP Kube-DNS du maître Linux [précédemment](./creating-a-linux-master.md#collect-cluster-information)

Après l’exécution de cette opération, vous devriez être en mesure d’effectuer les opérations suivantes:
  * Afficher les nœuds Windows joints à l’aide de `kubectl get nodes`
  * Voir 3 fenêtres PowerShell ouvertes, une pour `kubelet` `flanneld`les et une pour les `kube-proxy`
  * Voir les processus de l’agent `flanneld`hôte `kubelet`pour, `kube-proxy` et en exécution sur le nœud

En cas de succès, passez aux [étapes suivantes](#next-steps).

## <a name="joining-a-tor-cluster"></a>Rejoindre un groupe ##
> [!NOTE]
> Vous pouvez ignorer cette section si vous avez choisi Flannel comme solution réseau [auparavant](./network-topologies.md#flannel-in-host-gateway-mode).

Pour cela, vous devez suivre les instructions de configuration des [conteneurs Windows Server sur Kubernetes pour la topologie de routage en amont](https://kubernetes.io/docs/getting-started-guides/windows/#for-1-upstream-l3-routing-topology-and-2-host-gateway-topology). Cela consiste à vérifier que vous configurez votre routeur en amont de telle sorte que le préfixe de port de la Pod affecté à un nœud correspond à son adresse IP de nœud respective.

En supposant que le nouveau nœud est répertorié comme «prêt `kubectl get nodes`» en, kubelet + Kube-proxy est en cours d’exécution et que vous avez configuré votre routeur en amont, vous êtes prêt pour les étapes suivantes.

## <a name="next-steps"></a>Étapes suivantes ##
Dans cette section, nous avons expliqué comment rejoindre des travailleurs Windows sur notre cluster Kubernetes. Vous êtes maintenant prêt à passer à l’étape 5:

> [!div class="nextstepaction"]
> [Participation à des travailleurs Linux](./joining-linux-workers.md)

Par ailleurs, si vous n’avez pas de travailleurs Linux, n’hésitez pas à passer à l’étape 6:

> [!div class="nextstepaction"]
> [Déploiement de ressources Kubernetes](./deploying-resources.md)
