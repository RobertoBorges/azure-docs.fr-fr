---
title: Matrice de support Azure Site Recovery pour la réplication Azure vers Azure | Microsoft Docs
description: Fournit un récapitulatif des systèmes d’exploitation et des configurations pris en charge pour la réplication de machines virtuelles Azure Site Recovery d’une région à une autre à des fins de récupération d’urgence.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: sujayt
ms.openlocfilehash: 49773e076ed8bb06ff76f9f654b914a709051fb5
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378616"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Matrice de support pour la réplication à partir d’une région Azure vers une autre



Cet article récapitule les composants et les configurations pris en charge lors de la réplication et de la récupération de machines virtuelles Azure d’une région vers une autre avec le service [Azure Site Recovery](site-recovery-overview.md).

## <a name="user-interface-options"></a>Options d’interface utilisateur

**Interface utilisateur** |  **Prise en charge / Non prise en charge**
--- | ---
**Portail Azure** | Pris en charge
**PowerShell** | [Réplication d’Azure vers Azure avec PowerShell](azure-to-azure-powershell.md)
**API REST** | Non prise en charge pour le moment
**INTERFACE DE LIGNE DE COMMANDE** | Non prise en charge pour le moment


## <a name="resource-support"></a>Prise en charge des ressources

**Type de déplacement de ressources** | **Détails**
--- | --- | ---
**Déplacer le coffre entre plusieurs groupes de ressources** | Non pris en charge<br/><br/> Vous ne pouvez pas déplacer un coffre Recovery Services d’un groupe de ressources à un autre.
**Déplacer le calcul/le stockage/les ressources réseau entre plusieurs groupes de ressources** | Non pris en charge.<br/><br/> Si vous déplacez une machine virtuelle ou des composants associés tels que le stockage/réseau après sa réplication, vous devez désactiver et réactiver la réplication pour la machine virtuelle.
**Répliquer des machines virtuelles Azure d’un abonnement à un autre pour la reprise d’activité** | Pris en charge à l’intérieur du même locataire Azure Active Directory. Non pris en charge pour les machines virtuelles classiques.
**Migrer des machines virtuelles entre des régions dans les clusters géographiques pris en charge (dans un même abonnement et entre plusieurs abonnements)** | Pris en charge à l’intérieur du même locataire Azure Active Directory pour les machines virtuelles « Modèle de déploiement Resource Manager ». Non pris en charge pour les machines virtuelles « Modèle de déploiement classique ».
**Migrer des machines virtuelles au sein de la même région** | Non pris en charge.


## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée

La prise en charge ci-dessous est applicable à toutes les charges de travail en cours d’exécution sur le système d’exploitation mentionné.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, Server avec Expérience utilisateur)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 avec au moins SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server n’est pas pris en charge.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5
- Serveur LTS Ubuntu 14.04 [ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Serveur LTS Ubuntu 16.04 [ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (versions du noyau prises en charge)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (versions du noyau prises en charge)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (versions du noyau prises en charge)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Linux 6.4, 6.5, 6.6, 6.7 exécutant le noyau compatible Red Hat ou le noyau Unbreakable Enterprise Kernel Release 3 (UEK3)

(La mise à niveau des machines de réplication de SLES 11 SP3 vers SLES 11 SP4 n’est pas prise en charge. Si une machine répliquée a été mise à niveau de SLES 11SP3 vers SLES 11 SP4, vous devez désactiver la réplication et protéger à nouveau la machine après la mise à niveau.)

>[!NOTE]
>
> Sur les serveurs Ubuntu utilisant l’authentification et la connexion basées sur un mot de passe, et utilisant le package cloud-init pour configurer des machines virtuelles cloud, la connexion basée sur un mot de passe peut être désactivée lors du basculement (en fonction de la configuration de cloudinit.) La connexion basée sur un mot de passe peut être réactivée sur la machine virtuelle en réinitialisant le mot de passe dans le menu des paramètres (dans la section SUPPORT + TROUBLESHOOTING) de la machine virtuelle basculée sur le portail Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau Ubuntu prises en charge pour les machines virtuelles Azure

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-generic à 3.13.0-153-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-generic à 3.13.0-151-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic à 3.13.0-147-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic à 3.13.0-144-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-119-generic |
|||
LTS 16.04 | 9.19 | 4.4.0-21-generic à 4.4.0-131-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-30-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1019-azure|
LTS 16.04 | 9.18 | 4.4.0-21-generic à 4.4.0-128-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure |
LTS 16.04 | 9.17 | 4.4.0-21-generic à 4.4.0-124-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-41-generic,<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1016-azure |
LTS 16.04 | 9.16 | 4.4.0-21-generic à 4.4.0-119-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-38-generic,<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau Debian prises en charge pour les machines virtuelles Azure

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
Debian 7 | 9.17, 9.18, 9.19 | 3.2.0-4-amd64 à 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-amd64 à 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-amd64 à 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 à 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 à 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau SUSE Linux Enterprise Server 12 prises en charge pour les machines virtuelles Azure

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.19 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default à 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default à 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.17 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default à 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Systèmes de fichiers et configurations de stockage invité pris en charge sur les machines virtuelles Azure exécutant le système d’exploitation Linux

* Systèmes de fichiers : ext3, ext4, ReiserFS (Suse Linux Enterprise Server uniquement), XFS
* Gestionnaire de volume : LVM2
* Logiciel multichemin : Device Mapper

## <a name="region-support"></a>Prise en charge de la région

Vous pouvez répliquer et restaurer des machines virtuelles entre deux régions appartenant au même cluster géographique.

**Cluster géographique** | **Régions Azure**
-- | --
Amérique | Canada de l’Est, Canada du Centre, Sud du Centre des États-Unis, Ouest du Centre des États-Unis, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis de l’Ouest, États-Unis de l’Ouest 2, États-Unis du Centre, Nord du Centre des États-Unis
Europe | Royaume-Uni Ouest, Royaume-Uni Sud, Europe du Nord, Europe de l’Ouest, France-Centre, France-Sud
Asie | Inde du Sud, Centre de l’Inde, Asie du Sud-Est, Asie de l’Est, Japon de l’Est, Japon de l’Ouest, Centre de la Corée, Corée du Sud
Australie   | Australie Est, Australie Sud-Est, Australie Centre, Australie Centre 2
Azure Government    | Gouvernement des États-Unis - Virginie, Gouvernement des États-Unis - Iowa, Gouvernement des États-Unis - Arizona, Gouvernement des États-Unis - Texas, US DoD Est, US DoD Centre
Allemagne | Centre de l’Allemagne, Nord-Est de l’Allemagne
Chine | Est de la Chine, Nord de la Chine

>[!NOTE]
>
> Pour la région Brésil Sud, vous pouvez uniquement effectuer une réplication et un basculement vers les régions USA Centre Sud, USA Centre-Ouest, USA Est, USA Est 2, USA Ouest, USA Ouest 2 et USA Centre Nord, puis effectuer une restauration automatique.

## <a name="support-for-vmdisk-management"></a>Prise en charge de la gestion des machines virtuelles/disques

**Action** | **Détails**
-- | ---
Redimensionner le disque sur la machine virtuelle répliquée | Pris en charge
Ajouter un disque à la machine virtuelle répliquée | Non pris en charge. Vous devez désactiver la réplication pour la machine virtuelle, ajouter le disque, puis réactiver la réplication.


## <a name="support-for-compute-configuration"></a>Prise en charge de la configuration de calcul

**Configuration** | **Prise en charge/Non prise en charge** | **Remarques**
--- | --- | ---
Taille | N’importe quelle taille de machine virtuelle Azure avec au moins 2 cœurs d’UC et 1 Go de RAM | Voir [Tailles de machine virtuelle Azure](../virtual-machines/windows/sizes.md).
Groupes à haute disponibilité | Prise en charge | Si vous utilisez l’option par défaut pendant l’étape « Activation de la réplication » dans le portail, le groupe à haute disponibilité est créé automatiquement en fonction de la configuration de la région source. Vous pouvez changer le groupe à haute disponibilité cible à tout moment dans « Élément répliqué > Paramètres > Calcul et réseau > Groupe à haute disponibilité ».
Zones de disponibilité | Non pris en charge | Les machines virtuelles déployées dans des zones de disponibilité ne sont pas prises en charge.
Machines virtuelles HUB (Hybrid Use Benefit) | Prise en charge | Si la machine virtuelle source a une licence HUB activée, la machine virtuelle de basculement ou de test de basculement utilise également la licence HUB.
Groupes identiques de machines virtuelles  | Non pris en charge |
Images de la galerie Azure publiées par Microsoft | Prise en charge | Prises en charge tant que la machine virtuelle s’exécute sur un système d’exploitation pris en charge par Site Recovery.
Images de la galerie Azure publiées par un tiers | Prise en charge | Prises en charge tant que la machine virtuelle s’exécute sur un système d’exploitation pris en charge par Site Recovery.
Images personnalisées publiées par un tiers | Prise en charge | Prises en charge tant que la machine virtuelle s’exécute sur un système d’exploitation pris en charge par Site Recovery.
Machines virtuelles migrées à l’aide de Site Recovery | Prises en charge | S’il s’agit d’un ordinateur physique/VMware migré vers Azure à l’aide de Site Recovery, vous devez désinstaller l’ancienne version du service Mobilité et redémarrer l’ordinateur avant de le répliquer vers une autre région Azure.

## <a name="support-for-storage-configuration"></a>Prise en charge de la configuration de stockage

**Configuration** | **Prise en charge/Non prise en charge** | **Remarques**
--- | --- | ---
Taille maximale du disque du système d’exploitation | 2048 GB | Voir [Disques utilisés par les machines virtuelles](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
Taille maximale de disque de données | 4095 Go | Voir [Disques utilisés par les machines virtuelles](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
Nombre de disques de données | Jusqu’à 64, tel que pris en charge par une taille spécifique de machine virtuelle Azure | Voir [Tailles de machine virtuelle Azure](../virtual-machines/windows/sizes.md).
Disque temporaire | Toujours exclus de la réplication | Le disque temporaire est exclu de la réplication. Dans les recommandations Azure, il est stipulé que vous ne devez pas placer de données persistantes sur un disque temporaire. Pour plus d’informations, consultez [Disque temporaire sur des machines virtuelles Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Taux de modification des données sur le disque | Maximum de 10 Mbits/s par disque pour le stockage Premium, et 2 Mbits/s par disque pour le stockage Standard | Si le taux moyen de modification des données sur le disque est en permanence supérieur à 10 Mbits/s (pour Premium) et 2 Mbits/s (pour Standard), la réplication ne peut pas suivre. Toutefois, s’il s’agit d’une rafale de données occasionnelle, que le taux de modification des données est supérieur à 10 Mbits/s (pour Premium) et 2 Mbits/s (pour Standard) pendant un certain laps de temps et qu’il redescend par la suite, la réplication peut rattraper le retard. Dans ce cas, certains points de récupération pourront être légèrement différés.
Disques sur des comptes de stockage Standard | Prise en charge |
Disques sur des comptes de stockage Premium | Prise en charge | Si une machine virtuelle a des disques répartis sur des comptes de stockage Standard et Premium, vous pouvez sélectionner un compte de stockage cible différent pour chaque disque afin d’être sûr d’avoir la même configuration de stockage dans la région cible.
Disques gérés Standard | Pris en charge dans les régions Azure dans lesquelles Azure Site Recovery est pris en charge. |  
Disques gérés Premium | Pris en charge dans les régions Azure dans lesquelles Azure Site Recovery est pris en charge. |
Espaces de stockage | Prise en charge |         
Chiffrement au repos (SSE) | Pris en charge | SSE est le paramètre par défaut sur les comptes de stockage.   
Azure Disk Encryption (ADE) pour système d’exploitation Windows | Prise en charge des machines virtuelles activées pour le [chiffrement avec Azure AD app](https://aka.ms/ade-aad-app) |
Azure Disk Encryption (ADE) pour système d’exploitation Linux | Non pris en charge |
Ajout/suppression de disque à chaud | Non pris en charge | Si vous ajoutez ou supprimez un disque de données sur la machine virtuelle, vous devez désactiver la réplication puis la réactiver pour la machine virtuelle.
Exclure le disque | Non pris en charge|   Le disque temporaire est exclu par défaut.
Espaces de stockage direct  | Non pris en charge|
Serveur de fichiers avec montée en puissance parallèle  | Non pris en charge|
LRS | Prise en charge |
GRS | Prise en charge |
RA-GRS | Prise en charge |
ZRS | Non pris en charge |  
Stockage à froid et à chaud | Non pris en charge | Les disques de machine virtuelle ne sont pas pris en charge sur le stockage à froid et à chaud.
Pare-feu pour réseaux virtuels du Stockage Azure  | Non  | L’autorisation d’accès à des réseaux virtuels Azure spécifiques sur des comptes de stockage en cache utilisés pour stocker des données répliquées n’est pas prise en charge.
Comptes de stockage V2 à usage général (niveaux chaud et froid) | Non  | Augmentation significative des coûts de transaction par rapport aux comptes de stockage V1 à usage général

>[!IMPORTANT]
> Vérifiez que vous respectez les valeurs d'évolutivité et de performances des disques VM pour les machines virtuelles [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md) afin d'éviter tout problème de performances. Si vous suivez les paramètres par défaut, Site Recovery créera les disques et les comptes de stockage nécessaires en fonction de la configuration source. Si vous personnalisez et sélectionnez vos propres paramètres, veillez à suivre les cibles de scalabilité et de performances de disque de vos machines virtuelles sources.

## <a name="support-for-network-configuration"></a>Prise en charge de la configuration réseau
**Configuration** | **Prise en charge/Non prise en charge** | **Remarques**
--- | --- | ---
Interfaces réseau | Jusqu’à la quantité maximale de cartes réseau prises en charge par une taille spécifique de machine virtuelle Azure | Les cartes réseau sont créées quand la machine virtuelle est créée dans le cadre de l’opération Test de basculement ou Basculement. Le nombre de cartes réseau sur la machine virtuelle de basculement dépend du nombre de cartes réseau que possède la machine virtuelle source au moment de l’activation de la réplication. Si vous ajoutez/supprimez des cartes réseau après l’activation de la réplication, cela n’affecte pas le nombre de cartes réseau sur la machine virtuelle de basculement.
Équilibreur de charge Internet | Prise en charge | Vous devez associer l’équilibreur de charge préconfiguré de manière à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Équilibreur de charge interne | Prise en charge | Vous devez associer l’équilibreur de charge préconfiguré de manière à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Adresse IP publique| Prise en charge | Vous devez associer une adresse IP publique existante à la carte réseau ou en créer une et l’associer à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Groupe de sécurité réseau sur la carte réseau (Resource Manager)| Prise en charge | Vous devez associer le groupe de sécurité réseau à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.  
Groupe de sécurité réseau sur le sous-réseau (Resource Manager et Classique)| Prise en charge | Vous devez associer le groupe de sécurité réseau au sous-réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Groupe de sécurité réseau sur la machine virtuelle (Classique)| Prise en charge | Vous devez associer le groupe de sécurité réseau à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Adresse IP réservée (adresse IP statique) / Conserver l’adresse IP source | Prise en charge | Si la carte réseau sur la machine virtuelle source a une configuration IP statique et que le sous-réseau cible a la même adresse IP disponible, elle est affectée à la machine virtuelle de basculement. Si le sous-réseau cible n’a pas la même adresse IP disponible, l’une des adresses IP disponibles sur le sous-réseau est réservée à cette machine virtuelle. Vous pouvez spécifier l’adresse IP fixe de votre choix dans « Élément répliqué > Paramètres > Calcul et réseau > Interfaces réseau ». Vous pouvez sélectionner la carte réseau et spécifier le sous-réseau et l’adresse IP de votre choix.
Adresse IP dynamique| Prise en charge | Si la carte réseau sur la machine virtuelle source a une configuration IP dynamique, la carte réseau sur la machine virtuelle de basculement est également dynamique par défaut. Vous pouvez spécifier l’adresse IP fixe de votre choix dans « Élément répliqué > Paramètres > Calcul et réseau > Interfaces réseau ». Vous pouvez sélectionner la carte réseau et spécifier le sous-réseau et l’adresse IP de votre choix.
Intégration de Traffic Manager | Prise en charge | Vous pouvez préconfigurer Traffic Manager pour que le trafic soit acheminé vers le point de terminaison dans la région source de manière régulière et vers le point de terminaison dans la région cible en cas de basculement.
Système DNS géré par Azure | Prise en charge |
Système DNS personnalisé  | Prise en charge |    
Proxy non authentifié | Prise en charge | Voir le [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).    
Proxy authentifié | Non pris en charge | Si la machine virtuelle utilise un proxy authentifié pour la connectivité sortante, elle ne peut pas être répliquée à l’aide d’Azure Site Recovery.    
VPN de site à site avec infrastructure locale (avec ou sans ExpressRoute)| Prise en charge | Vérifiez que les itinéraires définis par l’utilisateur et les groupes de sécurité réseau sont configurés de telle sorte que le trafic Site Recovery ne soit pas acheminé vers l’infrastructure locale. Voir le [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).  
Connexion de réseau virtuel à réseau virtuel | Prise en charge | Voir le [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).  
Points de terminaison de service de réseau virtuel | Prise en charge | Les pare-feu pour réseaux virtuels du Stockage Azure ne sont pas pris en charge. L’autorisation d’accès à des réseaux virtuels Azure spécifiques sur des comptes de stockage en cache utilisés pour stocker des données répliquées n’est pas prise en charge.
Mise en réseau accélérée | Pris en charge | L’accélération réseau doit être activée sur la machine virtuelle source. [Plus d’informations](azure-vm-disaster-recovery-with-accelerated-networking.md)


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Commencez à protéger vos charges de travail en [répliquant des machines virtuelles Azure](site-recovery-azure-to-azure.md).
