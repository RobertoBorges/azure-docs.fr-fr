---
title: Console série de machine virtuelle Azure | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: bccf53ed5554579f4ff0a864c38562b7b7f0d3ca
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885287"
---
# <a name="virtual-machine-serial-console"></a>Console série de machine virtuelle


La console série de machine virtuelle Azure permet aux machines virtuelles Linux d’accéder à une console texte. Cette connexion série est effectuée via le port série COM1 de la machine virtuelle. Elle fournit l’accès à la machine virtuelle et n’est pas liée au réseau de la machine virtuelle ni à l’état du système d’exploitation. Pour une machine virtuelle, l’accès à la console série n’est possible que via le portail Azure. De plus, seuls les utilisateurs disposant d’un rôle de Contributeur (ou supérieur) pour cette machine virtuelle sont autorisés à accéder à la console. 

Pour obtenir la documentation sur la console série pour les machines virtuelles Windows, [cliquez ici](../windows/serial-console.md).

> [!Note] 
> La console série pour les machines virtuelles est généralement disponible dans les régions Azure globales. À ce stade, la console série n’est encore disponible ni dans les clouds Azure Government, ni dans le clouds Azure en Chine.


## <a name="prerequisites"></a>Prérequis 

* Vous devez utiliser le modèle de déploiement de gestion des ressources. Les déploiements classiques ne sont pas pris en charge. 
* L’option [Diagnostics de démarrage](boot-diagnostics.md) DOIT être activée sur votre machine virtuelle (voir la capture d’écran ci-dessous).

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Le compte Azure qui utilise la console série doit disposer du [rôle Contributeur](../../role-based-access-control/built-in-roles.md) pour la machine virtuelle et pour le compte de stockage de [diagnostics de démarrage](boot-diagnostics.md). 
* La machine virtuelle pour laquelle vous accédez à la console doit également avoir un compte avec mot de passe. Vous pouvez en créer un avec la fonctionnalité [Réinitialiser le mot de passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de l’extension d’accès aux machines virtuelles (voir la capture d’écran ci-dessous).

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Pour découvrir les paramètres spécifiques aux distributions Linux, consultez la section [Disponibilité de la distribution de Linux pour la console série](#serial-console-linux-distro-availability)



## <a name="get-started-with-serial-console"></a>Bien démarrer avec la console série
Pour les machines virtuelles, la console série est accessible uniquement via le [portail Azure](https://portal.azure.com). Assurez-vous de remplir les[ conditions préalables](#prerequisites) ci-dessus. Voici les étapes permettant aux machines virtuelles d’accéder à la console série via le portail :

  1. Ouvrez le portail Azure
  1. (Ignorez cette étape si votre machine virtuelle a un utilisateur qui emploie l’authentification par mot de passe) Ajoutez un utilisateur avec une authentification par nom d’utilisateur/mot de passe en cliquant sur le panneau Réinitialiser le mot de passe
  1. Dans le menu de gauche, sélectionnez Machines virtuelles.
  1. Cliquez sur la machine virtuelle de votre choix dans la liste. La page de présentation de la machine virtuelle s’ouvre.
  1. Faites défiler jusqu’à la section Support + dépannage, puis cliquez sur l’option « Console série ». Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> La console série nécessite la configuration d’un utilisateur local avec un mot de passe. À ce stade, les machines virtuelles configurées uniquement avec une clé publique SSH ne peuvent pas se connecter à la console série. Pour créer un utilisateur local avec mot de passe, utilisez [Extension d’accès aux machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), disponible dans le portail en y cliquant sur Réinitialiser le mot de passe.
> Vous pouvez également réinitialiser le mot de passe administrateur de votre compte en [utilisant GRUB pour passer en mode mono-utilisateur](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distro-availability"></a>Disponibilité de distributions Linux pour la console série
Pour permettre le bon fonctionnement de la console série, le système d’exploitation invité doit être configuré pour la lecture et l’écriture des messages de console sur le port série. La plupart des [distributions Azure Linux approuvées](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) présentent la console série configurée par défaut. Un simple clic sur la section de la console série dans le portail Azure vous octroie l’accès à la console. 

Distribution      | Accès à la console série
:-----------|:---------------------
Red Hat Enterprise Linux    | L’accès à la console série est activé par défaut pour les images Red Hat Enterprise Linux disponibles sur Azure. 
CentOS      | Les images CentOS disponibles sur Azure disposent de l’accès à la console activé par défaut. 
Ubuntu      | Les images Ubuntu disponibles sur Azure disposent de l’accès à la console activé par défaut.
CoreOS      | Les images CoreOS disponibles sur Azure disposent de l’accès à la console activé par défaut.
SUSE        | Les images SLES les plus récentes disponibles sur Azure disposent de l’accès à la console activé par défaut. Si vous utilisez des versions antérieures (version 10 ou antérieure) de SLES sur Azure, suivez les instructions de [l’article de la base de connaissances](https://www.novell.com/support/kb/doc.php?id=3456486) pour activer la console série. 
Oracle Linux        | Les images Oracle Linux disponibles sur Azure disposent de l’accès à la console activé par défaut.
Images Linux personnalisées     | Pour activer la console série pour votre image de machine virtuelle Linux personnalisée, activez l’accès à la console dans `/etc/inittab` pour exécuter un terminal sur `ttyS0`. Voici un exemple d’ajout de cet élément dans le fichier inittab : `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Pour plus d’informations sur la création d’images personnalisées, consultez [Création et chargement d’un disque dur virtuel Linux dans Azure](https://aka.ms/createuploadvhd). Si vous générez un noyau personnalisé, vous devez envisager d’activer certains indicateurs de noyau comme `CONFIG_SERIAL_8250=y` et `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Le fichier de configuration se trouve souvent sous /boot/ à des fins d’exploration plus approfondie.

## <a name="common-scenarios-for-accessing-serial-console"></a>Scénarios courants pour l’accès à la console série 
Scénario          | Actions à effectuer dans la console série                
:------------------|:-----------------------------------------
Fichier FSTAB endommagé | Saisissez la clé `Enter` pour continuer et réparer le fichier fstab à l’aide d’un éditeur de texte. Vous devrez peut-être activer le mode mono-utilisateur pour cela. Consultez [Résoudre les incidents fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) et [Utilisation de la console série pour accéder à GRUB et au mode mono-utilisateur](serial-console-grub-single-user-mode.md) pour commencer.
Règles de pare-feu incorrectes | Accès à la console série et correction des tables d’adresses IP. 
Contrôle de la corruption du système de fichiers | Accès à la console série et récupération du système de fichiers. 
Problèmes de configuration SSH/RDP | Accès à la console série et modification des paramètres. 
Système de verrouillage du réseau| Accès à la console série via le portail de gestion du système. 
Interaction avec le chargeur de démarrage | Accès à GRUB via la console série. Accédez à [Utilisation de la console série pour accéder à GRUB et au mode mono-utilisateur](serial-console-grub-single-user-mode.md) pour commencer. 

## <a name="disable-serial-console"></a>Désactiver la console série
Par défaut, tous les abonnements ont accès à la console série pour toutes les machines virtuelles. Vous pouvez désactiver la console série au niveau de l’abonnement ou au niveau de la machine virtuelle.

> [!Note] 
> Pour activer ou désactiver la console série pour un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Cela inclut, mais sans s’y limiter, les rôles d’administrateur ou de propriétaire. Les rôles personnalisés peuvent également avoir des autorisations en écriture.

### <a name="subscription-level-disable"></a>Désactiver au niveau de l’abonnement
La console série peut être désactivée pour un abonnement entier par le biais de [l’appel à l’API REST Disable Console](https://aka.ms/disableserialconsoleapi). Vous pouvez utiliser la fonctionnalité « Essayez » disponible sur la page de documentation de l’API afin de désactiver et d’activer la console série pour un abonnement. Entrez votre `subscriptionId`, « valeur par défaut » dans le champ `default`, puis cliquez sur Exécuter. Les commandes Azure CLI seront disponibles à une date ultérieure. [Essayez l’appel à l’API REST ici](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Vous pouvez également utiliser le jeu de commandes ci-dessous dans Cloud Shell (commandes bash indiquées) pour désactiver, activer et afficher l’état de la console série pour un abonnement. 

* Pour obtenir l’état désactivé de la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Pour désactiver la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Pour activer la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Désactiver au niveau de la machine virtuelle
La console série peut être désactivée pour certaines machines virtuelles, en désactivant le paramètre de diagnostics de démarrage. Désactivez simplement les diagnostics de démarrage à partir du portail Azure et la console série sera désactivée pour la machine virtuelle.

## <a name="serial-console-security"></a>Sécurité de la console série 

### <a name="access-security"></a>Sécurité des accès 
L’accès à la console série est limité aux utilisateurs qui disposent du rôle [Contributeur](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) (ou supérieur) pour la machine virtuelle. Si votre locataire AAD nécessite l’authentification MFA, l’accès à la console série nécessite également l’authentification MFA, puisque son accès s’effectue via le [portail Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sécurité des canaux
Toutes les données envoyées sur les canaux sont chiffrées.

### <a name="audit-logs"></a>Journaux d’audit
Tous les accès à la console série sont journalisés dans les journaux [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la machine virtuelle. L’accès à ces journaux est détenu et contrôlé par l’administrateur de la machine virtuelle Azure.  

>[!CAUTION] 
Même si les mots de passe d’accès à la console ne sont pas journalisés, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ceux-ci seront écrits dans les journaux Diagnostics de démarrage de la machine virtuelle, avec tout autre texte visible, dans le cadre de l’implémentation de la fonctionnalité de scrollback de la console série. Ces journaux sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation de la console SSH pour tout élément pouvant impliquer des secrets et/ou des informations d’identification personnelle. 

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le deuxième utilisateur est connecté. Le premier utilisateur quitte la console pendant que le nouvel utilisateur s’installe, en quelque sorte.

>[!CAUTION] 
Cela signifie donc que l’utilisateur qui laisse sa place n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (via SIGHUP ou autre mécanisme similaire) est actuellement étudiée. Pour Windows, il existe un délai d’expiration automatique qui est activé dans la console SAC (Special Administrative Console). Toutefois, pour Linux, vous pouvez configurer un délai d’expiration terminal. Pour ce faire, il vous suffit d’ajouter `export TMOUT=600` à votre profil .bash_profile ou .profile pour l’utilisateur avec lequel vous vous connectez à la console, pour mettre fin à la session après un délai de 10 minutes.

## <a name="accessibility"></a>Accessibilité
L’accessibilité est un point central de la console série Azure. Nous avons donc fait en sorte que la console série soit accessible aux personnes présentant des déficiences visuelles ou auditives, ainsi qu’aux personnes qui ne peuvent pas utiliser une souris.

### <a name="keyboard-navigation"></a>Navigation au clavier
Utilisez la touche `tab` de votre clavier pour naviguer dans l’interface de la console série, à l’intérieur du portail Azure. Votre emplacement est mis en surbrillance à l’écran. Pour quitter le panneau de la console série, appuyez sur `Ctrl + F6` sur votre clavier.

### <a name="use-serial-console-with-a-screen-reader"></a>Utiliser la console série avec un lecteur d’écran
La console série comprend une prise en charge intégrée des lecteurs d’écran. Quand le lecteur d’écran est activé, le texte de remplacement du bouton sélectionné est lu à voix haute.

## <a name="errors"></a>Errors
La plupart des erreurs sont de nature temporaire et peuvent être corrigées par une nouvelle tentative de connexion à la console série. Le tableau ci-dessous présente une liste d’erreurs accompagnées de mesures de prévention.

Error                            |   Atténuation 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>'. To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle. 
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM serial console. Ensure you have at least VM Contributor role permissions (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer du rôle Contributeur pour accéder à la console sur cette machine virtuelle)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
WebSocket est fermé ou n’a pas pu être ouvert. | Vous devrez peut-être autoriser `*.console.azure.com`. Une approche plus détaillée, mais plus longue, consiste à autoriser les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), qui changent régulièrement.
Une réponse « Interdit » s’est produite lors de l’accès au compte de stockage des diagnostics de démarrage de cette machine virtuelle. | Assurez-vous que les diagnostics de démarrage n’ont pas un pare-feu de compte. Un compte de stockage des diagnostics de démarrage accessible est nécessaire au fonctionnement de la console série.

## <a name="known-issues"></a>Problèmes connus 
Nous sommes conscients de certains problèmes avec la console série. Voici une liste de ces problèmes et la procédure d’atténuation.

Problème                           |   Atténuation 
:---------------------------------|:--------------------------------------------|
L’invite de connexion ne s’affiche pas lorsque vous appuyez sur la touche Entrée après l’affichage de la bannière de connexion | Consultez la page suivante : [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Cela peut se produire si vous exécutez une machine virtuelle personnalisée, une appliance à sécurité renforcée ou une configuration de GRUB qui fait que Linux ne parvient pas à se connecter correctement au port série.
Le texte de la console série n’occupe l’écran que partiellement (souvent après l’utilisation d’un éditeur de texte) | Les consoles série ne gèrent pas la négociation sur la taille de fenêtre ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), ce qui signifie qu’aucun signal SIGWINCH ne sera envoyé pour mettre à jour la taille de l’écran et la machine virtuelle ne connaîtra pas la taille de votre terminal. Il est recommandé d’installer xterm ou un autre utilitaire similaire permettant de redimensionner l’affichage. L’exécution de la commande « resize » permet de résoudre ce problème.
Le collage de chaînes très longues ne fonctionne pas | La console série limite la longueur des chaînes collées dans le terminal à 2 048 caractères. Cela vise à empêcher une surcharge de la bande passante du port série.


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 
**Q. Comment puis-je envoyer des commentaires ?**

R. Pour nous contacter au sujet d’un problème, accédez à la page https://aka.ms/serialconsolefeedback (méthode conseillée). Vous pouvez également envoyer des commentaires via azserialhelp@microsoft.com ou dans la catégorie Machine virtuelle de la page http://feedback.azure.com

**Q. Est-ce que la console série prend en charge l’opération Copier/Coller ?**

R. Oui, c’est le cas. Utilisez Ctrl+Maj+C et Ctrl+Maj+V pour copier et coller du texte dans le terminal.

**Q. Puis-je utiliser une console série à la place d’une connexion SSH ?**

R. Même si cela est techniquement possible, la console série est principalement utilisée comme outil de résolution des problèmes, lorsque la connectivité via le protocole SSH n’est pas possible. Nous déconseillons l’utilisation de la console série en remplacement du SSH, et ce, pour deux raisons :

1. La console série ne dispose pas d’autant de bande passante que le protocole SSH, car c’est une connexion qui convient au texte uniquement. De fait, les interactions qui sollicitent beaucoup l’interface graphique utilisateur seront difficiles.
1. L’accès à la console série se fait uniquement à l’aide d’un nom d’utilisateur et d’un mot de passe. Les clés SSH sont beaucoup plus sécurisées que les combinaisons nom d’utilisateur/mot de passe. Pour des raisons de sécurité, il est donc recommandé d’utiliser le SSH plutôt que la console série.

**Q. Qui peut activer ou désactiver la console série pour mon abonnement ?**

R. Pour activer ou désactiver la console série au niveau d’un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Les rôles ayant une autorisation en écriture incluent, mais de façon non limitative, les rôles d’administrateur ou de propriétaire. Les rôles personnalisés peuvent également avoir des autorisations en écriture.

**Q. Qui peut accéder à la console série pour ma machine virtuelle ?**

R. Vous devez avoir un accès de niveau contributeur ou supérieur à une machine virtuelle afin d’accéder à sa console série. 

**Q. Ma console série n’affiche rien, que dois-je faire ?**

R. Votre image est probablement mal configurée pour l’accès à la console série. Voir [Disponibilité de la distribution de Linux pour la console série](#serial-console-linux-distro-availability) pour plus d’informations sur la configuration de votre image pour activer la console série.

**Q. La console série est-elle disponible pour Virtual Machine Scale Sets ?**

R. À ce stade, l’accès à la console série n’est pas pris en charge pour les instances de groupe de machines virtuelles identiques.

**Q. J’ai configuré ma machine virtuelle en utilisant uniquement une authentification par clé SSH, puis-je toujours utiliser la console série pour me connecter à ma machine virtuelle ?** R. Oui. Comme la console série ne nécessite pas de clés SSH, vous devez simplement configurer une combinaison nom d’utilisateur/mot de passe. Pour ce faire, utilisez le panneau Réinitialiser le mot de passe dans le portail et ces informations d’identification pour vous connecter à la console série.

## <a name="next-steps"></a>Étapes suivantes
* Utiliser la console série pour [démarrer dans GRUB et entrer en mode mono-utilisateur](serial-console-grub-single-user-mode.md)
* Utiliser la console série pour [les appels SysRq et NMI](serial-console-nmi-sysrq.md)
* La console série est également disponible pour les machines virtuelles [Windows](../windows/serial-console.md)
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md)