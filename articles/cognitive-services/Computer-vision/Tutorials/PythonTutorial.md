---
title: 'Tutoriel : API Vision par ordinateur avec Python'
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Vision par ordinateur avec Python à l’aide de bloc-notes Jupyter. Visualisez vos résultats à l’aide de bibliothèques populaires.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 046250d3d2142badaac35490eff27bcac220fea9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344895"
---
# <a name="tutorial-computer-vision-api-python"></a>Tutoriel : API Vision par ordinateur avec Python

Ce didacticiel vous montre comment utiliser l’API Vision par ordinateur dans Python et comment visualiser vos résultats à l’aide de certaines bibliothèques populaires. Utilisez Jupyter pour exécuter le didacticiel. Pour savoir comment prendre en main les blocs-notes Jupyter interactifs, consultez la [documentation Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Ouvrir le bloc-notes du didacticiel dans Jupyter 

1. Accédez au [bloc-notes du didacticiel dans GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Cliquez sur le bouton vert pour cloner ou télécharger le didacticiel. 
3. Ouvrez une invite de commandes et accédez au dossier _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Exécutez la commande **jupyter notebook** dans l’invite de commandes. Cela permet de démarrer Jupyter.
5. Dans la fenêtre Jupyter, cliquez sur _Computer Vision API Example.ipynb_ pour ouvrir le bloc-notes du didacticiel. 

## <a name="run-the-tutorial"></a>Exécuter le didacticiel

Pour utiliser ce bloc-notes, vous avez besoin d’une clé d’abonnement pour l’API Vision par ordinateur. Rendez-vous sur la [page d’inscription](https://azure.microsoft.com/try/cognitive-services/) pour vous inscrire. Sur la page de connexion, utilisez votre compte Microsoft pour vous authentifier et vous abonner afin d’obtenir des clés gratuites. À la suite du processus d’inscription, collez votre clé dans la section des variables du bloc-notes (reproduite ci-dessous). La clé primaire ou secondaire fonctionne. Veillez à placer la clé entre guillemets pour créer une chaîne.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
