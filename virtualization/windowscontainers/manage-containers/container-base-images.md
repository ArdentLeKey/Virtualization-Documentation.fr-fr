---
title: Images de base du conteneur Windows
description: Vue d’ensemble des images de base du conteneur Windows et de leur utilisation.
keywords: Docker, conteneurs, hachages
author: patricklang
ms.date: 09/25/2019
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
ms.assetid: 88e6e080-cf8f-41d8-a301-035959dc5ce0
ms.openlocfilehash: 9884cc0ae2d2f398d2dc2fb1997a70493a6de6c0
ms.sourcegitcommit: 16744984ede5ec94cd265b6bff20aee2f782ca88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764178"
---
# <a name="container-base-images"></a>Images de base du conteneur

Windows propose quatre images de base de conteneur à partir desquelles les utilisateurs peuvent créer. Chaque image de base est une version différente du système d’exploitation Windows, a un encombrement sur disque différent et comporte une quantité différente de l’API Windows définie.

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows-servercore" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="" data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Windows Server Core</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Prend en charge les applications .NET Framework traditionnelles.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows-nanoserver" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="" data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">nano Server</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Conçu pour les applications .NET Core.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="" data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Windows</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Fournit l’ensemble des API Windows complet.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows-iotcore" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="" data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Windows IoT Core</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Conçu spécialement pour les applications IoT.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="image-discovery"></a>Détection d’images

Toutes les images de base du conteneur Windows sont détectables par le biais du Hub de l' [arrimeur](https://hub.docker.com/_/microsoft-windows-base-os-images). Les images de base du conteneur Windows sont servies à partir de [MCR.Microsoft.com](https://azure.microsoft.com/en-us/services/container-registry/), la Container Registry Microsoft. C’est pourquoi les commandes pull pour les images de base du conteneur Windows se présentent comme suit :

```code
docker pull mcr.microsoft.com/windows/servercore:ltsc2019
```

La valeur de la fonction de travail n’a pas son propre catalogue et est destinée à prendre en charge les catalogues existants tels que le hub d’ancrage. Grâce à l’empreinte mondiale d’Azure et couplée à Azure CDN, la solution de travail offre une expérience d’extraction d’image cohérente et rapide. Les clients Azure, qui exécutent leurs charges de travail dans Azure, bénéficient de l’amélioration des performances dans le réseau, ainsi que de l’intégration étroite avec les compartiments (source pour les images de conteneur Microsoft), de la place de marché Azure et du nombre croissant de services proposés par Azure. conteneurs comme format de package de déploiement.

## <a name="choosing-a-base-image"></a>Choix d’une image de base

Comment choisir l’image de base appropriée pour la génération ? Pour la plupart des utilisateurs, `Windows Server Core` et `Nanoserver` sera l’image la plus appropriée à utiliser.

### <a name="guidelines"></a>Recommandations

 Si vous êtes libre de cibler l’image que vous souhaitez, voici quelques conseils pour vous aider à orienter votre choix :

- **Votre application a-t-elle besoin du .NET Framework complet ?** Si la réponse à cette question est oui, vous devez cibler `Windows Server Core`.
- **Créez-vous une application Windows basée sur .NET Core ?** Si la réponse à cette question est oui, vous devez cibler `Nanoserver`.
- **Créez-vous une application IoT ?** Si la réponse à cette question est oui, vous devez cibler `IoT Core`.
- **L’image de conteneur Windows Server Core n’a-t-elle pas une dépendance dont votre application a besoin ?** Si la réponse à cette question est oui, vous devez essayer de cibler `Windows`. Cette image est beaucoup plus volumineuse que les autres images de base, mais elle contient un grand nombre des bibliothèques principales de Windows (telles que la bibliothèque GDI).
- **Êtes-vous un Windows Insider ?** Si c’est le cas, vous devez envisager d’utiliser la version Insider des images. Consultez la section « images de base pour les Windows Insiders » ci-dessous.

> [!TIP]
> De nombreux utilisateurs Windows souhaitent placer des applications qui ont une dépendance sur .NET. En plus des quatre images de base décrites ici, Microsoft publie plusieurs images de conteneur Windows qui sont préconfigurées avec des infrastructures Microsoft populaires, telles que l’image [.NET Framework](https://hub.docker.com/_/microsoft-dotnet-framework) et l’image [ASP .net](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/) .

### <a name="base-images-for-windows-insiders"></a>Images de base pour les Windows Insiders

Microsoft fournit des versions « Insider » de chaque image de base de conteneur. Ces images de conteneur d’Insider comportaient le plus récent et le plus grand développement de fonctionnalités dans nos images de conteneur. Lorsque vous exécutez un ordinateur hôte qui est une version Insider de Windows (Windows Insider ou Windows Server Insider), il est préférable d’utiliser ces images. Les images Insider sont disponibles sur le hub d’ancrage :

- [mcr.microsoft.com/windows/servercore/insider](https://hub.docker.com/_/microsoft-windows-servercore-insider)
- [mcr.microsoft.com/windows/nanoserver/insider](https://hub.docker.com/_/microsoft-windows-nanoserver-insider)
- [mcr.microsoft.com/windows/iotcore/insider](https://hub.docker.com/_/microsoft-windows-iotcore-insider)
- [mcr.microsoft.com/windows/insider](https://hub.docker.com/_/microsoft-windows-insider)

Pour en savoir plus, consultez [utiliser des conteneurs avec le programme Windows Insider](../deploy-containers/insider-overview.md) .

### <a name="windows-server-core-vs-nanoserver"></a>Windows Server Core vs Server

`Windows Server Core` et `Nanoserver` sont les images de base les plus courantes à cibler. La principale différence entre ces images est que la surface de l’API de serveur est beaucoup plus petite. PowerShell, WMI et la pile de maintenance Windows sont absents de l’image de serveur.

Le serveur de serveurs a été conçu pour fournir juste assez de surface d’API pour exécuter des applications qui ont une dépendance sur .NET Core ou d’autres infrastructures Open source modernes. En guise de compromis avec la surface d’APi plus petite, l’image du serveur est beaucoup plus petite que le reste des images de base Windows. N’oubliez pas que vous pouvez ajouter autant de couches que vous le souhaitez sur Nano Server. Pour obtenir un exemple, consultez le [fichier Dockerfile Nano Server .NET Core](https://github.com/dotnet/dotnet-docker/blob/master/2.1/sdk/nanoserver-1909/amd64/Dockerfile).
