---
title: Qu’est-ce que l’API Recherche visuelle Bing ?
titleSuffix: Azure Cognitive Services
description: La Recherche visuelle Bing fournit des détails ou des informations sur une image, comme des images similaires ou des sources d’achat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 85240f1f44278cec0f76c9b4e2e79aa065224840
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886293"
---
# <a name="what-is-bing-visual-search-api"></a>Qu’est-ce que l’API Recherche visuelle Bing ?

L’API Recherche visuelle de Bing fournit une expérience similaire aux détails de l’image illustrés sur Bing.com/images. Avec la recherche visuelle, vous pouvez charger une image et obtenir des informations sur celle-ci, comme des images visuellement similaires, des sources d’achat, des pages web qui incluent l’image, et bien plus encore. Au lieu de charger une image, vous pouvez également fournir un jeton Insights que vous obtenez à partir d’une image des résultats de recherche (voir l’[API Recherche d’images Bing](../bing-image-search/overview.md)).

La recherche visuelle peut identifier des célébrités, des monuments et repères, des œuvres d’art, des meubles, des articles de mode, des produits, des caractères (OCR) et bien plus encore.

Voici les informations que la recherche visuelle vous permet de découvrir.

- Images visuellement similaires &mdash; Liste d’images qui sont visuellement semblables à l’image d’entrée
- Produits visuellement similaires &mdash; Liste d’images qui contiennent des produits visuellement semblables à celui illustré dans l’image d’entrée
- Sources d’achat &mdash; Liste des lieux où vous pouvez acheter l’article affiché dans l’image d’entrée
- Recherches associées &mdash; Liste des recherches associées effectuées par d’autres personnes ou basées sur le contenu de l’image
- Pages web où figure l’image &mdash; Liste des pages web qui contiennent l’image d’entrée
- Recettes &mdash; Liste des pages web qui comportent des recettes pour préparer le plat illustré dans l’image d’entrée

En plus de ces informations, la recherche visuelle renvoie également un ensemble varié de termes (balises) dérivés de l’image d’entrée. Ces balises permettent aux utilisateurs d’explorer les concepts de l’image. Par exemple, si l’image d’entrée représente un athlète célèbre, une des balises peut être le nom du sportif et une autre balise peut être Sport. Ou, si l’image d’entrée représente une tarte aux pommes, les balises peuvent être Tarte aux pommes, Tarte ou Desserts, afin que les utilisateurs puissent explorer les concepts connexes.

Les résultats de la recherche visuelle incluent également des rectangles englobants pour les zones d’intérêt de l’image. Par exemple, si l’image représente plusieurs célébrités, les résultats peuvent inclure des rectangles englobants pour chacune des célébrités reconnues dans l’image. Ou, si Bing reconnaît un produit ou un vêtement dans l’image, le résultat peut inclure un rectangle englobant pour le produit ou le vêtement reconnu.

> [!IMPORTANT]
> Si vous avez recours au point de terminaison /images/détails pour [obtenir des informations sur l’image](../bing-image-search/image-insights.md), vous devez mettre à jour votre code pour utiliser la recherche visuelle, car elle fournit des informations plus complètes.


## <a name="the-request"></a>Requête

Voici les options permettant d’obtenir des informations sur une image. 

- Envoyer un jeton Insights que vous obtenez à partir d’une image dans un appel précédent à l’un des points de terminaison de l’[API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
- Envoyer l’URL d’une image
- Charger une image (binaire)


Si vous envoyez à la recherche visuelle un jeton d’image ou une URL, voici l’objet JSON que vous devez inclure dans le corps de la requête POST. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

L’objet `imageInfo` doit inclure le champ `url` ou `imageInsightsToken`, mais pas les deux. Définissez le champ `url` sur l’URL d’une image accessible via Internet. La taille d’image maximale prise en charge est 1 Mo.

`imageInsightsToken` doit être défini sur un jeton Insights. Pour obtenir un jeton Insights, appelez l’API Recherche d’images Bing. La réponse contient une liste d’objets `Image`. Chaque objet `Image` contient un champ `imageInsightsToken` renfermant le jeton.

Le champ `cropArea` est facultatif. La zone de rognage spécifie les coins supérieur gauche et inférieur droit d’une zone d’intérêt. Spécifiez les valeurs dans la plage entre 0,0 – 1,0. Les valeurs sont un pourcentage de la largeur ou hauteur globale. Par exemple, l’exemple ci-dessus marque la moitié droite de l’image en tant que zone d’intérêt. Incluez ce champ si vous souhaitez limiter la requête d’informations à la zone d’intérêt.

L’objet `filters` contient un filtre de site (voir le champ `site`) que vous pouvez utiliser pour limiter les images similaires et les résultats de produits similaires pour un domaine spécifique. Par exemple, si l’image représente un ordinateur Surface Book, vous pouvez définir `site` sur www.microsoft.com. 

Si vous souhaitez obtenir des informations sur une copie locale d’une image, chargez l’image en tant que données binaires.

Pour plus d’informations sur l’inclusion de ces options dans le corps de la requête POST, consultez les [types de formulaire de contenu](#content-form-types).


### <a name="endpoint"></a>Point de terminaison

Le point de terminaison de la recherche visuelle est : https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Les requêtes doivent être envoyées uniquement en tant que requêtes HTTP POST. 


### <a name="query-parameters"></a>Paramètres de requête

Votre requête doit spécifier les paramètres suivants. Au minimum, vous devez inclure le paramètre de requête `mkt`.

|NOM|Valeur|type|Obligatoire|  
|----------|-----------|----------|--------------|  
|<a name="cc" />cc|Code à 2 caractères du pays d'où proviennent les résultats.<br /><br /> Si vous définissez ce paramètre, vous devez également spécifier l’en-tête [Accept-Language](#acceptlanguage). Bing utilise la première langue prise en charge qu’il trouve dans la liste et associe la langue au code de pays que vous spécifiez pour déterminer le marché à partir duquel renvoyer les résultats. Si la liste des langues n’inclut de langue prise en charge, Bing recherche la langue et le marché les plus proches qui prennent en charge la requête. Sinon, il peut utiliser un marché agrégé ou par défaut pour les résultats au lieu de celui spécifié.<br /><br /> Vous ne devez utiliser ce paramètre de requête et le paramètre de requête `Accept-Language` que si vous spécifiez plusieurs langues ; sinon, vous devez utiliser les paramètres de requête `mkt` et `setLang`.<br /><br /> Ce paramètre et le paramètre de requête [mkt](#mkt) s’excluent mutuellement &mdash; ne spécifiez pas les deux.|Chaîne|Non |  
|<a name="mkt" />mkt|Marché d’où proviennent les résultats. <br /><br /> **Remarque :** il est recommandé de toujours spécifier le marché, s’il est connu. Le fait d’indiquer le marché aide Bing à router la requête et à renvoyer une réponse appropriée et optimale.<br /><br /> Ce paramètre et le paramètre de requête [cc](#cc) s’excluent mutuellement &mdash; ne spécifiez pas les deux.|Chaîne|Oui|  
|<a name="safesearch" />safeSearch|Filtre utilisé pour filtrer le contenu pour adultes. Voici les valeurs possibles. Elles ne sont pas sensibles à la casse.<br /><ul><li>Désactivé &mdash; Renvoyer les pages web avec le texte ou les images pour adultes.<br /><br/></li><li>Modéré &mdash; Renvoyer les pages web avec le texte pour adultes, mais pas les images pour adultes.<br /><br/></li><li>Strict &mdash; Ne pas renvoyer les pages web avec le texte ou les images pour adultes.</li></ul><br /> Par défaut, la valeur est Modéré.<br /><br /> **Remarque :** si la requête provient d’un marché où la stratégie de Bing en matière de contenu pour adultes requiert que la valeur Strict pour `safeSearch`, Bing ignore la valeur `safeSearch` et utilise Strict.<br/><br/>**Remarque :** si vous utilisez l’opérateur de requête `site:`, il est possible que la réponse présente du contenu pour adultes, et ce quel que soit le paramètre de requête `safeSearch` défini. N’utilisez `site:` que si vous connaissez le contenu du site et si votre scénario accepte le contenu pour adultes. |Chaîne|Non |  
|<a name="setlang" />setLang|Langue à utiliser pour les chaînes de l’interface utilisateur. Spécifiez la langue en utilisant le code de langue ISO 639-1 à deux lettres. Par exemple, celui de l’anglais est EN. La valeur par défaut est EN (anglais).<br /><br /> Nous vous conseillons de toujours indiquer la langue, bien qu’elle soit facultative. En général, on définit `setLang` sur la langue spécifiée par `mkt`, sauf si l’utilisateur souhaite que les chaînes de l’interface utilisateur soient affichées dans une autre langue.<br /><br /> Ce paramètre et l’en-tête [Accept-Language](#acceptlanguage) s’excluent mutuellement &mdash; ne spécifiez pas les deux.<br /><br /> Une chaîne d’interface utilisateur est une chaîne utilisée comme étiquette dans une interface utilisateur. Les objets de réponse JSON en comportent quelques-unes. En outre, les liens vers les propriétés Bing.com dans les objets de la réponse s’appliquent à la langue spécifiée.|Chaîne|Non | 

### <a name="headers"></a>headers

Votre requête doit spécifier les en-têtes suivants. Les en-têtes Content-Type et Ocp-Apim-Subscription-Key sont les seuls en-têtes obligatoires, mais vous devriez également inclure User-Agent, X-MSEdge-ClientID, X-MSEdge-ClientIP et X-Search-Location.


|En-tête|Description|  
|------------|-----------------|  
|<a name="acceptlanguage" />Accept-Language|En-tête de demande facultatif.<br /><br /> Liste délimitée par des virgules des langues à utiliser pour les chaînes d’interface utilisateur. Elle est triée par ordre de préférence décroissant. Pour plus d’informations, notamment le format attendu, voir [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Cet en-tête et le paramètre de requête [setLang](#setlang) s’excluent mutuellement &mdash; ne spécifiez pas les deux.<br /><br /> Si vous définissez cet en-tête, vous devrez également spécifier le paramètre de requête [cc](#cc). Pour déterminer pour quel marché les résultats devront être retournés, Bing utilise la première langue prise en charge qu’il trouve dans la liste et la combine avec la valeur du paramètre `cc`. Si la liste ne comporte pas de langue prise en charge, Bing recherche la langue et le marché les plus proches qui prennent en charge la demande, ou utilise un marché agrégé ou par défaut pour les résultats. Pour identifier le marché utilisé par Bing, voir l’en-tête BingAPIs-Market.<br /><br /> N’utilisez cet en-tête et le paramètre de requête `cc` que si vous spécifiez plusieurs langues. Sinon, utilisez les paramètres de requête [mkt](#mkt) et [setLang](#setlang).<br /><br /> Une chaîne d’interface utilisateur est une chaîne utilisée comme étiquette dans une interface utilisateur. Les objets de réponse JSON en comportent quelques-unes. Les liens vers les propriétés Bing.com dans les objets de la réponse s’appliquent à la langue spécifiée.|  
|<a name="contenttype" />Content-Type|En-tête de requête requis.<br /><br />Doit avoir la valeur multipart/form-data et inclure un paramètre boundary (par exemple, multipart/form-data; boundary=\<chaîne de limite\>). Pour plus d’informations, consultez [Types de formulaires de contenu](#content-form-types).
|<a name="market" />BingAPIs-Market|En-tête de réponse.<br /><br /> Marché utilisé par la demande. Le format est \<code de langue\>-\<code du pays\>. Par exemple, en-US.|  
|<a name="traceid" />BingAPIs-TraceId|En-tête de réponse.<br /><br /> ID de l’entrée du journal contenant les détails de la demande. Lorsqu’une erreur se produit, capturez cet ID. Si vous ne parvenez pas à identifier ou à résoudre le problème, précisez cet ID avec les autres informations envoyées à l’équipe de support.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|En-tête de demande requis.<br /><br /> Clé d’abonnement reçue lors de l’inscription à ce service dans [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|En-tête de demande facultatif.<br /><br /> Par défaut, Bing retourne le contenu en cache, s’il est disponible. Pour éviter cela, définissez l’en-tête Pragma sur no-cache (par exemple, Pragma: no-cache).
|<a name="useragent" />User-Agent|En-tête de demande facultatif.<br /><br /> Agent utilisateur à l’origine de la requête. Bing utilise l’agent utilisateur pour offrir une expérience optimisée aux utilisateurs mobiles. Nous vous conseillons de toujours indiquer cet en-tête, bien qu’il soit facultatif.<br /><br /> L’agent utilisateur doit correspondre à la chaîne envoyée par n’importe quel navigateur couramment utilisé. Pour plus d’informations sur les agents utilisateurs, voir [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Voici quelques exemples de chaînes de l’agent utilisateur.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible ; MSIE 10.0 ; Windows Phone 8.0 ; Trident/6.0 ; IEMobile/10.0 ; ARM ; Touch ; NOKIA ; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux ; U ; Android 2.3.5 ; en-us ; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML ; comme Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone ; CPU iPhone OS 6_1 comme Mac OS X) AppleWebKit/536.26 (KHTML ; comme Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3 ; WOW64 ; Trident/7.0 ; Touch ; rv:11.0) comme Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad ; CPU OS 7_0 comme Mac OS X) AppleWebKit/537.51.1 (KHTML, comme Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|En-tête de demande et de réponse facultatif.<br /><br /> Bing utilise cet en-tête pour garantir aux utilisateurs un comportement cohérent d’un appel d’API Bing à l’autre. Bing propose souvent de nouvelles fonctionnalités et améliorations, et se sert de l’ID client comme d’une clé pour attribuer le trafic aux différentes versions d’évaluation. Si vous n’assignez pas le même ID client à un utilisateur d’une demande à l’autre, Bing est susceptible d’affecter cet utilisateur à plusieurs versions d’évaluation en conflit, ce qui risque de nuire à l’expérience utilisateur. Par exemple, si la deuxième demande comporte une attribution de version d’évaluation différente de la première, l’expérience se révélera peut-être inattendue. De même, Bing peut utiliser l’ID client pour personnaliser les résultats web d’après l’historique de recherche correspondant à cet ID et ainsi proposer à l’utilisateur une expérience plus riche.<br /><br /> Bing utilise également cet en-tête pour aider à améliorer le classement des résultats en analysant l’activité générée par un ID client. L’amélioration de la pertinence permet d’obtenir des résultats de meilleure qualité de la part des API Bing et, en retour, des taux de clic plus élevés pour le consommateur des API.<br /><br /> **IMPORTANT :** Il est vivement recommandé d’indiquer cet en-tête, bien qu’il soit facultatif. Grâce à la persistance de l’ID client dans plusieurs demandes pour une même combinaison appareil/utilisateur final, (1) le consommateur des API bénéficie d’une expérience utilisateur cohérente et (2) le taux de clic est plus élevé du fait des résultats de meilleure qualité provenant des API Bing.<br /><br /> Voici les règles d’utilisation de base qui s’appliquent à cet en-tête.<br /><ul><li>Chaque utilisateur de votre application sur l’appareil doit avoir un ID client unique, généré par Bing.<br /><br/>Si vous n’insérez pas cet en-tête dans la demande, Bing génère un ID et le retourne dans l’en-tête de réponse X-MSEdge-ClientID. Sachez néanmoins que la seule fois où vous ne devez PAS inclure cet en-tête dans une requête, c’est la première fois où l’utilisateur utilise votre application sur cet appareil.<br /><br/></li><li>**ATTENTION :** vous devez vous assurer que cet ID client ne peut pas être associé à des informations de compte utilisateur authentifiables.</li><li>Utilisez l’ID client pour chaque requête d’API Bing qu’effectue votre application pour cet utilisateur sur l’appareil.<br /><br/></li><li>Conservez l’ID client. Pour conserver l’identifiant dans une application de navigateur, utilisez un cookie HTTP persistant qui garantit l’utilisation de cet identifiant dans toutes les sessions. N’utilisez pas de cookie de session. Dans le cas d’autres applications, comme des applications mobiles, utilisez le stockage persistant de l’appareil pour conserver cet identifiant.<br /><br/>La prochaine fois que l’utilisateur utilisera votre application sur cet appareil, vous récupérerez l’ID client que vous aurez conservé.</li></ul><br /> **REMARQUE :** Les réponses de Bing ne comportent pas forcément cet en-tête. Si elles l’incluent, capturez l’ID client et utilisez-le pour toutes les demandes Bing suivantes concernant l’utilisateur sur cet appareil.<br /><br /> **REMARQUE :** Si vous insérez l’en-tête X-MSEdge-ClientID, n’incluez pas les cookies dans la demande.|  
|<a name="clientip" />X-MSEdge-ClientIP|En-tête de demande facultatif.<br /><br /> Adresse IPv4 ou IPv6 de l’appareil client. L’adresse IP est utilisée pour découvrir l’emplacement de l’utilisateur. Bing utilise les informations de localisation pour déterminer le comportement de recherche approprié.<br /><br /> **REMARQUE :** Nous vous conseillons de toujours indiquer cet en-tête et l’en-tête X-Search-Location, bien qu’ils soient facultatifs.<br /><br /> N’obfusquez pas l’adresse (par exemple, en remplaçant le dernier octet par 0). Cela aurait pour effet d’éloigner la localisation de l’emplacement réel de l’appareil, ce qui pourrait conduire Bing à retourner des résultats erronés.|  
|<a name="location" />X-Search-Location|En-tête de demande facultatif.<br /><br /> Liste délimitée par des points-virgules de paires clé/valeur qui décrivent la situation géographique du client. Bing utilise les informations de localisation pour déterminer le comportement de recherche approprié et retourner le contenu local pertinent. Spécifiez la paire clé/valeur sous la forme \<clé\>:\<valeur\>. Voici les clés qui vous permettent de spécifier la localisation de l’utilisateur.<br /><br /><ul><li>lat &mdash; Obligatoire. Latitude de la localisation du client, en degrés. La valeur doit être supérieure ou égale à -90 et inférieure ou égale à +90. Les valeurs négatives indiquent les latitudes sud et les valeurs positives indiquent les latitudes nord.<br /><br /></li><li>long &mdash; Obligatoire. Longitude de la localisation du client, en degrés. La valeur doit être supérieure ou égale à -180 et inférieure ou égale à +180. Les valeurs négatives indiquent les longitudes occidentales et les valeurs positives indiquent les longitudes orientales.<br /><br /></li><li>re &mdash; Obligatoire. Rayon, en mètres, qui spécifie la précision horizontale des coordonnées. Transmettez la valeur renvoyée par le service de localisation de l’appareil. Les valeurs standard peuvent être 22 m pour le GPS/Wi-Fi, 380 m pour la triangulation des stations cellulaires et 18 000 m pour la recherche IP inversée.<br /><br /></li><li>ts &mdash; Facultatif. Horodatage UTC UNIX correspondant au moment où le client était à cette position. (L’horodatage UNIX est le nombre de secondes écoulées depuis le 1er janvier 1970.)<br /><br /></li><li>head &mdash; Facultatif. Direction relative du client en déplacement. Spécifiez-la en degrés, entre 0 et 360, dans le sens horaire par rapport au nord géographique. Spécifiez cette clé uniquement si la clé `sp` est différente de zéro.<br /><br /></li><li>sp &mdash; Facultatif. Vitesse horizontale, en mètres par seconde, à laquelle se déplace l’appareil client.<br /><br /></li><li>alt &mdash; Facultatif. Altitude de l’appareil client, en mètres.<br /><br /></li><li>are &mdash; Facultatif. Rayon, en mètres, qui spécifie la précision verticale des coordonnées. Spécifiez cette clé uniquement si vous indiquez la clé `alt`.<br /><br /></li></ul> **REMARQUE :** bien que la plupart des clés soient facultatives, plus vous fournissez d’informations, plus les résultats de localisation sont précis.<br /><br /> **REMARQUE :** nous vous conseillons de toujours indiquer la situation géographique de l’utilisateur (qui est facultative). La localisation est particulièrement importante si l’adresse IP du client ne reflète pas exactement l’emplacement physique de l’utilisateur (par exemple, si le client utilise un VPN). Pour des résultats optimaux, précisez cet en-tête et l’en-tête X-MSEdge-ClientIP ; au minimum, indiquez cet en-tête.|

> [!NOTE] 
> N’oubliez pas que les conditions d’utilisation imposent le respect de toutes les lois en vigueur, y compris en ce qui concerne l’utilisation de ces en-têtes. Par exemple, dans certaines juridictions, comme en Europe, il faut obtenir le consentement de l’utilisateur avant de placer certains dispositifs de suivi sur les appareils des utilisateurs.


<a name="content-form-types" />

### <a name="content-form-types"></a>Types de formulaires de contenu

Chaque requête doit inclure l’en-tête Content-Type. L’en-tête doit être défini sur : multipart/form-data; boundary=\<chaîne de limite\>, où \<chaîne de limite\> est une chaîne opaque unique qui identifie la limite des données de formulaire. Par exemple, boundary=boundary_1234-abcd.


Si vous envoyez à la recherche visuelle un jeton d’image ou une URL, voici les données de formulaire que vous devez inclure dans le corps de la requête POST. Les données de formulaire doivent inclure l’en-tête Content-Disposition, et le paramètre `name` doit être défini sur « knowledgeRequest ». Pour plus d’informations sur l’objet `imageInfo`, voir la [requête](#the-request).


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Si vous chargez une image locale, voici les données de formulaire que vous devez inclure dans le corps de la requête POST. Les données du formulaire doivent inclure l’en-tête Content-Disposition. Le paramètre `name` doit être défini sur « image », tandis que le paramètre `filename` peut être défini sur une chaîne quelle qu’elle soit. L’en-tête Content-Type peut être défini sur n’importe quel type de mime image couramment utilisé. Le contenu du formulaire correspond au fichier binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo. La plus grande largeur ou hauteur doit être de 1 500 pixels ou moins.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Voici comment spécifier la zone d’intérêt de l’image chargée.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Exemple de requête

Voici une requêteInsights d’image complète qui transmet un jeton de l’image et une zone d’intérêt. Vous obtenez le jeton Insights à partir d’un appel précédent à /images/search.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>Réponse

Si des informations sont disponibles pour l’image, la réponse contient un ou plusieurs `tags`, qui renferment les informations. Le champ `image` contient le jeton Insights pour l’image d’entrée.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Le champ `tags` contient un nom d’affichage et une liste d’actions (Insights). Une des balises contient un champ `displayName` qui est défini sur une chaîne vide. Cette balise contient les informations par défaut (telles que des pages web où figure l’image, les images visuellement similaires et les sources d’achat pour les articles présents sur l’image). L’image entière étant digne d’intérêt, la balise Insights par défaut n’inclut pas de rectangles englobants pour les zones d’intérêt.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Pour obtenir la liste des informations par défaut, voir les [informations par défaut](./default-insights-tag.md).



Les balises restantes contiennent d’autres informations éventuellement utiles à l’utilisateur. Par exemple, si l’image contient du texte, une des balises peut inclure une information TextResults, avec le texte reconnu. Ou alors, si Bing reconnaît une entité (personne, lieu ou chose) dans l’image, une des balises peut identifier l’entité. La recherche visuelle renvoie également un ensemble varié de termes (balises) dérivés de l’image d’entrée. Ces balises permettent aux utilisateurs d’explorer les concepts de l’image. Par exemple, si l’image d’entrée représente un athlète célèbre, une des balises peut être Sports et contenir des liens vers des images de sports.

Chaque balise inclut un nom d’affichage que vous pouvez utiliser pour catégoriser les informations, un rectangle englobant qui identifie la zone d’intérêt correspondant aux informations, les informations elles-mêmes et une miniature de l’image. Par exemple, si l’image représente une personne portant le maillot d’une équipe sportive, une des balises peut inclure un rectangle englobant qui délimite le maillot et inclut des données VisualSearch et ProductVisualSearch. Et une autre balise peut inclure des informations ImageResults avec une URL pour une requête d’API /images/search afin d’obtenir des images sur un sujet connexe ou une URL de recherche Bing.com qui dirige l’utilisateur vers les résultats de recherche d’images Bing.com.

Les balises autres que celui par défaut incluent des rectangles englobants qui identifient des zones d’intérêt dans l’image. Par exemple, si l’image inclut plusieurs personnes reconnue, les balises peut inclure des rectangles englobants pour chacune de ces personnes, ou si l’image contient des vêtements reconnus, les balises peuvent inclure des rectangles englobants pour chaque vêtement reconnu. Vous pouvez utiliser des rectangles englobants pour créer des zones réactives sur l’image qui, lorsque vous cliquez dessus, fournissent des détails sur le contenu de cette zone de l’image. Vous ne devez pas inclure de zones réactives dans une image pour les rectangles englobantes qui identifient l’image entière.

### <a name="text-recognition"></a>Reconnaissance de texte

Si l’image contient du texte que le service reconnaît, une des balises contient des informations TextResults (action). Cet élément `displayName` contient le texte reconnu. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Étant donné que le champ `displayName` de la balise contient ##TextRecognition, ne l’utilisez pas comme une catégorie de titre dans l’expérience utilisateur. Il en va de même pour n’importe quel nom d’affichage qui commence par ##. Utilisez le nom d'affichage de l’action à la place.


La reconnaissance de texte peut aussi reconnaître les coordonnées sur des cartes de visite, telles que les numéros de téléphone et les adresses de messagerie. Le rectangle englobant identifie la position des coordonnées sur la carte. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Si l’image contient une entité reconnue, comme une personne, un lieu ou un objet, une des balises peut inclure des informations sur l’entité. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>Étapes suivantes

Pour configurer rapidement votre première requête, consultez les guides de démarrage rapide : [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

Essayez l’API. Accédez à la [console de test de l’API Recherche visuelle](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Familiarisez-vous avec les informations de référence sur [l’API Recherche visuelle](https://aka.ms/bingvisualsearchreferencedoc). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse. 

Veillez à lire les [exigences relatives à l’affichage et à l’utilisation des API Bing](./use-and-display-requirements.md) pour respecter les règles d’utilisation des résultats de la recherche.


