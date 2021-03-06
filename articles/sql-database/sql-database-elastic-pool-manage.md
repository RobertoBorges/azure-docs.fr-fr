---
title: Créer et gérer des pools élastiques – Azure SQL Database | Microsoft Docs
description: Créez et gérez des pools élastiques SQL Azure.
services: sql-database
ms.service: sql-database
subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 0c939956a8f3336b5071748a8c2bdf8840b749ad
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466067"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Créer et gérer des pools élastiques dans Azure SQL Database

Avec un pool élastique, on détermine la quantité de ressources qui lui sont nécessaires pour gérer la charge de travail de ses bases de données ainsi que la quantité de ressources allouées à chaque base de données mise en pool.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portail Azure : Gérer des pools élastiques et des bases de données mises en pool

Tous les paramètres du pool se trouvent au même endroit : le panneau **Configurer le pool**. Pour accéder au panneau, recherchez un pool élastique dans le portail, puis cliquez sur **Configurer le pool** en haut du panneau ou dans le menu des ressources situé sur la gauche.

Vous pouvez effectuer n’importe quelle combinaison de modifications parmi les suivantes, et les enregistrer dans un même ensemble :

1. Modifier le niveau de service du pool
2. Mettre à l’échelle les performances (DTU ou vCore) et le stockage
3. Ajouter ou supprimer des bases de données dans le pool
4. Définir un niveau minimal (garanti) et maximal de performance pour les bases de données des pools
5. Examiner le récapitulatif des coûts pour voir toutes les modifications apportées à votre facture depuis vos nouvelles sélections

![Panneau de configuration du pool élastique](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell : Gérer des pools élastiques et des bases de données mises en pool

Pour créer et gérer les pools élastiques SQL Database et des bases de données mises en pool avec Azure PowerShell, utilisez les cmdlets PowerShell suivantes. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). Pour créer et gérer les serveurs logiques pour un pool élastique, consultez l’article décrivant comment [créer et gérer des serveurs logiques](sql-database-logical-servers.md). Pour créer et gérer des règles de pare-feu, consultez l’article expliquant comment [créer et gérer des règles de pare-feu à l’aide de PowerShell](sql-database-firewall-configure.md#manage-firewall-rules-using-azure-powershell).

> [!TIP]
> Pour obtenir des exemples de scripts PowerShell, consultez [Créer des pools élastiques et déplacer les bases de données entre les pools et en dehors d’un pool à l’aide de PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) et [Utiliser PowerShell pour surveiller et mettre à l’échelle un pool élastique SQL dans Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Crée un pool de bases de données élastique sur un serveur SQL logique.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtient les pools élastiques et leurs valeurs de propriété sur un serveur SQL logique.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifie les propriétés d’un pool de bases de données élastique sur un serveur SQL logique. Par exemple, utilisez la propriété **StorageMB** pour modifier le stockage maximal d’un pool élastique.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Supprime un pool de bases de données élastique sur un serveur SQL logique.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtient l’état des opérations sur un pool élastique sur un serveur SQL logique.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données dans un pool existant ou en tant que base de données seule. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique, en dehors de celui-ci ou entre des pools élastiques.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Supprime une base de données.|

> [!TIP]
> La création d’un grand nombre de bases de données dans un pool élastique peut prendre du temps si elle se fait par le biais du portail ou d’applets de commande PowerShell qui créent une seule base de données à la fois. Pour automatiser la création dans un pool élastique, consultez [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI : Gérer des pools élastiques et des bases de données mises en pool

Pour créer et gérer un serveur des pools élastiques avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows.

> [!TIP]
> Pour obtenir des exemples de scripts Azure CLI , consultez [Utiliser l’interface CLI afin de déplacer une base de données SQL Azure dans un pool élastique SQL](scripts/sql-database-move-database-between-pools-cli.md) et [Utiliser Azure CLI pour mettre un pool élastique SQL à l’échelle dans Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Applet de commande | Description |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Crée un pool élastique.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Renvoie une liste de pools élastiques dans un serveur.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Renvoie une liste des bases de données dans un pool élastique.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Inclut également les paramètres DTU de pool, les limites de stockage et les paramètres par base de données disponibles. Pour réduire les détails, les limites de stockage supplémentaires et les paramètres par base de données sont masqués par défaut.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Met à jour un pool élastique.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Supprime le pool élastique.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL : Gérer des bases de données mises en pool

Pour créer et déplacer des bases de données dans les pools élastiques existants ou pour renvoyer des informations sur un pool élastique SQL Database avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour créer et gérer des règles de pare-feu à l’aide de T-SQL, consultez l’article [Gérer les règles de pare-feu à l’aide de Transact-SQL](sql-database-firewall-configure.md#manage-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Vous ne pouvez pas créer, mettre à jour ou supprimer un pool élastique SQL Database à l’aide de Transact-SQL. Vous pouvez ajouter ou supprimer des bases de données à partir d’un pool élastique, et vous pouvez utiliser des vues de gestion dynamiques pour renvoyer des informations sur les pools élastiques existants.
>

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données dans un pool existant ou en tant que base de données seule. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Déplace une base de données dans un pool élastique, en dehors de celui-ci ou entre des pools élastiques.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Renvoie les statistiques d’utilisation de ressources pour tous les pools de bases de données élastiques dans un serveur logique. Pour chaque pool de bases de données élastique, il existe une ligne pour chaque fenêtre de création de rapports de 15 secondes (quatre lignes par minute). Cela inclut la consommation de stockage, le journal, les E/S, l’UC et l’utilisation de session/requête simultanée par toutes les bases de données du pool.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>API REST : Gérer des pools élastiques et des bases de données mises en pool

Pour créer et gérer des pools élastiques SQL Database et des bases de données mises en pool, utilisez ces demandes d’API REST.

| Commande | Description |
| --- | --- |
|[Pools élastiques - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_createorupdate)|Crée un pool élastique ou met à jour un pool élastique existant.|
|[Pools élastiques - Supprimer](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_delete)|Supprime le pool élastique.|
|[Pools élastiques - Obtenir](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_get)|Obtenir un pool élastique.|
|[Pools élastiques - Lister par serveur](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_listbyserver)|Renvoie une liste de pools élastiques dans un serveur.|
|[Pools élastiques - Mettre à jour](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_listbyserver)|Met à jour un pool élastique existant.|
|[Activités de pool élastique](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Retourne les activités de pool élastique.|
|[Activités de bases de données du pool élastique](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Retourne l’activité sur les bases de données à l’intérieur d’un pool élastique.|
|[Bases de données - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Obtenir](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtient une base de données.|
|[Bases de données - Lister par pool élastique](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Bases de données - Lister par serveur](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/update)|Met à jour une base de données existante.|

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez aussi regarder la vidéo [Formation vidéo Microsoft Virtual Academy sur les fonctions de bases de données élastiques dans Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).
* Pour obtenir un didacticiel SaaS utilisant des pools élastiques, consultez [Présentation de l’application SaaS Wingtip](sql-database-wtp-overview.md).
