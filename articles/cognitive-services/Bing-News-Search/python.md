---
title: 'Démarrage rapide : API Recherche d’actualités Bing, Python'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Recherche d’actualités Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 583b304a742d9abfd799442c9aa2999ad6783a34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803543"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Guide de démarrage rapide pour l’API Recherche d’actualités Bing avec Python
Cette procédure pas à pas donne un exemple simple d’appel dans l’API Recherche d’actualités Bing, et de post-traitement de l’objet JSON en résultant. Pour plus d’informations, consultez la [documentation de la Nouvelle Recherche Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>Prérequis

Vous devrez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) est suffisant pour suivre ce guide de démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’un abonnement payant de votre tableau de bord Azure.

## <a name="running-the-walkthrough"></a>Exécution de la procédure pas à pas
Tout d’abord, affectez à `subscription_key` votre clé API pour le service API Bing.


```python
subscription_key = None
assert subscription_key
```

Vérifiez ensuite que le point de terminaison `search_url` est correct. Pour l’instant, un seul point de terminaison est utilisé pour les API Recherche Bing. Si vous rencontrez des erreurs d’autorisation, vérifiez bien cette valeur par rapport au point de terminaison de Recherche Bing dans votre tableau de bord Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Définissez `search_term` pour rechercher des articles d’actualités sur Microsoft.


```python
search_term = "Microsoft"
```

Le bloc suivant utilise la bibliothèque `requests` dans Python pour faire appel aux API Recherche Bing et retourner les résultats en tant qu’objet JSON. Notez que nous transmettons la clé API par le biais du dictionnaire `headers`, et le terme recherché par celui du dictionnaire `params`. Pour afficher la liste complète des options qui peuvent être utilisées pour filtrer les résultats de recherche, consultez la documentation sur [l’API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L’objet `search_results` contient les articles d’actualités pertinents, ainsi que des métadonnées enrichies. Par exemple, la ligne de code suivante extrait les descriptions des articles.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Ces descriptions peuvent ensuite être restituées sous la forme d’un tableau, avec le mot clé de recherche surligné en **gras**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Pagination des actualités](paging-news.md)
> [Utiliser des marqueurs d’ornement pour surligner du texte](hit-highlighting.md)

## <a name="see-also"></a>Voir aussi 

 [Recherche d’actualités sur le Web](search-the-web.md)  
 [Essayer](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
