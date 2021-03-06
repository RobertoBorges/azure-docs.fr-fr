---
title: Journaux d’activité Azure Active Directory dans Azure Monitor (préversion) | Microsoft Docs
description: Présentation des journaux d’activité Azure Active Directory dans Azure Monitor (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3a4fc814a40bf370a137a2045c6218d3ee4b8778
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395482"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Journaux d’activité Azure Active Directory dans Azure Monitor (préversion)

À l’aide d’Azure Monitor, vous pouvez maintenant acheminer les journaux d’activité Azure AD vers votre propre compte de stockage ou Event Hub. Avec la préversion publique des journaux Azure Active Directory disponible dans Azure Monitor, vous pouvez :

* Archiver vos journaux d’audit d’un compte de stockage Azure, ce qui vous permet de conserver les données longtemps.
* Diffuser vos journaux d’audit vers un Event Hub Azure pour analyser des données à l’aide des outils populaires de Security Information and Event Management (SIEM), comme Splunk et QRadar.
* Intégrer vos journaux d’audit à vos propres solutions de journaux personnalisés en les diffusant en continu vers un Event Hub.
* Envoyer vos journaux d’activité Azure AD à Log Analytics pour permettre les visualisations enrichies, la supervision et les alertes sur les données connectées.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Rapports pris en charge

Vous pouvez acheminer les journaux d’activité d’audit et authentifier des journaux d’activité vers votre compte de stockage Azure, un Event Hub ou une solution personnalisée à l’aide de cette fonctionnalité. 

* **Journaux d’audit** : les [rapports d’activité de journaux d’audit](concept-audit-logs.md) vous permettent d’accéder à l’historique de toutes les tâches effectuées dans votre locataire.
* **Journaux d’identification** : le [rapport d’activité de connexions](concept-sign-ins.md) vous permet d’identifier qui a effectué les tâches signalées par les journaux d’audit.

> [!NOTE]
> Les journaux d’activité d’audit et de connexions associés à B2C ne sont pas pris en charge pour le moment.
>

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Une [licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 ou Premium 2 permet d’accéder aux journaux Azure AD dans le portail Azure. 
* Un locataire Azure AD.
* Utilisateur considéré comme **administrateur général** ou **administrateur de la sécurité** pour un locataire Azure AD.
* Une [licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Premium 1 ou Premium 2 permet d’accéder aux journaux de connexion Azure AD dans le portail Azure. 

Selon l’endroit où vous souhaitez acheminer les données du journal d’audit, vous devez disposer de l’un des éléments suivants :

* Un compte de stockage Azure doté des autorisations *ListKeys* activées. Nous vous recommandons d’utiliser un compte de stockage général et pas un compte de stockage blob. Pour plus d’informations sur la tarification du stockage, utilisez la [Calculatrice de prix pour le stockage Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Un espace de noms Azure Event Hubs pour intégrer avec des solutions tierces.
* Un espace de travail Azure Log Analytics pour l’envoi de journaux à Log Analytics.

## <a name="cost-considerations"></a>Considérations relatives au coût

Si vous avez déjà une licence Azure AD, vous avez besoin d’un abonnement Azure pour configurer le compte de stockage et l’Event Hub. L’abonnement Azure est fourni gratuitement, mais vous devez payer pour utiliser des ressources Azure, y compris le compte de stockage que vous utilisez pour l’archivage, et l’Event Hub que vous utilisez pour la diffusion en continu. La quantité de données, et donc le coût, varient considérablement selon la taille du locataire. 

### <a name="storage-size-for-activity-logs"></a>Taille de stockage des journaux d’activité

Chaque événement du journal d’audit utilise environ 2 Ko de stockage de données. Par conséquent, pour un locataire possédant 100 000 utilisateurs, et qui impliquerait environ 1,5 million d’événements par jour, vous auriez besoin d’environ 3 Go de stockage de données par jour. Dans la mesure où les écritures sont réalisées par lots de cinq minutes environ, vous pouvez anticiper environ 9000 opérations d’écriture par mois. 

Le tableau suivant contient une estimation des coûts, selon la taille du locataire. Ces coûts sont calculés pour un compte de stockage à usage général v2 dans la région USA Ouest, pendant au moins un an de rétention. Utilisez la [Calculatrice de prix de stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) pour obtenir une estimation plus précise du volume de données que vous prévoyez pour votre application. 

| Catégorie de journal | Nombre d’utilisateurs | Événements par jour | Volume de données par mois (estimation) | Coût par mois (estimation) | Coût par an (estimation) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100 000 | 1,5&nbsp;million | 90 Go | 1,93 $ | 23,12 $ |
| Audit | 1 000 | 15 000 | 900 Mo | 0,02 $ | 0,24 $ |
| Connexions | 1 000 | 34800 | 4 Go | 0,13 $ | 1,56 $ |
| Connexions | 100 000 | 15&nbsp;millions | 1,7 To | 35,41 $ | 424,92 $ | 


### <a name="event-hub-messages-for-activity-logs"></a>Messages Event Hub pour les journaux d’activité

Les événements sont regroupés dans des intervalles d’environ cinq minutes et sont envoyés sous forme de message unique contenant tous les événements apparus au cours de cette période de temps. La taille maximale d’un message dans le Event Hub est de 256 Ko. Si la taille de tous les messages pendant cette période de temps dépasse ce volume, plusieurs messages sont envoyés. 

Par exemple, on compte normalement environ 18 événements par seconde pour un locataire important de plus de 100 000 utilisateurs, un taux qui équivaut à 5 400 événements toutes les cinq minutes. Étant donné que les journaux d’audit font environ 2 Ko par événement, cela équivaut à 10.8 Mo de données. Par conséquent, 43 sont envoyés à l’Event Hub dans cet intervalle de cinq minutes. 

Le tableau suivant contient les coûts approximatifs pour un Event Hub de base dans l’ouest des États-Unis, en fonction du volume de données d’événement. Utilisez la [Calculatrice de prix Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/) pour obtenir une estimation plus précise du volume de données que vous prévoyez pour votre application.

| Catégorie de journal | Nombre d’utilisateurs | Événements par seconde | Événements par intervalle de cinq minutes | Volume par intervalle | Messages par intervalle | Messages par mois | Coût par mois (estimation) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100 000 | 18 | 5400 | 10,8 Mo | 43 | 371,520 | 10,83 $ |
| Audit | 1 000 | 0.1 | 52 | 104 Ko | 1 | 8640 | 10,80 $ |
| Connexions | 1 000 | 178 | 53400 | 106,8&nbsp;MO | 418 | 3 611 520 | 11,06 $ |  

### <a name="log-analytics-cost-considerations"></a>Considérations relatives aux coûts dans Log Analytics

Pour connaître les coûts liés à la gestion des données dans l’espace de travail Log Analytics, consultez [Gérer les coûts en contrôlant le volume et la conservation des données dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Cette section répond aux questions fréquemment posées et traitent des problèmes rencontrés avec les journaux Azure Active Directory dans Azure Monitor.

**Q : Par quoi dois-je commencer ?** 

**R** : Cet article décrit les fonctionnalités que vous devez déployer. Une fois que vous avez rempli les conditions préalables, accédez aux didacticiels qui peuvent aider à configurer et à acheminer vos journaux vers un Event Hub.

---

**Q : Quels sont les journaux inclus ?**

**R** : Les journaux d’audit et d’activité de connexion sont disponibles pour le routage via cette fonctionnalité. Néanmoins, les événements d’audit associés à B2C ne sont pas inclus à l’heure actuelle. Consultez le [Schéma des journaux d’audit](reference-azure-monitor-audit-log-schema.md) et le [Schéma des journaux de connexion](reference-azure-monitor-sign-ins-log-schema.md) pour savoir quels types de journaux et quelles fonctionnalités de ces journaux sont actuellement pris en charge. 

---

**Q : Combien de temps après une action les journaux correspondants s’affichent-ils dans Event Hubs ?**

**R** : les journaux apparaissent normalement dans votre Event Hub dans les deux à cinq minutes après que l’action soit effectuée. Pour plus d’informations sur Event Hubs, consultez la section [Qu’est-ce qu’Event Hubs ?](../../event-hubs/event-hubs-about.md).

---

**Q : Combien de temps après une action les journaux correspondants s’affichent-ils dans les comptes de stockage ?**

**R** : Pour les comptes de stockage Azure, la latence est d’environ 5 à 15 minutes après l’exécution de l’action.

---

**Q : Combien me coûtera le stockage de mes données ?**

**R** : Les coûts du stockage dépendent de la taille de vos journaux, ainsi que de la période de rétention que vous choisissez. Pour obtenir la liste de l’estimation des coûts pour les clients, qui dépendent du volume de journaux générés, consultez la section [Taille de stockage des journaux d’activité](#storage-size-for-activity-logs).

---

**Q : Quel est le coût de la diffusion en continu de mes données vers un Event Hubs ?**

**R** : Le coût de la diffusion en continu dépend du nombre de messages reçus par minute. Cet article explique comment sont calculés les coûts et répertorie les estimations de coûts, qui sont basées sur le nombre de messages. 

---

**Q : Comment puis-je intégrer les journaux d’activité Azure AD à mon système SIEM ?**

**R** : Il existe deux méthodes pour le faire :

- Utilisez Azure Monitor avec Event Hubs pour diffuser les journaux dans votre système SIEM. Tout d’abord, [diffusez les journaux vers un Event Hub](tutorial-azure-monitor-stream-logs-to-event-hub.md), puis [configurez votre outil SIEM](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) auprès du Event Hub configuré. 

- Utilisez l’[API Graph de création de rapports](concept-reporting-api.md) pour accéder aux données, puis les intégrer à votre système SIEM avec vos propres scripts.

---

**Q : Quels sont les outils SIEM actuellement pris en charge ?** 

**R** : Pour le moment, Azure Monitor est pris en charge par [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar et [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Pour en savoir plus sur le fonctionnement des connecteurs, consultez la section [Diffuser des données de surveillance Azure vers un Event Hub pour les utiliser dans un outil externe](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

---

**Q : Comment puis-je intégrer les journaux d’activité Azure AD à mon instance Splunk ?**

**R** : Tout d’abord, [acheminez les journaux d’activité Azure AD vers un Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md), puis suivez les étapes pour [intégrer des journaux d’activité avec Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**Q : Comment puis-je intégrer les journaux d’activité Azure AD avec Sumo Logic ?** 

**R** : Tout d’abord, [acheminez les journaux d’activité Azure AD à un Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), puis suivez les étapes pour [installer l’application Azure AD et afficher les tableaux de bord dans SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**Q : Puis-je accéder aux données d’un Event Hub sans avoir recours à un outil SIEM externe ?** 

**R** : Oui. Vous pouvez utiliser l’[API Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) pour accéder aux journaux à partir de votre application personnalisée. 

---


## <a name="next-steps"></a>Étapes suivantes

* [Archiver les journaux d’activité dans un compte de stockage](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Acheminer les journaux d’activité vers un Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Intégrer les journaux d’activité à Log Analytics](howto-integrate-activity-logs-with-log-analytics.md)
