---
title: Ajouter un compte Microsoft (MSA) en tant que fournisseur d’identité en utilisant des stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: En vous aidant de cet exemple, découvrez comment utiliser Microsoft en tant que fournisseur d’identité à l’aide du protocole OpenID Connect (OIDC).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d53bbd10cbe822a91f201b7ba702b27b5ac0a6f9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887238"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un compte Microsoft à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs à partir d’un compte Microsoft à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Prérequis

- Suivez les étapes de l’article [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Si vous n’avez pas de compte Microsoft, créez-en un sur [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Ajouter une application

Pour utiliser un compte Microsoft en tant que fournisseur d’identité dans Azure AD B2C, vous devez ajouter une application de compte Microsoft.

1. Connectez-vous au [Portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) avec vos informations d'identification Microsoft.
2. En haut à droite, sélectionnez **Ajouter une application**.
3. Entrez un **nom d’application**, puis cliquez sur **Créer**. 
4. Sélectionnez **Générer un nouveau mot de passe** et veillez à copier le mot de passe à utiliser quand vous configurez le fournisseur d’identité. Copiez également l’**ID de l’application**. 
5. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URL de redirection**. Remplacez `your-tenant-name` par le nom de votre locataire.
6. Sélectionnez **Enregistrer**.

## <a name="create-a-policy-key"></a>Créer une clé de stratégie

Vous devez stocker le mot de passe que vous avez géré et précédemment enregistré dans votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Dans le coin supérieur gauche du Portail Azure, choisissez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité - PRÉVERSION**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Manual`.
7. Entrez le **nom** de la clé de stratégie. Par exemple : `MSASecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez le mot de passe que vous avez précédemment enregistré.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajouter un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Microsoft, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. 

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant l’élément **ClaimsProvider** au fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouvel élément **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4.  Remplacez la valeur de **client_id** par l’ID d’application que vous avez enregistré précédemment.
5.  Enregistrez le fichier .

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre compte Microsoft. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** de votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez l’option **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscrire le fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, vous créez un doublon d’un modèle de parcours utilisateur et le modifiez afin qu’il dispose également du fournisseur d’identité de compte Microsoft.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`, puis copiez l’intégralité de son contenu.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInMSA`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte Microsoft, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Dans le fichier *TrustFrameworkExtensions.xml*, recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez une valeur appropriée pour **TargetClaimsExchangeId**, par exemple `MicrosoftAccountExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. Ici, l’action est la communication d’Azure AD B2C avec un compte Twitter pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour **l’ID** la même valeur que celle utilisée pour **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur **l’ID** du profil technique que vous avez créé précédemment. Par exemple : `MSA-OIDC`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et chargez-le une nouvelle fois pour effectuer des vérifications.

## <a name="create-an-azure-ad-b2c-application"></a>Créer une application Azure AD B2C

La communication avec Azure AD B2C s’effectue via une application que vous créez dans votre locataire. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Dans le coin supérieur gauche du Portail Azure, choisissez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application, par exemple, *testapp1*.
6. Pour **Application/API web**, sélectionnez `Yes`, puis entrez `https://jwt.ms` pour **l’URL de réponse**.
7. Cliquez sur **Créer**.

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie du fichier *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInMSA.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInMSA`.
3. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Par exemple, `http://contoso.com/B2C_1A_signup_signin_msa`
4. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignInMSA).
5. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
6. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.
