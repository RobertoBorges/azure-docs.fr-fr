---
title: Présentation de la stratégie Azure
description: Azure Policy est un service dans Azure, que vous utilisez pour créer, affecter et gérer les définitions de stratégie dans votre environnement Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: dbdffc7a6f77f3f34ce7937c60eb7a53e5f72590
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961278"
---
# <a name="what-is-azure-policy"></a>Présentation d’Azure Policy

La gouvernance informatique garantit que votre organisation est en mesure d’atteindre ses objectifs via une utilisation efficace de l’informatique. Pour cela, elle clarifie les objectifs de l’entreprise et les projets informatiques.

Votre société rencontre un nombre important de problèmes informatiques qui ne semblent jamais résolus ?
Une bonne gouvernance informatique implique la planification de vos initiatives et la définition de priorités à un niveau stratégique. De cette manière, vous pouvez gérer et éviter les problèmes de manière plus efficace. C’est ici qu’Azure Policy entre en jeu.

Azure Policy est un service d’Azure que vous utilisez pour créer, affecter et gérer des stratégies. Ces stratégies appliquent différentes règles et effets sur vos ressources, qui restent donc conformes aux normes et aux contrats de niveau de service de l’entreprise. Pour ce faire, Azure Policy effectue des évaluations de vos ressources et recherche celles qui ne sont pas conformes aux stratégies que vous avez créées. Par exemple, vous pouvez disposer d’une stratégie qui n’autorise qu’une certaine taille de référence SKU de machines virtuelles dans votre environnement. Une fois que cette stratégie a été implémentée, elle sera évaluée lors de la création et la mise à jour des ressources, ainsi que pour vos ressources déjà existantes. Plus tard dans ce document, nous allons parler plus en détail de la manière de créer et d’implémenter des stratégies avec Azure Policy.

> [!IMPORTANT]
> L’évaluation de la conformité Azure Policy est désormais fournie pour toutes les affectations, quel que soit le niveau de tarification. Si vos affectations n’affichent pas les données de conformité, vérifiez que l’abonnement est inscrit auprès du fournisseur de ressources Microsoft.PolicyInsights.

## <a name="how-is-it-different-from-rbac"></a>Quelle est la différence avec RBAC ?

Il existe quelques différences importantes entre la stratégie et le contrôle d’accès en fonction du rôle (RBAC). Le contrôle RBAC porte sur les actions des utilisateurs dans différentes étendues. Par exemple, le rôle de contributeur peut vous être attribué pour un groupe de ressources dans l’étendue de votre choix. Ce rôle vous permet d’apporter des modifications à ce groupe de ressources.
La stratégie se focalise sur les propriétés des ressources pendant le déploiement et sur les ressources existantes. Par exemple, vous pouvez utiliser des stratégies pour contrôler les types de ressources qui peuvent être approvisionnées. Vous pouvez aussi restreindre les emplacements auxquels les ressources peuvent être approvisionnées. Contrairement à RBAC, la stratégie est, par défaut, un système explicite d'autorisation et de refus.

### <a name="rbac-permissions-in-azure-policy"></a>Autorisations RBAC dans Azure Policy

Azure Policy dispose d’autorisations, représentées sous la forme d’opérations, dans les deux fournisseurs de ressources différents :

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsight](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Plusieurs rôles intégrés présentent des niveaux d’autorisation différents pour les ressources Azure Policy. Par exemple, l’**administrateur de la sécurité** peut gérer les affectations et les définitions de stratégie, mais ne peut pas afficher les informations de conformité. Par ailleurs, le **lecteur** peut lire les détails concernant les affectations et définitions de stratégie, mais ne peut pas modifier ni afficher les informations de conformité. Le **propriétaire** dispose de l’intégralité des droits, tandis que le **contributeur** ne bénéficie d’aucune autorisation Azure Policy. Pour autoriser l’accès aux informations de conformité Azure Policy, créez un [rôle personnalisé](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Définition de stratégie

Pour créer et implémenter une stratégie dans Azure Policy, il faut commencer par créer une définition de stratégie. Chaque définition de stratégie présente des conditions dans lesquelles elle est appliquée. Si les conditions sont remplies, un effet d’accompagnement a lieu.

Dans Azure Policy, nous offrons quelques stratégies intégrées qui sont disponibles par défaut. Par exemple : 

- **Nécessitent SQL Server 12.0** : cette définition de stratégie a des conditions/règles garantissant que tous les serveurs SQL Server utilisent la version 12.0. Son effet consiste à refuser tous les serveurs qui ne répondent pas à ces critères.
- **Références (SKU) de compte de stockage autorisées**: cette définition de stratégie a un ensemble de conditions/règles qui déterminent si un compte de stockage qui est déployé est dans un ensemble de tailles de référence (SKU). Son effet consiste à refuser tous les comptes de stockage dont la taille ne fait pas partie de l’ensemble de tailles de références (SKU) définies.
- **Type de ressource autorisé**: cette définition de stratégie a un ensemble de conditions/règles pour spécifier les types de ressources que votre organisation peut déployer. Son effet consiste à refuser toutes les ressources qui ne font pas partie de cette liste définie.
- **Emplacements autorisés** : cette stratégie vous permet de restreindre les emplacements que votre organisation peut spécifier lors du déploiement de ressources. Son effet permet d’appliquer vos exigences de conformité géographique.
- **Références SKU de machine virtuelle autorisées** : cette stratégie permet de spécifier un ensemble de références SKU de machine virtuelle que votre organisation peut déployer.
- **Appliquer une balise et sa valeur par défaut** : cette stratégie applique une balise requise et sa valeur par défaut si elle n’est pas spécifiée par l’utilisateur.
- **Imposer une balise et sa valeur** : cette stratégie applique une balise requise et sa valeur à une ressource.
- **Types de ressource non autorisés** : cette stratégie permet de spécifier les types de ressource que votre organisation ne peut pas déployer.

Pour implémenter ces définitions de stratégie (définitions intégrées et personnalisées), vous devez les affecter. Vous pouvez affecter l’une de ces stratégies par le biais du portail Azure, de PowerShell ou d’Azure CLI.

N’oubliez pas qu’une réévaluation de la stratégie est effectuée toutes les heures. Ainsi, si vous modifiez la définition de votre stratégie après l’avoir implémentée (et donc avoir créé une affectation de la stratégie), elle sera réévaluée auprès de vos ressources dans l’heure qui suit.

Pour plus d’informations sur les structures des définitions de stratégie, consultez [Structure des définitions de stratégie](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Affectation de rôle

Une affectation de stratégie est une définition de stratégie qui a été affectée avec une étendue spécifique. Cette étendue peut aller d’un [groupe d’administration](../management-groups/overview.md) à un groupe de ressources. Le terme *étendue* désigne l’ensemble des groupes de ressources, abonnements ou groupes d’administration auxquels la définition de stratégie est affectée. Toutes les ressources enfants héritent des affectations de stratégie. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle l’est à toutes les ressources de ce groupe de ressources. Toutefois, vous pouvez exclure une sous-étendue de l’affectation de stratégie.

Par exemple, dans l’étendue de l’abonnement, vous pouvez affecter une stratégie qui empêche la création de ressources réseau. Toutefois, vous excluez un groupe de ressources au sein de l’abonnement qui est destiné à l’infrastructure réseau. Vous accordez l’accès à ce groupe de ressources réseau aux utilisateurs auxquels vous faites confiance avec la création des ressources réseau.

Dans un autre exemple, vous souhaiterez peut-être affecter une stratégie de liste verte de type de ressource au niveau du groupe d’administration. Affectez ensuite une stratégie plus permissive (autorisant plus de types de ressources) à un groupe d’administration enfant ou même directement aux abonnements. Toutefois, cet exemple ne fonctionnera pas, car la stratégie est un système de refus explicite. Au lieu de cela, vous devez exclure le groupe d’administration enfant ou l’abonnement de l’attribution de stratégie au niveau du groupe d’administration. Affectez ensuite la stratégie plus permissive au niveau du groupe d’administration enfant ou de l’abonnement. Pour résumer, si une stratégie se traduit par le refus d’une ressource, alors la seule façon d’autoriser la ressource est de modifier la stratégie de refus.

Pour plus d’informations sur les définitions de stratégie et les affectations par le biais du portail, consultez [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure](assign-policy-portal.md). Les étapes pour [PowerShell](assign-policy-powershell.md) et [Azure CLI](assign-policy-azurecli.md) sont également disponibles.

## <a name="policy-parameters"></a>Paramètres de stratégie

Les paramètres de stratégie permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies que vous devez créer. Vous pouvez définir des paramètres lors de la création d’une définition de stratégie, pour la rendre plus générique. Vous pouvez ensuite réutiliser cette définition de stratégie pour différents scénarios. Pour ce faire, transmettez différentes valeurs lors de l’affectation de la définition de stratégie. Par exemple, spécifiez un ensemble d’emplacements pour un abonnement.

Les paramètres sont définis/créés lors de la création d’une définition de stratégie. Quand un paramètre est défini, un nom lui est affecté et éventuellement une valeur. Par exemple, vous pouvez définir un paramètre pour une stratégie intitulée *Emplacement*. Vous pouvez ensuite lui attribuer différentes valeurs comme *EastUS* ou *WestUS* lors de l’affectation d’une stratégie.

Pour plus d’informations sur les paramètres de stratégie, consultez [Vue d’ensemble des stratégies de ressources - Paramètres](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Définition d’initiative

Une définition d’initiative est une collection de définitions de stratégie qui sont spécialement conçues pour atteindre un objectif global particulier. Les définitions d’initiative simplifient la gestion et l’affectation des définitions de stratégie. Elles simplifient ces procédures en regroupant un ensemble de stratégies en un seul élément. Par exemple, vous pouvez créer une initiative intitulée **Activer la surveillance dans Azure Security Center**, avec comme objectif de surveiller toutes les recommandations de sécurité disponibles dans votre centre Azure Security Center.

Dans cette initiative, vous avez par exemple des définitions de stratégie comme celles-ci :

- **Surveiller les bases de données non chiffrées dans Security Center** : pour surveiller les bases de données et les serveurs SQL Server non chiffrés.
- **Surveiller les vulnérabilités du système d’exploitation dans Security Center** : pour surveiller les serveurs qui ne répondent pas à la base de référence configurée.
- **Surveiller l’absence d’Endpoint Protection dans Security Center** : pour surveiller les serveurs où un agent Endpoint Protection n’est pas installé.

## <a name="initiative-assignment"></a>Affectation d’initiative

Comme une affectation de stratégie, une affectation d’initiative est une définition d’initiative affectée à une étendue spécifique. Les affectations d’initiative réduisent la nécessité de créer plusieurs définitions d’initiative pour chaque étendue. Cette étendue peut également aller d’un groupe d’administration à un groupe de ressources.

Dans l’exemple précédent, l’initiative **Activer la surveillance dans Azure Security Center** peut être affectée à différentes étendues. Par exemple, une affectation peut être attribuée à **subscriptionA**.
Une autre peut être attribuée à **subscriptionB**.

## <a name="initiative-parameters"></a>Paramètres d’initiative

Comme les paramètres de stratégie, les paramètres d’initiative permettent de simplifier la gestion en réduisant la redondance. Les paramètres d’initiative sont essentiellement la liste des paramètres utilisés par les définitions de stratégie dans l’initiative.

Par exemple, imaginons un scénario où vous avez une définition d’initiative (**initiativeC**) avec les définitions de stratégie **policyA** et **policyB**, et chacune des définitions de stratégie attend un type de paramètre différent :

| Stratégie | Nom de paramètre |Type de paramètre  |Remarque |
|---|---|---|---|
| policyA | allowedLocations | array  |Ce paramètre attend une liste de chaînes pour une valeur, le type de paramètre ayant été défini comme tableau |
| policyB | allowedSingleLocation |chaîne |Ce paramètre attend un mot pour une valeur, le type de paramètre ayant été défini comme chaîne |

Dans ce scénario, quand vous définissez les paramètres d’initiative pour **initiativeC**, vous avec trois options :

- Utiliser les paramètres des définitions de stratégie dans cette initiative : dans cet exemple, *allowedLocations* et *allowedSingleLocation* deviennent des paramètres d’initiative pour **initiativeC**.
- Fournir des valeurs pour les paramètres des définitions de stratégie dans la définition de cette initiative. Dans cet exemple, vous pouvez fournir une liste d’emplacements au **paramètre de policyA, allowedLocations** et au **paramètre de policyB, allowedSingleLocation**. Vous pouvez également fournir des valeurs lors de l’affectation de cette initiative.
- Fournir une liste d’options de *valeurs* qui peuvent être utilisées lors de l’affectation de cette initiative. Lorsque vous affectez cette initiative, les paramètres hérités des définitions de stratégie dans l’initiative peuvent avoir seulement des valeurs provenant de cette liste fournie.

Par exemple, vous pouvez créer une liste d’options de valeur dans une définition d’initiative contenant les valeurs *EastUS*, *WestUS*, *CentralUS* et *WestEurope*. Si tel est le cas, vous ne pouvez pas entrer une valeur différente comme *Asie Sud-Est* pendant l’affectation d’initiative, car elle ne fait pas partie de la liste.

## <a name="maximum-count-of-policy-objects"></a>Nombre maximal d’objets de stratégie Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recommandations pour la gestion des stratégies

Voici quelques conseils que nous vous recommandons de suivre lors de la création et de la gestion des définitions et des affectations de stratégie :

- Si vous créez des définitions de stratégie dans votre environnement, nous recommandons de commencer par un effet d’audit, et non pas un effet de refus, de façon à conserver un suivi de l’impact de votre définition de stratégie sur les ressources de votre environnement. Si vous avez déjà des scripts en place pour mettre à l’échelle automatiquement vos applications, la définition d’un effet de refus peut entraver ces tâches d’automatisation que vous avez déjà en place.
- Il est important de garder à l’esprit les hiérarchies de l’organisation lors de la création de définitions et d’affectations. Nous recommandons de créer les définitions à un niveau plus élevé, par exemple au niveau du groupe d’administration ou de l’abonnement, et d’affecter au niveau enfant suivant. Par exemple, si vous créez une définition de stratégie au niveau du groupe d’administration, une affectation de stratégie de cette définition peut être limitée au niveau d’un abonnement dans ce groupe d’administration.
- Nous recommandons de toujours utiliser des définitions d’initiative au lieu de définitions de stratégie, même si vous n’avez qu’une seule stratégie à l’esprit. Par exemple, si vous avez une définition de stratégie *policyDefA* et que vous la créez sous la définition d’initiative *initiativeDefC*, si vous décidez de créer une autre définition de stratégie ultérieurement pour *policyDefB* avec des objectifs similaires à ceux de *policyDefA*, vous pouvez simplement l’ajouter sous *initiativeDefC* et mieux les suivre de cette façon.
- N’oubliez pas qu’une fois que vous avez créé une affectation d’initiative à partir d’une définition d’initiative, toutes les nouvelles définitions de stratégie ajoutées à la définition d’initiative sont automatiquement ajoutées sous la ou les affectations d’initiative sous cette définition d’initiative.
- Une fois qu’une affectation de l’initiative est déclenchée, toutes les stratégies dans l’initiative sont également déclenchées. Toutefois, si vous devez exécuter une stratégie individuellement, il est préférable de ne pas l’inclure dans une initiative.

## <a name="video-overview"></a>Présentation vidéo

La présentation suivante d’Azure Policy est à partir de la Build 2018. Pour le téléchargement des diapositives ou de la vidéo, accédez à [Govern your Azure environment through Azure Policy (Gouvernance de votre environnement Azure à l’aide d’Azure Policy)](https://channel9.msdn.com/events/Build/2018/THR2030) sur Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble d’Azure Policy et des autres concepts clés, voici les étapes suivantes que nous suggérons :

- [Affecter une définition de stratégie à l’aide du portail](assign-policy-portal.md)
- [Affecter une définition de stratégie avec Azure CLI](assign-policy-azurecli.md)
- [Affecter une définition de stratégie avec PowerShell](assign-policy-powershell.md)
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](..//management-groups/overview.md).
- Regarder [Govern your Azure environment through Azure Policy (Gouvernance de votre environnement Azure à l’aide d’Azure Policy)](https://channel9.msdn.com/events/Build/2018/THR2030) sur Channel 9