---
title: Supprimer des données de l’Explorateur de données Azure
description: Cet article décrit les scénarios de suppression en bloc dans l’Explorateur de données Azure, notamment les suppressions basées sur la conservation et le vidage.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 07ee05128333df963c2d8ff3dd3f6442a3843d07
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48866888"
---
# <a name="delete-data-from-azure-data-explorer"></a>Supprimer des données de l’Explorateur de données Azure

L’Explorateur de données Azure prend en charge plusieurs approches de suppression en bloc, lesquelles sont abordées dans cet article. Il prend seulement en charge la suppression par enregistrement en temps réel, car il est optimisé pour un accès en lecture rapide.

* Si une ou plusieurs tables ne sont plus nécessaires, supprimez-les à l’aide de la commande drop table ou drop tables.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Si des données anciennes ne sont plus nécessaires, supprimez-les en changeant la période de conservation au niveau de la base de données ou de la table.

    Prenons l’exemple d’une base de données ou d’une table qui est définie pour être conservée 90 jours. Les besoins métier changent. Maintenant, 60 jours de données suffisent. Dans ce cas, supprimez les anciennes données de l’une des manières suivantes.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Pour plus d’informations, consultez [Stratégie de conservation](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

Si vous avez besoin d’aide pour résoudre des problèmes de suppression de données, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com).
