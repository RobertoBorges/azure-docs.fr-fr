---
title: 'Démarrage rapide : Reconnaissance vocale dans Objective-C sur iOS à l’aide du SDK Speech Service'
titleSuffix: Azure Cognitive Services
description: Découvrez la reconnaissance vocale dans Objective-C sur iOS à l’aide du SDK Speech Service
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: chlandsi
ms.openlocfilehash: 8d6ecf251bb816eb0f41352af7c9d086c4aad751
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469773"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>Démarrage rapide : Reconnaissance vocale dans Objective-C sur iOS à l’aide du SDK Speech Service

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez découvrir comment créer une application iOS en Objective-C à l’aide du SDK Speech de Cognitive Services pour transcrire un fichier audio avec de la parole enregistrée en texte.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Essayez le service Speech gratuitement](get-started.md).
* Un Mac avec Xcode 9.4.1 installé en tant qu’environnement de développement iOS. Ce tutoriel cible iOS versions 11.4. Si vous n’avez pas encore Xcode, vous pouvez l’installer à partir de l’[App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Obtenir le SDK Speech pour iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.0.1`.

Le SDK Speech Cognitive Services pour Mac et iOS est actuellement distribué sous forme de framework Cocoa.
Vous pouvez le télécharger à l’adresse suivante : https://aka.ms/csspeech/iosbinary. Téléchargez le fichier dans votre répertoire de base.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode 

Démarrez Xcode et démarrez un nouveau projet en cliquant sur **File** > **New** > **Project** (Fichier->Nouveau->Projet).
Dans la boîte de dialogue de sélection de modèle, choisissez le modèle « iOS Single View App ».

Dans les boîtes de dialogue qui suivent, effectuez les sélections suivantes :

1. Boîte de dialogue Project Options
    1. Entrez un nom pour l’application de démarrage rapide, par exemple `helloworld`.
    1. Entrez un nom d’organisation et un identificateur d’organisation appropriés, si vous avez déjà un compte de développeur Apple. À des fins de test, vous pouvez sélectionner n’importe quel nom, par exemple `testorg`. Pour signer l’application, vous avez également besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez qu’Objective-C est choisi comme langage pour le projet.
    1. Décochez toutes les cases pour les tests et les données de base.
    ![Paramètres du projet](media/sdk/qs-objectivec-project-settings.png)
1. Sélectionner le répertoire du projet
    1. Choisissez votre répertoire de base pour y placer le projet. Cela crée un répertoire `helloworld` dans votre répertoire de base, où sont stockés tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
    1. Ajustez les chemins du SDK dans les *paramètres du projet*.
        1. Sous l’onglet **General**, sous l’en-tête **Embedded Binaries** (Binaires incorporés), ajoutez la bibliothèque du SDK en tant que framework : **Add embedded binaries (Ajouter des binaires incorporés)** > **Add other... (Ajouter autres)** > Accédez à votre répertoire de base et choisissez le fichier `MicrosoftCognitiveServicesSpeech.framework`. Cela ajoutera aussi automatiquement la bibliothèque du SDK à l’en-tête **Linked Framework and Libraries** (Bibliothèques et framework liés).
        ![Framework ajouté](media/sdk/qs-objectivec-framework.png)
        1. Accédez à l’onglet **Build Settings** (paramètres de Build) et activez tous les paramètres (**All**).
        1. Ajoutez le répertoire `$(SRCROOT)/..` aux chemins de recherche de framework (*Framework Search Paths*) sous l’en-tête **Search Paths** (chemins de recherche).
        ![Paramètre de chemin de recherche de frameworks](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurer l’interface utilisateur

L’exemple d’application aura une interface utilisateur très simple : deux boutons pour démarrer la reconnaissance vocale à partir du fichier ou à partir de l’entrée du microphone, et une étiquette de texte pour afficher le résultat.
L’interface utilisateur est définie dans la partie `Main.storyboard` du projet.
Ouvrez la vue XML de la table de montage séquentiel en cliquant avec le bouton droit sur l’entrée `Main.storyboard` de l’arborescence de projet et en sélectionnant **Open As...** > **Source Code**.
Remplacez le code XML généré automatiquement par ceci :

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Téléchargez l’[exemple de fichier wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) en cliquant avec le bouton droit sur le lien et en choisissant **Enregistrer la cible sous...**. Ajoutez le fichier wav au projet en tant que ressource en le faisant glisser à partir d’une fenêtre Finder vers le niveau racine de la vue du projet.
Cliquez sur **Finish** dans la boîte de dialogue suivante sans changer les paramètres.
1. Remplacez le contenu du fichier `ViewController.m` généré automatiquement par :

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).
1. Ajoutez la requête pour l’accès au microphone. Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez **Ouvrir comme...** > **Code source**. Ajoutez les lignes suivantes dans la section `<dict>`, puis enregistrez le fichier.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Génération et exécution de l’exemple

1. Affichez la sortie de débogage (**View** > **Debug Area** > **Activate Console**).
1. Choisissez le simulateur iOS ou un appareil iOS connecté à votre ordinateur de développement comme destination pour l’application à partir de la liste présentée dans le menu **Produit** -> **Destination**.
1. Générez et exécutez l’exemple de code dans le simulateur iOS en sélectionnant **Product** -> **Run** dans le menu ou en cliquant sur le bouton **Play**.
Actuellement, le SDK Speech prend uniquement en charge les plateformes iOS 64 bits.
1. Après avoir cliqué sur le bouton « Recognize (Fichier) » dans l’application, vous devez voir le contenu du fichier audio « What’s the weather like? » dans la partie inférieure de l’écran.

 ![Application iOS simulée](media/sdk/qs-objectivec-simulated-app.png)

1. Après avoir cliqué sur le bouton « Recognize (Microphone) » dans l’application et prononcé quelques mots, vous devez voir le texte énoncé sur la partie inférieure de l’écran.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/objectivec-ios`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez nos exemples](speech-sdk.md#get-the-samples)

