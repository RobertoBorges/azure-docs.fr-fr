---
title: Comptes de stockage dans Azure Stack | Microsoft Docs
description: Découvrez comment créer un compte de stockage Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: mabrigg
ms.openlocfilehash: ae6539900e201f0559d998ad2d9be24c39d42e3b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713491"
---
# <a name="storage-accounts-in-azure-stack"></a>Comptes de stockage dans Azure Stack
Les comptes de stockage incluent les services d’objets Blob et de Table, ainsi que l’espace de noms unique pour vos objets de données de stockage. Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte de stockage.

1. Sur l’ordinateur Azure Stack POC, connectez-vous à `https://adminportal.local.azurestack.external` en tant [qu’administrateur](azure-stack-connect-azure-stack.md), puis cliquez sur **+ Créer une ressource** > **Données + stockage** > **Compte de stockage**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Dans le panneau **Créer un compte de stockage**, tapez un nom pour votre compte de stockage. Créer un **Groupe de ressources** ou sélectionnez un groupe existant, puis cliquez sur **Créer** pour créer le compte de stockage.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Pour voir votre nouveau compte de stockage, cliquez sur **Toutes les ressources**, puis recherchez le compte de stockage et cliquez sur son nom.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Étapes suivantes
[Utiliser les modèles Azure Resource Manager](user/azure-stack-arm-templates.md)

[Découvrir les comptes de stockage Azure](../storage/common/storage-create-storage-account.md)

[Télécharger le guide de validation du stockage ACS (Azure-Consistent Storage) d’Azure Stack](http://aka.ms/azurestacktp1doc)
