---
title: Utiliser l’interface de ligne de commande Azure pour créer une application Azure AD et la configurer pour accéder à l’API Azure Media Services | Microsoft Docs
description: Cette rubrique montre comment utiliser l’interface de ligne de commande Azure pour créer une application Azure AD et la configurer pour accéder à l’API d’Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: fcd0ea10bd39f9e7252e114e8d6401a4fe0ecadb
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096546"
---
# <a name="use-azure-cli-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Utiliser l’interface de ligne de commande Azure pour créer une application AAD et la configurer pour accéder à l’API Azure Media Services

Cette rubrique vous montre comment utiliser l’interface de ligne de commande Azure pour créer une application Azure Active Directory (Azure AD) et un principal de service pour accéder aux ressources Azure Media Services. 

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Pour plus d’informations, consultez la page [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Un compte Media Services. Pour plus d’informations, voir [Création d’un compte Azure Media Services à l’aide du portail Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Lancez Cloud Shell à partir du volet de navigation supérieur du portail.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Pour plus d’informations, voir la [présentation d’Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Créer une application Azure AD et configurer l’accès au compte multimédia avec l’interface de ligne de commande Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Par exemple : 

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Dans cet exemple, l’**étendue** est le chemin d’accès complet de la ressource pour le compte de services multimédia. Toutefois, l’**étendue** peut être à n’importe quel niveau.

Par exemple, il peut s’agir d’un des niveaux suivants :
 
* Niveau d'**abonnement**.
* Niveau de **groupe de ressources**.
* Niveau de **ressource** (par exemple, compte multimédia).

Pour plus d’informations, consultez [Créez un principal du service avec Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli).

Voir aussi [Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Étapes suivantes

Commencez le [chargement de fichiers sur votre compte](media-services-portal-upload-files.md).
