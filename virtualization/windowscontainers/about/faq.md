---
title: FAQ sur les conteneurs Windows
description: Forum aux questions sur les conteneurs Windows Server
keywords: docker, conteneurs
author: PatrickLang
ms.date: 10/25/2019
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
ms.assetid: 25de368c-5a10-40a4-b4aa-ac8c9a9ca022
ms.openlocfilehash: 19ff54ec032d61b24aea9fec4f14e8fce301d33a
ms.sourcegitcommit: 347d7c9d34f4c1d2473eb6c94c8ad6187318a037
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "10257951"
---
# <a name="frequently-asked-questions-about-containers"></a>Forum aux questions sur les conteneurs

## <a name="whats-the-difference-between-linux-and-windows-server-containers"></a>Quelle est la différence entre les conteneurs Linux et Windows Server?

Linux et Windows Server implémentent des technologies similaires au sein de leurs systèmes d’exploitation noyau et noyau. La différence provient de la plateforme et des charges de travail qui s’exécutent dans les conteneurs.  

Lorsqu’un client utilise des conteneurs Windows Server, il peut s’intégrer aux technologies Windows existantes, telles que .NET, ASP.NET et PowerShell.

## <a name="what-are-the-prerequisites-for-running-containers-on-windows"></a>Quelles sont les conditions préalables à l’exécution des conteneurs sur Windows?

Des conteneurs ont été introduits dans la plateforme avec Windows Server 2016. Pour utiliser les conteneurs, vous avez besoin de Windows Server 2016 ou de la mise à jour anniversaire Windows 10 (version 1607) ou d’une version ultérieure. Pour en savoir plus, consultez la [Configuration système requise](../deploy-containers/system-requirements.md) .

## <a name="what-are-wcow-and-lcow"></a>Présentation de WCOW et LCOW

WCOW est l’abréviation de «conteneurs Windows sur Windows». LCOW est l’abréviation de «conteneurs Linux sur Windows».

## <a name="how-are-containers-licensed-is-there-a-limit-to-the-number-of-containers-i-can-run"></a>Quels sont les conteneurs sous licence? Est-ce que le nombre de conteneurs que je peux exécuter est limité?

Le [CLUF](../images-eula.md) d’image de conteneur Windows décrit une utilisation qui dépend d’un utilisateur ayant un système d’exploitation hôte sous licence valide. Le nombre de conteneurs qu’un utilisateur peut exécuter dépend de l’édition de l’OS OS et du [mode d’isolation](../manage-containers/hyperv-container.md) sur lequel est exécuté un conteneur, ainsi que de l’exécution de ces conteneurs à des fins de développement/test ou en production.

|Système d’exploitation hôte                                                         |Limite de conteneur isolé processus                   |Hyper-V-limite de conteneurs isolés                   |
|----------------------------------------------------------------|---------------------------------------------------|---------------------------------------------------|
|WindowsServerStandard                                         |Illimité                                          |deuxième                                                  |
|WindowsServerDatacenter                                       |Illimité                                          |Illimité                                          |
|Windows 10 professionnel et entreprise                                   |Illimité *(aux fins de test ou de développement uniquement)*|Illimité *(aux fins de test ou de développement uniquement)*|
|Windows 10 IoT standard et entreprise)                             |Illimité *(aux fins de test ou de développement uniquement)*|Illimité *(aux fins de test ou de développement uniquement)*|

L’utilisation des images du conteneur Windows Server est déterminée par la lecture du nombre de invités de virtualisation pris en charge pour cette [édition](/windows-server/get-started-19/editions-comparison-19.md). L’utilisation de conteneurs sur une édition IoT de Windows dépend de restrictions de licence supplémentaires. Lisez l' [image du conteneur CLUF](../images-eula.md) pour comprendre exactement ce qui est autorisé et ce qui ne l’est pas.

## <a name="as-a-developer-do-i-have-to-rewrite-my-app-for-each-type-of-container"></a>En tant que développeur, dois-je réécrire mon application pour chaque type de conteneur?

Non. Les images de conteneur Windows sont communes à tous les conteneurs Windows Server et à l’isolation Hyper-V. Le choix du type de conteneur est effectué quand vous démarrez le conteneur. Du point de vue du développeur, les conteneurs Windows Server et l’isolation Hyper-V sont deux versions du même élément. Elles offrent le même niveau de développement, de programmation et de gestion, et sont ouvertes et extensibles, et incluent le même niveau d’intégration et de prise en charge de l’arrimeur.

Un développeur peut créer une image de conteneur à l’aide d’un conteneur Windows Server et le déployer dans l’isolation Hyper-V ou inversement sans modifier l’indicateur d’exécution approprié.

Les conteneurs Windows Server fournissent une plus grande densité et des performances optimales lorsque la vitesse est importante, par exemple pour réduire le temps de réactivité et des performances du Runtime par rapport aux configurations imbriquées. Isolation Hyper-V, true à son nom, offre une plus grande isolation, ce qui garantit que le code exécuté dans un conteneur ne peut pas être compromis ou influant sur le système d’exploitation hôte ou d’autres conteneurs qui s’exécutent sur le même hôte. Cela s’avère utile pour les scénarios multilocataires avec la configuration requise pour l’hébergement de code non fiable, y compris les applications SaaS et l’hébergement de calcul.

## <a name="can-i-run-windows-containers-in-process-isolated-mode-on-windows-10-enterprise-or-professional"></a>Puis-je exécuter des conteneurs Windows en mode d’isolation de processus sur Windows 10 entreprise ou professionnel?

À partir de la mise à jour 2018 de Windows 10 d’octobre, vous pouvez exécuter un conteneur Windows avec l’isolement de processus, mais vous devez préalablement demander l’isolement du processus à l’aide de l' `--isolation=process` indicateur lors de l’exécution de vos conteneurs avec `docker run`.

Si vous souhaitez exécuter vos conteneurs Windows de cette manière, vous devez vous assurer que votre hôte exécute Windows 10 Build 17763 + et que vous disposez d’une version d’amarrage avec le moteur 18,09 ou version ultérieure.

> [!WARNING]
> Cette fonctionnalité est réservée aux développements et aux tests. Vous devez continuer à utiliser Windows Server en tant qu’hôte pour les déploiements de production. En utilisant cette fonctionnalité, vous devez également vous assurer que les balises de version de votre hôte et du conteneur correspondent, sinon le conteneur peut ne pas démarrer ou présenter un comportement non défini.

## <a name="how-do-i-make-my-container-images-available-on-air-gapped-machines"></a>Comment rendre mes images de conteneur disponibles sur les appareils d’entrée aérienne?

Les images de base conteneur Windows contiennent des artefacts dont la distribution est restreinte par licence. Lorsque vous générez sur ces images et les transmettez dans un registre privé ou public, vous remarquerez que le calque de base n’est jamais transmis. Au lieu de cela, nous utilisons le concept de couche étrangère qui pointe vers le véritable calque de base résidant dans le stockage cloud Azure.

Cela peut compliquer l’opération lorsque vous disposez d’une machine qui ne peut pas extraire des images à partir de l’adresse de votre registre de conteneurs privés. Dans ce cas, les tentatives de suivi des couches étrangères pour obtenir l’image de base ne fonctionneront pas. Pour remplacer le comportement de la couche étrangère, vous pouvez utiliser `--allow-nondistributable-artifacts` l’indicateur dans le démon de l’ancrage.

> [!IMPORTANT]
> L’utilisation de cet indicateur ne préjuge pas de votre obligation de respecter les conditions de la licence d’image de base du conteneur Windows; vous ne devez pas publier le contenu Windows pour une redistribution publique ou tierce. L’utilisation au sein de votre propre environnement est autorisée.

## <a name="additional-feedback"></a>Commentaires supplémentaires

Vous voulez ajouter une information au FAQ? Ouvrez un nouveau problème de commentaires dans la section commentaires ou configurez une demande d’extraction pour cette page avec GitHub.
