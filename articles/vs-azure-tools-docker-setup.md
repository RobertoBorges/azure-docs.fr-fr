---
title: Configurer un hôte Docker avec VirtualBox | Microsoft Docs
description: Instructions pas à pas pour configurer une instance Docker par défaut à l'aide de Docker Machine et de VirtualBox.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 7c78f27fa948c15202e83df4ed42a805a414a72e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140253"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Configurer un hôte Docker avec VirtualBox
## <a name="overview"></a>Vue d’ensemble
Cet article vous guide tout au long de la configuration d'une instance Docker par défaut à l'aide de Docker Machine et de VirtualBox. Si vous utilisez le [client Docker pour Windows](https://www.docker.com/products/docker-desktop), cette configuration n’est pas nécessaire.

## <a name="prerequisites"></a>Prérequis
Les outils suivants doivent être installés.

* [Boîte à outils Docker](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configuration du client Docker avec Windows PowerShell
Pour configurer un client Docker, ouvrez Windows PowerShell et procédez comme suit :

1. Créez une instance hôte docker par défaut.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Vérifiez que l'instance par défaut est configurée et en cours d'exécution. Vous devriez voir une instance nommée « par défaut » en cours d’exécution.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![Sortie docker-machine ls][0]
3. Choisissez par défaut l'hôte actuel et configurez votre interpréteur de commandes.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Affichez les conteneurs Docker actifs. La liste doit être vide.
   
    ```PowerShell
    docker ps
    ```
   
    ![Sortie docker ps][1]

> [!NOTE]
> Chaque fois que vous redémarrez votre machine de développement, vous devrez également redémarrer votre hôte Docker local.
> Pour ce faire, exécutez la commande suivante à l’invite de commandes : `docker-machine start default`
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
