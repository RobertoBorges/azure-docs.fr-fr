---
title: Configurer les alertes Azure Resource Health à l’aide de modèles Resource Manager | Microsoft Docs
description: Créez des alertes par programmation qui vous informent de l’indisponibilité de vos ressources Azure.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/4/2018
ms.author: shtabriz
ms.openlocfilehash: b19a840e5d0d3434d3e3ff91e0d7af037d64fb5a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975358"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Configurer les alertes Resource Health à l’aide de modèles Resource Manager

Cet article vous montre comment créer par programmation des alertes de journal d’activité Resource Health à l’aide de modèles Azure Resource Manager et d’Azure PowerShell.

Azure Resource Health vous tient informé de l’état d’intégrité actuel et précédent de vos ressources Azure. Les alertes Azure Resource Health peuvent vous signaler quasiment en temps réel tout changement de l’état d’intégrité des ressources. La création par programmation d’alertes Resource Health permet aux utilisateurs de créer et personnaliser leurs alertes en bloc.

## <a name="prerequisites"></a>Prérequis

Pour suivre les instructions de cette page, vous devez effectuer ces étapes préalables :

1. Installer le [module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (`AzureRm`)
2. [Créer ou réutiliser un groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) configuré pour la notification

## <a name="instructions"></a>Instructions
1. À l’aide de PowerShell, connectez-vous à Azure avec votre compte et sélectionnez l’abonnement à utiliser

        Login-AzureRmAccount
        Select-AzureRmSubscription -Subscription <subscriptionId>

    > La commande `Get-AzureRmSubscription` vous permet de répertorier les abonnements auxquels vous avez accès.

2. Recherchez l’ID Azure Resource Manager complet associé à votre groupe d’actions, et notez-le

        (Get-AzureRmActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Créez et enregistrez un modèle Resource Manager pour les alertes Resource Health au format `resourcehealthalert.json` ([voir les détails un peu plus loin](#resource-manager-template-for-resource-health-alerts))

4. Créez un déploiement Azure Resource Manager à l’aide de ce modèle

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Vous êtes invité à taper le nom de l’alerte ainsi que l’ID du groupe d’actions que vous avez noté précédemment :

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resouceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Si le processus s’est déroulé correctement, vous recevez une confirmation dans PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Notez que si vous voulez automatiser entièrement ce processus, modifiez simplement le modèle Resource Manager afin qu’il ne demande pas d’entrer les valeurs à l’étape 5.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Modèle Resource Manager pour les alertes Resource Health

Vous pouvez utiliser ce modèle simple comme base pour créer vos alertes Resource Health. Ce modèle fonctionne comme écrit dans cet extrait de code, à savoir qu’il génère des alertes pour tous les nouveaux événements d’intégrité de ressource qui se déclenchent sur l’ensemble des ressources dans un abonnement.

> À la fin de cet article, nous avons également inclus un modèle d’alerte plus complexe qui présente un rapport signal/bruit pour les alertes Resource Health plus élevé que ce modèle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Toutefois, une alerte avec une large étendue comme celle-ci n’est généralement pas recommandée. Voyons comment réduire l’étendue de cette alerte pour nous concentrer sur les événements qui nous intéressent ci-dessous.

### <a name="adjusting-the-alert-scope"></a>Paramétrage de l’étendue d’alerte

Les alertes Resource Health peuvent être configurées pour superviser les événements selon trois étendues différentes :

 * Niveau de l’abonnement
 * Niveau du groupe de ressources
 * Niveau de la ressource

Ce modèle d’alerte est configuré au niveau de l’abonnement, mais si vous préférez configurer l’alerte pour recevoir uniquement les notifications ayant trait à des ressources spécifiques, ou aux ressources d’un groupe de ressources donné, il vous suffit de modifier la section `scopes` dans l’exemple de modèle ci-dessus.

Pour une étendue au niveau d’un groupe de ressources, la section scopes doit se présenter de la façon suivante :
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Pour une étendue au niveau d’une ressource, la section scopes doit se présenter de la façon suivante :

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Par exemple : `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Pour obtenir cette chaîne, accédez au portail Azure et recherchez l’URL dans la page de votre ressource Azure.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Paramétrage des types de ressources qui vous alertent

Les alertes au niveau de l’abonnement ou du groupe de ressources peuvent provenir de différents types de ressources. Si vous souhaitez limiter les alertes afin qu’elles proviennent uniquement d’un sous-ensemble spécifique de types de ressources, vous pouvez le configurer dans la section `condition` du modèle de la manière suivante :

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Ici, nous utilisons le wrapper `anyOf` pour mettre l’alerte Resource Health en correspondance avec les conditions que nous avons spécifiées, afin d’activer les alertes ciblant des types de ressources spécifiques.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Paramétrage des événements Resource Health qui vous alertent
Quand des ressources rencontrent un événement d’intégrité, elles peuvent passer par une série de phases représentant l’état de l’événement d’intégrité : `Active`, `InProgress`, `Updated` et `Resolved`.

Si vous souhaitez recevoir une notification uniquement quand une ressource n’est plus intègre, configurez l’alerte pour être averti uniquement quand `status` a la valeur `Active`. Toutefois, si vous voulez également être informé aux autres phases, vous pouvez les ajouter comme ceci :

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Pour recevoir des notifications aux quatre phases des événements d’intégrité, supprimez la totalité de cette condition, et vous recevrez toujours l’alerte, quelle que soit la valeur de la propriété `status`.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Paramétrage des alertes Resource Health pour éviter les événements « Unknown »

Azure Resource Health peut vous informer de l’intégrité actuelle de vos ressources en les supervisant en continu à l’aide d’exécuteurs de série de tests. Les états d’intégrité signalés peuvent être « Available », « Unavailable » et « Degraded ». Toutefois, dans les cas où l’exécuteur de série de tests et la ressource Azure ne peuvent pas communiquer, un état d’intégrité « Unknown » est signalé pour la ressource, ce qui est considéré comme un événement d’intégrité « Active ».

Quand une ressource signale l’état « Unknown », la raison probable est que son état d’intégrité n’a pas changé depuis le dernier rapport généré. Si vous souhaitez supprimer les alertes sur des événements « Unknown », spécifiez cette logique dans le modèle :

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

Dans cet exemple, nous recevons une notification uniquement pour les événements dont l’état d’intégrité actuel et précédent n’est pas « Unknown ». Ce changement peut s’avérer utile si vos alertes sont envoyées directement vers votre téléphone mobile ou votre e-mail.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Paramétrage de l’alerte pour exclure les événements lancés par l’utilisateur

Les événements Resource Health peuvent être déclenchés par des événements lancés par la plateforme ou par l’utilisateur. Il est parfois préférable d’envoyer une notification uniquement quand l’événement d’intégrité est déclenché par la plateforme Azure.

La configuration de votre alerte pour filtrer ces types d’événements est simple :

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```

## <a name="recommended-resource-health-alert-template"></a>Modèle d’alerte Resource Health recommandé

En reprenant les différents paramétrages décrits dans la section précédente, nous pouvons créer un modèle d’alerte complet qui est configuré pour maximiser le rapport signal/bruit.

Voici le modèle que nous vous suggérons d’utiliser :
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Toutefois, vous savez mieux que quiconque quelles configurations sont les plus appropriées dans votre scénario. Alors, aidez-vous des outils présentés dans cette documentation pour réaliser votre propre personnalisation.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Resource Health :
-  [Vue d’ensemble d’Azure Resource Health](Resource-health-overview.md)
-  [Types de ressource et contrôles d’intégrité disponibles par le biais d’Azure Resource Health](resource-health-checks-resource-types.md)

Créer des alertes Service Health :
-  [Configurer des alertes pour Service Health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) 
