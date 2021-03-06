---
title: Sources de données d’analyse dans Azure
description: En savoir plus sur toutes les sources de données d’analyse disponibles sur Azure dès aujourd'hui.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 55dc43f238b487392be48047b3d608f84e1e3ee2
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579308"
---
# <a name="consume-monitoring-data-from-azure"></a>Consommer des données d’analyse à partir d’Azure

Sur la plateforme Azure, nous rassemblons les données d’analyse dans un emplacement unique avec le pipeline Azure Monitor, mais sachez qu’aujourd'hui les données d’analyse ne sont pas encore toutes disponibles dans ce pipeline. Dans cet article, nous résumons les différentes façons dont vous pouvez accéder par programme aux données d’analyse à partir des services Azure.

## <a name="options-for-data-consumption"></a>Options pour la consommation de données

| Type de données | Catégorie | Services pris en charge | Méthodes d’accès |
| --- | --- | --- | --- |
| Mesures au niveau de la plateforme Azure Monitor | Mesures | [Consulter la liste ici](monitoring-supported-metrics.md) | <ul><li>**API REST :**[API métrique Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Stockage d’objets blob ou concentrateur d’événements :**[Paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Mesures de SE invité Compute (ex. compteurs de performances) | Mesures | Machines virtuelles [Windows](../virtual-machines-dotnet-diagnostics.md) et Linux (v2), [Services cloud](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Table ou blob de stockage :**[diagnostics Azure Windows ou Linux](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Concentrateur d’événements :**[Diagnostics Windows Azure](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Mesures personnalisées ou d’application | Mesures | Toute application instrumentée avec Application Insights | <ul><li>**API REST :**[API REST d’Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Métriques de stockage | Mesures | Stockage Azure | <ul><li>**Table de stockage :**[Analyse du stockage](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Données de facturation | Mesures | Tous les services Azure | <ul><li>**API REST** [API Azure Resource Usage et RateCard](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Journal d’activité | Événements | Tous les services Azure | <ul><li>**API REST :**[API Événements Azure Monitor](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Stockage d’objets blob ou concentrateur d’événements :**[profil du journal](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Journaux de diagnostics Azure Monitor | Événements | [Consulter la liste ici](monitoring-diagnostic-logs-schema.md) | <ul><li>**Stockage d’objets blob ou concentrateur d’événements :**[Paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Journaux de SE invité Compute (ex. IIS, ETW, syslogs) | Événements | Machines virtuelles [Windows](../virtual-machines-dotnet-diagnostics.md) et Linux (v2), [Services cloud](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Table ou blob de stockage :**[diagnostics Azure Windows ou Linux](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Concentrateur d’événements :**[Diagnostics Windows Azure](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Journaux App Service | Événements | App Services | <ul><li>**Stockage d’objets blob, table ou fichier :**[Diagnostics Web App](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Journaux de stockage | Événements | Stockage Azure | <ul><li>**Table de stockage :**[Analyse du stockage](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertes Security Center | Événements | Azure Security Center | <ul><li>**API REST :**[Alertes de sécurité](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Création de rapports Active Directory | Événements | Azure Active Directory | <ul><li>**API REST :**  [API Graph Azure Active Directory](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| État des ressources de Security Center | Statut | [Toutes les ressources prises en charge](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API REST :**[États de sécurité](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Intégrité des ressources | Statut | Services pris en charge | <ul><li>**API REST :**[API REST Resource Health](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alertes de métrique Azure Monitor | Notifications | [Consulter la liste ici](monitoring-supported-metrics.md) | <ul><li>**Webhook:**[Alertes de métrique Azure](insights-webhooks-alerts.md)</li></ul> |
| Alertes Journal d’activité Azure Monitor | Notifications | Tous les services Azure | <ul><li>**Webhook :** Alertes Journal d’activité Azure</li></ul> |
| Notifications de mise à l’échelle automatique | Notifications | [Consulter la liste ici](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook :**[Schéma de la charge utile du webhook de notification de mise à l’échelle automatique](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Alertes Requête de recherche dans les journaux | Notifications | Log Analytics | <ul><li>**Webhook:** [Action webhook pour les règles d’alerte de journal](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)</li></ul> |
| Alertes de métrique Application Insights | Notifications | Application Insights | <ul><li>**Webhook :**[Alertes Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Tests web Application Insights | Notifications | Application Insights | <ul><li>**Webhook :**[Alertes Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [mesures Azure Monitor](monitoring-overview-metrics.md)
- En savoir plus sur le [journal d’activité Azure](monitoring-overview-activity-logs.md)
- En savoir plus sur les [journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)
