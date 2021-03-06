---
title: Erreur Aucun abonnement trouvé lorsque vous tentez de vous connecter au portail Azure ou au centre des comptes Azure | Documents Microsoft
description: Fournit la solution pour un problème dans lequel le message d’erreur Aucun abonnement trouvé s’affiche lorsque vous vous connectez au portail Azure ou au centre des comptes Azure.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: a1e90f946508f1ffc0a1ee812dde46ee733d715a
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392432"
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Erreur Aucun abonnement trouvé dans le portail Azure ou le centre des comptes Azure

Vous pouvez recevoir un message d’erreur « Aucun abonnement trouvé » quand vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) ou au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions). Cet article fournit une solution à ce problème.

## <a name="symptom"></a>Symptôme

Lorsque vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) ou au [centre des comptes Azure](https://account.windowsazure.com/Subscriptions), vous recevez le message d’erreur suivant : « Aucun abonnement trouvé ».

## <a name="cause"></a>Cause :

Ce problème se produit si vous avez sélectionné le mauvais annuaire ou si votre compte ne dispose pas des autorisations suffisantes. 

## <a name="solution"></a>Solution

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scénario 1 : le message d’erreur est reçu dans le [portail Azure](https://portal.azure.com)

Pour résoudre ce problème :

* Vérifiez que le répertoire Azure correct soit sélectionné en cliquant sur votre compte en haut à droite.

  ![Sélectionnez l’annuaire en haut à droite du portail Azure.](./media/billing-no-subscriptions-found/directory-switch.png)
* Si le répertoire Azure correct est sélectionné, mais que vous recevez néanmoins le message d’erreur, [affectez le rôle Propriétaire à votre compte](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scénario 2 : le message d’erreur est reçu dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions)

Vérifiez que le compte utilisé correspond à l’administrateur du compte. Pour vérifier qui est l’administrateur du compte, procédez comme suit :

1. Connectez-vous à la [vue Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .  

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) pour obtenir une prise en charge rapide de votre problème. 
