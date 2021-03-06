---
title: Spécifications de l’hyperviseur
description: Spécifications de l’hyperviseur
keywords: Windows 10, Hyper-V
author: allenma
ms.date: 06/26/2018
ms.topic: article
ms.prod: windows-10-hyperv
ms.service: windows-10-hyperv
ms.assetid: aee64ad0-752f-4075-a115-2d6b983b4f49
ms.openlocfilehash: afbbcf120961081191aaf9051866427c9ce1478e
ms.sourcegitcommit: 1ca9d7562a877c47f227f1a8e6583cb024909749
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74911179"
---
# <a name="hypervisor-specifications"></a>Spécifications de l’hyperviseur

## <a name="hypervisor-top-level-functional-specification"></a>Spécification fonctionnelle générale de l’hyperviseur

La spécification fonctionnelle générale de l’hyperviseur Hyper-V décrit le comportement de l’hyperviseur visible de l’extérieur par d’autres composants du système d’exploitation. Cette spécification est destinée aux développeurs des systèmes d’exploitation invités.
  
> Elle est fournie dans le cadre de Microsoft Open Specification Promise.  Consultez les informations suivantes pour plus de détails sur [Microsoft Open Specification Promise](https://docs.microsoft.com/openspecs/dev_center/ms-devcentlp/51a0d3ff-9f77-464c-b83f-2de08ed28134).  

#### <a name="download"></a>Télécharger
Release | Document
--- | ---
Windows Server 2016 (révision C) | [Spécification fonctionnelle de niveau supérieur de l’hyperviseur v 5.0 c. pdf](https://github.com/MicrosoftDocs/Virtualization-Documentation/raw/live/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v5.0C.pdf)
Windows Server 2012 R2 (révision B) | [Hypervisor Top Level Functional Specification v4.0b.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v4.0b.pdf) (Spécification fonctionnelle générale de l’hyperviseur)
Windows Server 2012 | [Hypervisor Top Level Functional Specification v3.0.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v3.0.pdf) (Spécification fonctionnelle générale de l’hyperviseur)
Windows Server 2008 R2 | [Hypervisor Top Level Functional Specification v2.0.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v2.0.pdf) (Spécification fonctionnelle générale de l’hyperviseur)

## <a name="requirements-for-implementing-the-microsoft-hypervisor-interface"></a>Configuration requise pour l’implémentation de l’interface de l’hyperviseur Microsoft

La spécification fonctionnelle générale décrit chaque aspect de l’architecture d’hyperviseur spécifique à Microsoft, qui est déclarée en tant qu’interface « HV#1 » pour les ordinateurs virtuels invités.  Toutefois, toutes les interfaces décrites dans cette spécification ne doivent pas être implémentées par l’hyperviseur tiers désireux de déclarer la conformité avec la spécification d’hyperviseur HV #1 de Microsoft. Le document « Configuration requise pour l’implémentation de l’interface de l’hyperviseur Microsoft » décrit l’ensemble minimal d’interfaces d’hyperviseur devant être implémenté par tout hyperviseur revendiquant la compatibilité avec l’interface HV#1 de Microsoft.

#### <a name="download"></a>Télécharger

[Requirements for Implementing the Microsoft Hypervisor Interface.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Requirements%20for%20Implementing%20the%20Microsoft%20Hypervisor%20Interface.pdf) (Configuration requise pour l’implémentation de l’interface de l’hyperviseur Microsoft)
