---
title: Chiffrer les données de table de stockage Azure | Microsoft Docs
description: Découvrez plus d’informations sur le chiffrement de données de table dans le Stockage Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 8a7b61705f6efc5a76212fdd1345a022f58e0686
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526464"
---
# <a name="encrypt-table-data"></a>Chiffrer des données de table
La bibliothèque cliente de stockage .NET Azure Storage prend en charge le chiffrement des propriétés de l’entité de chaîne pour les opérations d’insertion et de remplacement. Les chaînes chiffrées sont stockées sur le service en tant que propriétés binaires, et elles sont converties en chaînes après le déchiffrement.    

Pour les tables, outre la stratégie de chiffrement, les utilisateurs doivent spécifier les propriétés à chiffrer. Pour ce faire, il faut spécifier un attribut EncryptProperty \(pour les entités POCO qui dérivent de TableEntity) ou un programme de résolution de chiffrement dans les options de requête. Un programme de résolution de chiffrement est un délégué qui prend une clé de partition, une clé de ligne et un nom de propriété, puis renvoie une valeur booléenne indiquant si cette propriété doit être chiffrée. Pendant le chiffrement, la bibliothèque cliente utilise ces informations pour décider s’il faut chiffrer une propriété pendant l’écriture en ligne. Le délégué fournit également la possibilité de définir la manière dont les propriétés sont chiffrées l’aide d’un programme logique. (Par exemple, si X, alors chiffrer la propriété A ; sinon chiffrer les propriétés A et B.) Il n’est pas nécessaire de fournir ces informations pendant la lecture ou l’interrogation des entités.

## <a name="merge-support"></a>Prise en charge de la fusion

La fusion n’est pas prise en charge pour le moment. Comme un sous-ensemble de propriétés a peut-être été chiffré précédemment à l’aide d’une clé différente, la fusion des nouvelles propriétés et la mise à jour des métadonnées entraîne une perte de données. L’opération de fusion nécessite d’effectuer des appels de service supplémentaires pour lire l’entité pré-existante à partir du service ou d’utiliser une nouvelle clé par propriété. Ces deux solutions ne conviennent pas pour des raisons de performances.     

Pour plus d’informations sur le chiffrement des données de table, consultez [Chiffrement côté client et coffre de clés Azure pour Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Étapes suivantes

- [Modèles de conception de table](table-storage-design-patterns.md)
- [Modélisation des relations](table-storage-design-modeling.md)
- [Modélisation des relations](table-storage-design-modeling.md)
- [Conception pour la modification de données](table-storage-design-for-modification.md)