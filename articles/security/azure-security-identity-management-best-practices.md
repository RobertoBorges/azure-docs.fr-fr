---
title: 'Gestion des identités et des accès : bonnes pratiques de sécurité Azure | Microsoft Docs'
description: Cet article détaille un ensemble de meilleures pratiques en matière de gestion des identités et du contrôle d’accès à l’aide de fonctionnalités Azure intégrées.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: b1002d046014abd15452489e343ecf7c30b00d73
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311335"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Meilleures pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure

Beaucoup considèrent l’identité comme la nouvelle couche de sécurité assumant ce rôle du point classique du réseau. Les efforts en matière de gestion de la sécurité et les investissements ont changé de cible principale, car les périmètres réseau sont de plus en plus poreux et la défense du périmètre n’est plus aussi efficace qu’elle ne l’était avant l’explosion des appareils [BYOD](http://aka.ms/byodcg) et des applications cloud.

Dans cet article, nous étudions une collection de bonnes pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure. Ces meilleures pratiques sont issues de notre expérience avec [Azure AD](../active-directory/fundamentals/active-directory-whatis.md), mais également de celle des clients, comme vous.

Pour chaque bonne pratique, nous détaillons les éléments suivants :

* Nature de la bonne pratique
* Raison pour laquelle activer cette bonne pratique
* Conséquence possible en cas de non-utilisation de la meilleure pratique
* Alternatives possibles à la meilleure pratique
* Comment apprendre à utiliser la bonne pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plateforme Azure disponibles lors de l’écriture. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Dans cet article, nous allons étudier les points suivants :

* Traiter l’identité en tant que périmètre de sécurité principal
* La centralisation de la gestion des identités
* Activer l’authentification unique
* Activer l’accès conditionnel
* Activer la gestion des mots de passe
* Appliquer la vérification multifacteur pour les utilisateurs
* Utiliser le contrôle d’accès en fonction du rôle
* Exposition réduite des comptes privilégiés
* Contrôle des emplacements où se trouvent les ressources

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Traiter l’identité en tant que périmètre de sécurité principal

Beaucoup de gens considèrent l’identité comme le périmètre principal pour la sécurité. Il s’agit d’un changement par rapport à l’objectif traditionnel sur la sécurité réseau. Les périmètres de réseau continuent d’être plus poreux et la défense du périmètre ne peut plus être aussi efficace qu’avant l’explosion des appareils [BYOD](http://aka.ms/byodcg) et des applications cloud.
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) est la solution Azure pour la gestion des identités et des accès. Azure AD est un service multilocataire basé sur le cloud pour la gestion des identités et des annuaires par Microsoft. Il associe des services d’annuaires principaux, la gestion de l’accès aux applications et la protection des identités dans une même solution.

Les sections suivantes répertorient les meilleures pratiques pour la sécurité des identités et des accès à l’aide d’Azure AD.

## <a name="centralize-identity-management"></a>La centralisation de la gestion des identités

Dans un scénario [d’identité hybride](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?), nous vous recommandons d’intégrer vos répertoires cloud et locaux. L’intégration permet à votre équipe informatique de gérer des comptes depuis un emplacement unique, quel que soit l’endroit où un compte est créé. L’intégration améliore également la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales.


**Meilleure pratique** : intégrer vos répertoires locaux à Azure AD.  
**Détails** : utiliser [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) pour synchroniser votre annuaire local avec votre annuaire cloud.

**Meilleure pratique** : activer la synchronisation de hachage de mot de passe.  
**Détails** : la synchronisation de hachage de mot de passe est une fonctionnalité permettant de synchroniser les codes de hachage des mots de passe utilisateur entre une instance Active Directory locale et une instance Azure AD basée sur le cloud.

Même si vous décidez d’utiliser la fédération avec Active Directory Federation Services (AD FS) ou d’autres fournisseurs d’identité, vous pouvez éventuellement configurer la synchronisation de hachage de mot de passe en tant que sauvegarde au cas où vos serveurs locaux connaîtraient une défaillance ou deviendraient temporairement non disponibles. Cela permet aux utilisateurs de se connecter au service à l’aide du mot de passe qu’ils utilisent pour se connecter à leur instance Active Directory locale. Cela permet également à la protection d’identité de détecter les informations d’identification compromises en comparant ces codes de hachage de mot de passe avec des mots de passe connus pour être compromis, si un utilisateur a utilisé les mêmes adresse de messagerie et mot de passe sur d’autres services non connectés à Azure AD.

Pour plus d’informations, consultez [Implémenter la synchronisation de hachage du mot de passe avec la synchronisation Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

Les organisations qui n’intègrent pas leur identité locale avec leur identité cloud peuvent avoir une charge plus importante dans la gestion des comptes. Cette surcharge augmente la probabilité d’erreurs et de failles de sécurité.

## <a name="enable-single-sign-on"></a>Activer l’authentification unique

Dans un monde où mobilité et cloud occupent le premier plan, vous souhaitez activer l’authentification unique (SSO) sur tous les appareils, les applications et les services afin que vos utilisateurs soient être productifs où qu’ils soient et à tout moment. La gestion de plusieurs solutions d’identité pose un problème d’administration, non seulement pour l’informatique, mais également pour les utilisateurs qui auront à mémoriser plusieurs mots de passe.

En utilisant la même solution d’identité pour toutes vos applications et vos ressources, vous pouvez obtenir une authentification unique. Vos utilisateurs peuvent utiliser le même jeu d’informations d’identification pour s’authentifier et accéder aux ressources dont ils ont besoin, qu’elles soient situées en local ou dans le cloud.

**Meilleures pratiques** : activer l’authentification unique.  
**Détails** : Azure AD [étend vos versions locales d’Active Directory](../active-directory/connect/active-directory-aadconnect.md) sur le cloud. Les utilisateurs peuvent utiliser leur compte professionnel ou scolaire principal pour leurs appareils joints au domaine, les ressources de l’entreprise et toutes les applications web et SaaS dont ils ont besoin pour accomplir leur travail. Les utilisateurs n’ont plus besoin de gérer plusieurs combinaisons de nom d’utilisateur et mot de passe et l’accès aux applications peut être automatiquement mis en service (ou au contraire retiré) en fonction de leur appartenance aux groupes de l’entreprise et de leur statut en tant qu’employé. Vous pouvez en outre contrôler l’accès aux applications de la galerie ou aux applications en local que vous avez développées et publiées via le [proxy d’application Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

L’authentification unique permet aux utilisateurs d’accéder à leurs [applications SaaS](../active-directory/active-directory-appssoaccess-whatis.md) avec leur compte professionnel ou scolaire dans Azure AD. Ceci s’applique non seulement aux applications SaaS de Microsoft, mais également à d’autres applications, telles que [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) et [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Vous pouvez configurer votre application pour utiliser Azure AD comme fournisseur [d’identité SAML](../active-directory/fundamentals-identity.md). Pour contrôler la sécurité, Azure AD n’émet pas de jetons permettant aux utilisateurs de se connecter à l’application avant que l’accès n’ait été octroyé par Azure AD. Les utilisateurs peuvent accorder un accès direct ou via un groupe dont ils sont membres.

Les organisations qui ne créent pas une identité commune pour établir l’authentification unique pour leurs utilisateurs et les applications sont davantage exposées à des scénarios où les utilisateurs disposent de plusieurs mots de passe. Ces scénarios augmentent les chances qu’ils réutilisent des mots de passe ou qu’ils choisissent des mots de passe faibles.

## <a name="turn-on-conditional-access"></a>Activer l’accès conditionnel

Les utilisateurs peuvent accéder aux ressources de votre organisation en utilisant différents appareils et applications, n’importe où. En tant qu’administrateur, vous souhaitez vous assurer que ces appareils répondent à vos normes de sécurité et de conformité. Contrôler les personnes autorisées à accéder à une ressource ne suffit plus.

Afin d’équilibrer la sécurité et la productivité, vous devez aussi tenir compte des moyens d’accéder à une ressource avant de prendre une décision de contrôle d’accès. L’accès conditionnel Azure AD vous permet de satisfaire cette exigence. Avec l’accès conditionnel, vous pouvez prendre des décisions de contrôle d’accès automatisées pour accéder à vos applications cloud qui sont basées sur des conditions.

**Meilleure pratique** :gérer et contrôler l’accès aux ressources de l’entreprise.  
**Détails** : configurer un [accès conditionnel](../active-directory/active-directory-conditional-access-azure-portal.md) Azure AD en fonction du groupe, de l’emplacement et du niveau de confidentialité des applications SaaS et des applications connectées à Azure AD.

## <a name="enable-password-management"></a>Activer la gestion des mots de passe

Si vous avez plusieurs locataires ou si vous voulez permettre aux utilisateurs de [réinitialiser leurs mots de passe](../active-directory/active-directory-passwords-update-your-own-password.md), il est important d’utiliser des stratégies de sécurité appropriées afin d’éviter les abus.

**Meilleure pratique** : configurer la réinitialisation du mot de passe en libre service pour vos utilisateurs.  
**Détails** : utiliser la fonctionnalité de [réinitialisation du mot de passe en libre service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) d’Azure AD.

**Meilleure pratique**: surveiller l’utilisation de la réinitialisation du mot de passe en libre service.  
**Détails** : surveiller les utilisateurs inscrits en utilisant le [rapport d’activité d’inscription à la réinitialisation de mot de passe](../active-directory/active-directory-passwords-get-insights.md). La fonctionnalité de création de rapports fournie par Azure AD vous aide à répondre aux questions à l’aide de rapports prédéfinis. Si vous disposez d’une licence appropriée, vous pouvez également créer des requêtes personnalisées.

## <a name="enforce-multi-factor-verification-for-users"></a>Appliquer la vérification multifacteur pour les utilisateurs

Nous vous recommandons d’exiger une vérification en deux étapes pour tous vos utilisateurs. Cela inclut les administrateurs et les autres membres de votre organisation (par exemple, les responsables financiers) dont la compromission de leur compte pourrait avoir un impact significatif si leur compte est compromis.

Il existe plusieurs options pour exiger une vérification en deux étapes. La meilleure option pour vous dépend de vos objectifs, de l’édition d’Azure AD utilisée et de votre programme de licence. Consultez [Comment exiger la vérification en deux étapes pour un utilisateur](../active-directory/authentication/howto-mfa-userstates.md) pour déterminer la meilleure option pour vous. Consultez les pages de tarification [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) et [Authentification multifacteur Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) pour plus d’informations concernant les licences et la tarification.

Voici les options et les avantages de la vérification en deux étapes :

**Option 1** : [Activer l’authentification multifacteur en modifiant l’état de l’utilisateur](../active-directory/authentication/howto-mfa-userstates.md).   
**Avantage** : c’est la méthode traditionnelle pour exiger une vérification en deux étapes. Elle fonctionne avec [l’authentification multifacteur Azure dans le cloud et le serveur Multi-Factor Authentication Azure](../active-directory/authentication/concept-mfa-whichversion.md). Cette méthode nécessite que les utilisateurs effectuent la vérification en deux étapes chaque fois qu’ils se connectent, puis remplace les stratégies d’accès conditionnel.

**Option 2** : [Activer l’authentification multifacteur avec une stratégie d’accès conditionnel](../active-directory/authentication/howto-mfa-getstarted.md#enable-multi-factor-authentication-with-conditional-access).   
**Avantage**: cette option vous permet de demander une vérification en deux étapes sous certaines conditions à l’aide d’un [accès conditionnel](../active-directory/active-directory-conditional-access-azure-portal.md). Les conditions spécifiques peuvent être une connexion de l’utilisateur à partir d’emplacements différents, d’appareils non approuvés ou d’applications que vous considérez comme risquées. Le fait de définir des conditions spécifiques pour une vérification en deux étapes vous permet d’éviter de la demander continuellement à vos utilisateurs, ce qui peut être désagréable.

Il s’agit de la méthode la plus souple pour activer la vérification en deux étapes pour vos utilisateurs. Activer une stratégie d’accès conditionnel fonctionne uniquement pour l’authentification multifacteur Azure dans le cloud, et c’est une fonctionnalité payante d’Azure AD. Vous pouvez trouver plus d’informations sur cette méthode dans [Déployer une authentification multifacteur Azure basée sur le cloud](../active-directory/authentication/howto-mfa-getstarted.md).

**Option 3**: activer l’authentification multifacteur avec des stratégies d’accès conditionnel en évaluant les risques de l’utilisateur et de la connexion d’[Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Avantage**: cette option vous permet de :

- Détecter des vulnérabilités potentielles qui affectent les identités de votre organisation.
- Configurer des réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation.
- Examiner les incidents suspects et prendre les mesures appropriées pour les résoudre.

Cette méthode utilise l’évaluation de risques d’Azure AD Identity Protection pour déterminer si la vérification en deux étapes est exigée en se basant sur les risques de l’utilisateur et de la connexion pour toutes les applications cloud. Cette méthode requiert une licence Azure Active Directory P2. Vous trouverez plus d’informations sur cette méthode dans [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Option 1, l’activation de Multi-Factor Authentication en changeant l’état de l’utilisateur remplace les stratégies d’accès conditionnel. Étant donné que les options 2 et 3 utilisent des stratégies d’accès conditionnel, vous ne pouvez pas utiliser l’option 1 avec elles.

Les organisations qui n’ajoutent pas de couche supplémentaire de protection d’identité, comme la vérification en deux étapes, sont plus sensibles au vol d’informations d’identification. Le vol d’informations d’identification peut entraîner une compromission des données.

## <a name="use-role-based-access-control-rbac"></a>Utilisation du contrôle d’accès en fonction du rôle (RBAC)

Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations désireuses d’appliquer des stratégies de sécurité pour l’accès aux données. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) pour affecter des autorisations aux utilisateurs, groupes et applications à une certaine étendue. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement.

Vous pouvez utiliser des rôles [RBAC intégrés](../role-based-access-control/built-in-roles.md) dans Azure pour attribuer des privilèges aux utilisateurs. Les organisations qui n’appliquent aucun contrôle d’accès aux données en utilisant des fonctionnalités telles que RBAC risquent d’octroyer plus de privilèges que nécessaire à leurs utilisateurs. Le fait d’autoriser des utilisateurs à accéder à certains types de données (par exemple, des données HBI), auxquelles ils ne devraient pas avoir accès, peut conduire à la compromission de celles-ci.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposition réduite des comptes privilégiés

La sécurisation de l’accès privilégié est une première étape essentielle pour protéger les ressources d’entreprise. Limiter le nombre de personnes qui ont accès aux informations ou aux ressources sécurisées réduit le risque qu’un utilisateur malveillant accède à ces données ou qu’une ressource sensible soit accidentellement affectée par un utilisateur autorisé.

Les comptes privilégiés sont ceux qui administrent et gèrent des systèmes informatiques. Les pirates informatiques ciblent ces comptes pour accéder aux données et aux systèmes d’une organisation. Pour sécuriser l’accès privilégié, vous devez isoler les comptes et les systèmes contre les risques d’exposition à un utilisateur malveillant.

Nous vous recommandons de créer et de suivre une feuille de route pour sécuriser l’accès privilégié contre les cybercriminels. Pour plus d’informations sur la création d’un programme détaillé pour sécuriser les identités et les accès gérés ou signalés dans Azure AD, Microsoft Azure, Office 365 et d’autres services cloud, passez en revue l’article [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Les éléments suivants résument les meilleures pratiques indiquées dans [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md) :

**Meilleure pratique** : gérer, contrôler et surveiller l’accès à des comptes privilégiés.   
**Détails**: activer [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Après avoir activé Privileged Identity Management, vous recevez des notifications par courrier électronique de changements de rôles d’accès privilégié. Ces notifications vous informent lorsque des utilisateurs supplémentaires sont ajoutés aux rôles disposant de privilèges élevés dans votre annuaire.

**Meilleure pratique**: identifier et classer les comptes dans des rôles à privilèges élevés.   
**Détails** : Après avoir activé Azure AD Privileged Identity Management, vous voyez les utilisateurs ayant les rôles d’administrateur général, d’administrateur de rôle privilégié et d’autres rôles hautement privilégiés. Supprimez les comptes qui ne sont plus nécessaires dans ces rôles et classez les autres comptes qui sont affectés à des rôles d’administrateur :

- Affectés individuellement à des utilisateurs administratifs, et pouvant servir à des fins non administratives (par exemple, messagerie personnelle)
- Affectés individuellement à des utilisateurs administratifs et dédiés à des fins administratives uniquement
- Partagés entre plusieurs utilisateurs
- Pour les scénarios d’accès d’urgence
- Pour les scripts automatisés
- Pour les utilisateurs externes

**Meilleure pratique** : implémenter l’accès Juste à temps pour réduire le temps d’exposition des privilèges et augmenter votre visibilité sur l’utilisation des comptes privilégiés.   
**Détails** : grâce à Azure AD Privileged Identity Management, vous pouvez :

- Limiter les utilisateurs à l’utilisation de leur accès Juste à temps privilégiés.
- Attribuer des rôles pour une durée plus courte en sachant que les privilèges sont automatiquement révoqués.

**Meilleure pratique** : définir au moins deux comptes d’accès d’urgence.   
**Détails** : les comptes d’accès d’urgence aident les organisations à restreindre l’accès privilégié dans un environnement Azure Active Directory existant. Ces comptes sont hautement privilégiés et ne sont pas affectés à des individus spécifiques. Les comptes d’accès d’urgence sont limités aux scénarios où il est impossible d’utiliser des comptes d’administration normaux. Les organisations doivent limiter l’utilisation des comptes d’urgence au temps strictement nécessaire.

Évaluez les comptes qui sont affectés ou éligibles pour le rôle d’administrateur général. Si vous ne voyez pas de comptes cloud uniquement à l’aide du domaine `*.onmicrosoft.com` (conçu pour l’accès d’urgence), créez-les. Pour plus d’informations, consultez Gestion des comptes d’administration de l’accès d’urgence dans Azure AD.

**Meilleure pratique** : activer l’authentification multifacteur et inscrire tous les autres comptes administrateur non fédérés utilisateur unique à privilèges élevés.  
**Détails** : exiger l’authentification multifacteur Azure lors de la connexion de tous les utilisateurs individuels auxquels sont affectés un ou plusieurs rôles d’administrateur Azure AD : Administrateur général, Administrateur de rôle privilégié, Administrateur Exchange Online et Administrateur SharePoint Online. Suivez le guide pour activer [l’authentification multifacteur pour vos comptes Administrateur](../active-directory/authentication/howto-mfa-userstates.md) et vérifier que tous ces utilisateurs sont [inscrits](https://aka.ms/mfasetup).

**Meilleures pratiques** : prendre des mesures pour atténuer les techniques d’attaque les plus fréquemment utilisées.  
**Détails** : [Identifier les comptes Microsoft dans des rôles d’administrateur à basculer vers des comptes professionnels ou scolaires](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Vérifier les comptes d’utilisateur distincts et le transfert de messagerie pour les comptes administrateur général](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Vérifier que les mots de passe des comptes administrateur ont été récemment modifiés](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Activer la synchronisation de hachage de mot de passe](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Exiger l’authentification multifacteur pour les utilisateurs dans tous les rôles privilégiés, ainsi que pour les utilisateurs exposés](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obtenir votre Office 365 Secure Score (si vous utilisez Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Passer en revue l’aide Office 365 en matière de sécurité et de conformité (si vous utilisez Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurer la surveillance de l’activité Office 365 (si vous utilisez Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Définir des propriétaires de plan de réponse d’incident/d’urgence](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Sécuriser les comptes d’administration privilégiés locaux](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Si vous ne sécurisez l’accès privilégié, vous constaterez peut-être qu’un trop grand nombre d’utilisateurs disposent de rôles hautement privilégiés et sont plus vulnérables aux attaques. Les personnes malveillantes, comme les pirates informatiques, ciblent souvent des comptes administrateur et d’autres moyens d’accès privilégié pour accéder à des données sensibles et à des systèmes à l’aide de vols d’informations d’identification.

## <a name="control-locations-where-resources-are-created"></a>Contrôle des emplacements où les ressources sont créées

Le fait de permettre aux opérateurs de cloud d’effectuer des tâches tout en les empêchant de briser les conventions qui sont nécessaires à la gestion des ressources de votre organisation est très important. Les organisations qui veulent contrôler les emplacements où les ressources sont créées doivent coder en dur ces emplacements.

Vous pouvez utiliser [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour créer des stratégies de sécurité dotées de définitions décrivant les actions ou les ressources spécifiquement refusées. Vous affectez ces définitions de stratégies selon l’étendue souhaitée, au niveau de l’abonnement, du groupe de ressources ou d’une ressource individuelle.

> [!NOTE]
> Les stratégies de sécurité ne sont pas identiques au contrôle d’accès en fonction du rôle (RBAC). En fait, ils utilisent le RBAC pour autoriser des utilisateurs à créer ces ressources.
>
>

Les organisations qui ne contrôlent pas la création des ressources sont plus sensibles aux utilisateurs susceptibles d’abuser du service en créant plus de ressources que nécessaire. Le renforcement du processus de création des ressources est une étape importante de sécurisation d’un scénario à plusieurs locataires.

## <a name="actively-monitor-for-suspicious-activities"></a>Surveillance active des activités suspectes

Un système de surveillance d’identité actif peut détecter rapidement un comportement suspect et déclencher une alerte pour un examen approfondi. Le tableau suivant répertorie les deux fonctionnalités Azure AD pouvant aider les organisations à surveiller leurs identités :

**Meilleures pratiques** : avoir une méthode pour identifier :

- Les tentatives de connexion [sans être suivi](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- Les attaques par [force brute](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contre un compte particulier.
- Les tentatives de connexion à partir de plusieurs emplacements.
- Les connexions depuis des [appareils infectés](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Les adresses IP suspectes.

**Détails** : utiliser les [rapports d’anomalies](../active-directory/active-directory-view-access-usage-reports.md) d’Azure AD Premium. Disposer de processus et de procédures permettant aux administrateurs informatiques d’exécuter ces rapports de manière quotidienne ou à la demande (généralement dans un scénario de réponse aux incidents).

**Meilleure pratique** : disposer d’un système de surveillance actif qui vous avertit des risques et pouvant modifier le niveau de risque (élevé, moyen ou faible) à vos besoins métier.   
**Détail**: utiliser [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), qui marque les risques actuels sur son propre tableau de bord et envoie des notifications de synthèse quotidiennes par e-mail. Pour aider à protéger les identités de votre organisation, vous pouvez configurer des stratégies qui répondent automatiquement aux problèmes détectés lorsqu’un niveau de risque spécifié est atteint.

Les organisations qui ne surveillent pas activement leurs systèmes d’identité risquent de compromettre les informations d’identification des utilisateurs. Si elles n’ont pas connaissance des activités suspectes se déroulant avec ces informations d’identification, elles ne sont pas en mesure de limiter ce type de menace.

## <a name="next-step"></a>Étape suivante

Consultez l’article [Bonnes pratiques et tendances Azure relatives à la sécurité](security-best-practices-and-patterns.md) pour découvrir d’autres bonnes pratiques en matière de sécurité à appliquer dans le cadre de la conception, du déploiement et de la gestion de vos solutions cloud avec Azure.
