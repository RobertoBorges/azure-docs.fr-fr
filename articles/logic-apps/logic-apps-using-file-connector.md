---
title: Se connecter aux systèmes de fichiers locaux - Azure Logic Apps | Microsoft Docs
description: Automatiser des tâches et des flux de travail qui se connectent aux systèmes de fichiers locaux avec le connecteur de système de fichiers via la passerelle de données locale dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 41dd8ad721329c4c4d2761c9e4a37c640251dac3
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125276"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Se connecter aux systèmes de fichiers locaux avec Azure Logic Apps

Avec le connecteur de système de fichiers et Azure Logic Apps, vous pouvez créer des tâches et des flux de travail automatisés qui créent et gèrent des fichiers sur un partage de fichiers local, par exemple :  

- Créer, obtenir, ajouter, mettre à jour et supprimer des fichiers
- Répertorier des fichiers dans des dossiers ou des dossiers racine.
- Obtenir les métadonnées et le contenu des fichiers.

Cet article vous explique comment vous connecter à un système de fichiers local, conformément à la description faite dans cet exemple de scénario : copier un fichier chargé dans Dropbox vers un partage de fichiers, puis envoyer un e-mail. Pour vous connecter en toute sécurité et d’accéder aux systèmes locaux, les applications logiques utilisent la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Pour pouvoir connecter les applications logiques à des systèmes locaux tels que votre serveur de système de fichiers, vous devez [installer et configurer une passerelle de données locale](../logic-apps/logic-apps-gateway-install.md). De cette façon, vous pouvez spécifier que votre installation de passerelle soit utilisée lorsque vous créez la connexion du système de fichiers depuis votre application logique.

* Un [compte Dropbox ](https://www.dropbox.com/) et vos informations d’identification

  Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte Dropbox. 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour cet exemple, vous avez besoin d’une application logique vide.

## <a name="add-trigger"></a>Ajouter un déclencheur

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Dans la zone de recherche, entrez « dropbox » comme filtre. Depuis la liste des déclencheurs, sélectionnez ce déclencheur : **Lorsqu’un fichier est créé** 

   ![Sélectionner un déclencheur Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Connectez-vous avec vos informations d’identification de compte Dropbox et autorisez l’accès à vos données Dropbox pour Azure Logic Apps. 

1. Fournissez les informations requises pour votre déclencheur.

   ![Déclencheur Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Ajouter des actions

1. Sous le déclencheur, choisissez **Étape suivante**. Dans la zone de recherche, entrez « système de fichiers » en tant que filtre. Dans la liste des actions, sélectionnez cette action : **Créer un fichier : système de fichiers**

   ![Trouver le connecteur de système de fichiers](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Si vous ne disposez pas encore d’une connexion à votre système de fichiers, vous êtes invité à créer une connexion.

   ![Créer une connexion](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriété | Obligatoire | Valeur | Description | 
   | -------- | -------- | ----- | ----------- | 
   | **Nom de connexion** | Oui | <*connection-name*> | Le nom souhaité pour votre connexion | 
   | **Dossier racine** | Oui | <*root-folder-name*> | Le dossier racine pour votre système de fichiers, tel qu’un dossier local sur l’ordinateur où est installée la passerelle de données locale, ou le dossier de partage réseau auquel l’ordinateur peut accéder. <p>Par exemple : `\\PublicShare\\DropboxFiles` <p>Le dossier racine est le dossier parent principal qui est utilisé pour les chemins relatifs de toutes les actions liées aux fichiers. | 
   | **Type d'authentification** | Non  | <*auth-type*> | Type d’authentification utilisé par votre système de fichiers, par exemple, **Windows** | 
   | **Nom d’utilisateur** | Oui | <*domaine*>\\<*nom d’utilisateur*> | Le nom d’utilisateur de votre passerelle de données installée précédemment | 
   | **Mot de passe** | Oui | <*your-password*> | Le mot de passe de votre passerelle de données installée précédemment | 
   | **gateway** | Oui | <*installed-gateway-name*> | Le nom de la passerelle que vous avez installée précédemment | 
   ||| 

1. Lorsque vous êtes prêt, choisissez **Créer**. 

   Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement. 
   Si la connexion est configurée correctement, des options apparaissent pour l’action que vous avez sélectionnée précédemment. 

1. Configurez l’action **Créer un fichier**, fournissez les détails pour la copie de fichiers de Dropbox vers le dossier racine de votre partage de fichiers local. Pour ajouter des résultats des étapes précédentes, cliquez dans les zones, puis effectuez la sélection dans les champs disponibles lorsque la liste de contenu dynamique s’affiche.

   ![Créer une action de fichier](media/logic-apps-using-file-connector/create-file-filled.png)

1. Maintenant, ajoutez une action Outlook qui envoie un e-mail, afin que les utilisateurs concernés soient informés du nouveau fichier. Saisissez les destinataires, l’objet et le corps de l’e-mail. Pour tester, vous pouvez utiliser votre propre adresse e-mail.

   ![Envoyer l’action de messagerie](media/logic-apps-using-file-connector/send-email.png)

1. Enregistrez votre application logique. Testez votre application en chargeant un fichier sur Dropbox. 

   Votre application logique doit copier le fichier dans votre partage de fichiers en local et envoyer un e-mail aux destinataires pour les informer du fichier copié.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/fileconnector/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site UserVoice concernant Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [vous connecter à des données locales](../logic-apps/logic-apps-gateway-connection.md) 
* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
