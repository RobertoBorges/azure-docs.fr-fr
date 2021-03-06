---
title: 'Didacticiel : Intégration d’Azure Active Directory à MyWorkDrive | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: jeedes
ms.openlocfilehash: 2103c5c8c08a6aebfc1168c8fbdb4181dbe3a997
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046077"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Didacticiel : Intégration d’Azure Active Directory à MyWorkDrive

Dans ce didacticiel, vous allez apprendre à intégrer MyWorkDrive à Azure Active Directory (Azure AD).

L’intégration de MyWorkDrive à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à MyWorkDrive.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à MyWorkDrive (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à MyWorkDrive, vous devez disposer des éléments suivants :

- Un abonnement Azure AD
- Un abonnement MyWorkDrive pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MyWorkDrive à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-myworkdrive-from-the-gallery"></a>Ajout de MyWorkDrive à partir de la galerie
Pour configurer l’intégration de MyWorkDrive à Azure AD, vous devez ajouter MyWorkDrive à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter MyWorkDrive à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **MyWorkDrive**, sélectionnez **MyWorkDrive** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![MyWorkDrive dans la liste des résultats](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec MyWorkDrive grâce à un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur MyWorkDrive correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur MyWorkDrive associé.

Pour configurer et tester l’authentification unique Azure AD avec MyWorkDrive, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test MyWorkDrive](#create-a-myworkdrive-test-user)** pour avoir un équivalent de Britta Simon dans MyWorkDrive lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application MyWorkDrive.

**Pour configurer l’authentification unique Azure AD avec MyWorkDrive, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **MyWorkDrive**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/myworkdrive-tutorial/tutorial_myworkdrive_samlbase.png)

3. Dans la section **Domaine et URL MyWorkDrive**, suivez la procédure ci-après si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité** :

    ![Informations d’authentification unique dans Domaine et URL MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SERVER.DOMAIN.COM>/Account/Login-saml`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support technique de MyWorkDrive](mailto:support@myworkdrive.com). 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/myworkdrive-tutorial/tutorial_myworkdrive_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/myworkdrive-tutorial/tutorial_general_400.png)
    
7. Dans la section **Configuration de MyWorkDrive**, cliquez sur **Configurer MyWorkDrive** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_configure.png) 

8. Pour configurer l’authentification unique côté **MyWorkDrive**, vous devez envoyer le **Certificat (Base64)** téléchargé, **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à [l’équipe du support technique de MyWorkDrive](mailto:support@myworkdrive.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/myworkdrive-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/myworkdrive-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/myworkdrive-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/myworkdrive-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-myworkdrive-test-user"></a>Créer un utilisateur de test MyWorkDrive

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans MyWorkDrive. Collaborez avec [l’équipe du support technique de MyWorkDrive](mailto:support@myworkdrive.com) pour ajouter des utilisateurs dans la plateforme MyWorkDrive. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MyWorkDrive.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à MyWorkDrive, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **MyWorkDrive**.

    ![Lien MyWorkDrive dans la liste des applications](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette MyWorkDrive dans le volet d’accès, vous devez être connecté automatiquement à votre application MyWorkDrive.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/myworkdrive-tutorial/tutorial_general_01.png
[2]: ./media/myworkdrive-tutorial/tutorial_general_02.png
[3]: ./media/myworkdrive-tutorial/tutorial_general_03.png
[4]: ./media/myworkdrive-tutorial/tutorial_general_04.png

[100]: ./media/myworkdrive-tutorial/tutorial_general_100.png

[200]: ./media/myworkdrive-tutorial/tutorial_general_200.png
[201]: ./media/myworkdrive-tutorial/tutorial_general_201.png
[202]: ./media/myworkdrive-tutorial/tutorial_general_202.png
[203]: ./media/myworkdrive-tutorial/tutorial_general_203.png

