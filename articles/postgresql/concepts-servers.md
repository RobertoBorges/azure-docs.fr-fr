---
title: Concepts de serveur dans Azure Database pour PostgreSQL
description: Cet article indique les éléments à prendre en considération et fournit des instructions pour configurer et gérer des serveurs Azure Database pour PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 8fcb5e8371d6c813eb7f0ab4d23a5aac5c41fb3b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404637"
---
# <a name="azure-database-for-postgresql-servers"></a>Serveurs Azure Database pour PostgreSQL
Cet article présente des considérations et des instructions relatives à l’utilisation des serveurs Azure Database pour PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Qu’est-ce qu’un serveur de base de données Azure pour PostgreSQL ?
Un serveur de base de données Azure pour PostgreSQL est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur PostgreSQL que vous connaissez peut-être en local. Plus précisément, le service PostgreSQL est géré, fournit des garanties de performances et propose des fonctionnalités et des accès au niveau du serveur.

Un serveur de base de données Azure pour PostgreSQL :

- Est créé dans un abonnement Azure.
- Représente la ressource parente des bases de données.
- Fournit un espace de noms aux bases de données.
- Constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données autonomes qu’il contient).
- Colocalise les ressources d’une région.
- Fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données (.postgresql.database.azure.com).
- Fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc.
- Est disponible dans plusieurs versions. Pour plus d’informations, consultez [Versions de bases de données PostgreSQL prises en charge](concepts-supported-versions.md).
- Peut être étendu par les utilisateurs. Pour plus d’informations, consultez [Extensions de PostgreSQL](concepts-extensions.md).

Dans une base de données Azure Database pour serveur PostgreSQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Les tarifs sont structurés par serveur, en fonction de la configuration du niveau tarifaire, des vCores et du stockage (Go). Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Comment se connecter et s’authentifier auprès d’un serveur de base de données Azure pour PostgreSQL ?
Les éléments suivants permettent de garantir un accès sécurisé à votre base de données :

|||
|:--|:--|
| **Authentification et autorisation** | Le serveur de base de données Azure pour PostgreSQL prend en charge l’authentification PostgreSQL native. Vous pouvez vous connecter et vous authentifier auprès du serveur avec les informations de connexion d’administrateur du serveur. |
| **Protocole** | Le service prend en charge un protocole par messages utilisé par PostgreSQL. |
| **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. |
| **Pare-feu** | Pour renforcer la protection de vos données, une règle de pare-feu empêche tout accès à votre serveur et à ses bases de données tant que vous ne précisez pas quels ordinateurs sont autorisés. Consultez la page [Règles de pare-feu d’un serveur de base de données Azure pour PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Gestion de votre serveur
Vous pouvez gérer des serveurs Azure Database pour PostgreSQL à l’aide du [Portail Azure](https://portal.azure.com) ou [d’Azure CLI](/cli/azure/postgres).

Lors de la création d’un serveur, vous configurez les informations d’identification pour votre utilisateur administrateur. L’utilisateur administrateur est l’utilisateur ayant les privilèges les plus élevés sur le serveur. Il appartient au rôle azure_pg_admin. Ce rôle ne dispose pas de toutes les autorisations du superutilisateur. 

L’attribut de superutilisateur PostgreSQL est affecté à azure_superuser, qui appartient au service managé. Vous n’avez pas accès à ce rôle.

Un serveur Azure Database pour PostgreSQL a deux bases de données par défaut : 
- **postgres** : une base de données par défaut à laquelle vous pouvez vous connecter une fois que votre serveur est créé.
- **azure_maintenance** : cette base de données est utilisée pour séparer les processus qui fournissent le service managé des actions de l’utilisateur. Vous n’avez pas accès à cette base de données.
- **azure_sys** -Une base de données pour le Magasin des requêtes. Cette base de données n’accumule pas les données lorsque le Magasin des requêtes est désactivé ; il s’agit du paramètre par défaut. Pour plus d’informations, consultez [Vue d’ensemble du Magasin des requêtes](concepts-query-store.md).


## <a name="server-parameters"></a>Paramètres de serveur
Les paramètres de serveur PostgreSQL déterminent la configuration du serveur. Dans Azure Database pour PostgreSQL, la liste des paramètres peut être affichée et modifiée à l’aide du portail Azure ou d’Azure CLI. 

Azure Database pour PostgreSQL étant un service géré pour Postgres, ses paramètres configurables sont un sous-ensemble des paramètres d’une instance locale de Postgres (pour plus d’informations sur les paramètres de Postgres, consultez la [documentation de PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Votre serveur Azure Database pour PostgreSQL est activé avec des valeurs par défaut pour chaque paramètre lors de la création. Certains paramètres qui nécessitent un redémarrage du serveur ou un accès de superutilisateur pour que les modifications entrent en vigueur ne peuvent pas être configurés par l’utilisateur.


## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour PostgreSQL](overview.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](concepts-pricing-tiers.md).
- Pour plus d’informations sur la connexion au service, consultez la page [Bibliothèques de connexions de la base de données Azure pour PostgreSQL](concepts-connection-libraries.md).
- Affichez et modifiez les paramètres de serveur par le biais du [portail Azure](howto-configure-server-parameters-using-portal.md) ou [d’Azure CLI](howto-configure-server-parameters-using-cli.md).
