---
title: Exemple de la stratégie de Gestion des API Azure - Filtrer le contenu de la réponse | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: af362ac51fb8b7d1689451d49f2ed831c5f9ee2e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284425"
---
# <a name="filter-response-content"></a>Filtrer le contenu de la réponse

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

