---
title: Gestion du fournisseur de ressources SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez gérer le service de fournisseur de ressources SQL sur Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 360661402289ab9b06eb01be447dc98942c93302
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364085"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Opérations de maintenance du fournisseur de ressources SQL

Le fournisseur de ressources SQL s’exécute sur une machine virtuelle verrouillée. Pour activer les opérations de maintenance, vous devez mettre à jour la sécurité de la machine virtuelle. Si vous souhaitez utiliser le principe de séparation des privilèges à cet effet, vous pouvez utiliser le point de terminaison [PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/powershell/jea/overview) *DBAdapterMaintenance*. Le package d’installation du fournisseur de ressources inclut un script pour cette opération.

## <a name="patching-and-updating"></a>Mise à jour corrective et mise à jour

Le fournisseur de ressources SQL n’est pas traité au sein d’Azure Stack, car il s’agit d’un composant additionnel. Microsoft fournit au besoin des mises à jour du fournisseur de ressources SQL. Lorsqu’une mise à jour d’adaptateur SQL est publiée, un script est fourni pour appliquer la mise à jour. Ce script crée une nouvelle machine virtuelle pour le fournisseur de ressources et effectue la migration de l’état de l’ancienne machine virtuelle du fournisseur vers la nouvelle. Pour plus d’informations, consultez [Mettre à jour le fournisseur de ressources SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Machine virtuelle du fournisseur

Étant donné que le fournisseur de ressources s’exécute sur une machine virtuelle de l’*utilisateur*, vous devez appliquer les correctifs et mises à jour lors de leur publication. Vous pouvez utiliser les mises à jour Windows fournies dans le cadre du cycle de correction et de mise à jour pour appliquer les mises à jour à la machine virtuelle Windows.

## <a name="backuprestoredisaster-recovery"></a>Sauvegarde/restauration/récupération d’urgence

 Le fournisseur de ressources SQL n’est pas traité au sein du processus Azure Stack Business Continuity Disaster Recovery (BCDR), car il s’agit d’un composant additionnel. Des scripts seront fournis pour les opérations suivantes :

- Sauvegarde des informations d’état (stockées dans un compte de stockage Azure Stack).
- Restauration du fournisseur de ressources, si une récupération de la pile complète est requise.

>[!NOTE]
>S’il est nécessaire d’effectuer une récupération, les serveurs de base de données doivent être récupérés en premier, avant le fournisseur de ressources.

## <a name="updating-sql-credentials"></a>Mise à jour des informations d’identification SQL

Vous êtes responsable de la création et de la gestion des comptes administrateur système sur vos serveurs SQL. Le fournisseur de ressources a besoin d’un compte disposant de ces privilèges pour gérer les bases de données pour le compte d’utilisateurs, mais il n’a pas besoin d’accéder aux données utilisateur. Pour mettre à jour les mots de passe d’administrateur système sur vos serveurs SQL, vous pouvez utiliser l’interface d’administration du fournisseur de ressources afin de modifier le mot de passe stocké utilisé par ce dernier. Ces mots de passe sont stockés dans un coffre de clés sur votre instance Azure Stack.

Pour modifier les paramètres, cliquez sur **Parcourir** &gt; **RESSOURCES ADMINISTRATIVES** &gt; **Serveurs d’hébergement SQL** &gt; **Connexions SQL** et sélectionnez un nom d’utilisateur. La modification doit d’abord être effectuée sur l’instance SQL (et sur tous les réplicas, si nécessaire). Sous **Paramètres**, sélectionnez **Mot de passe**.

![Mettre à jour le mot de passe de l’administrateur](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotation des secrets

*Ces instructions s’appliquent uniquement aux systèmes intégrés Azure Stack*.

Quand vous utilisez les fournisseurs de ressources SQL et MySQL avec des systèmes intégrés Azure Stack, l’opérateur Azure Stack est chargé de faire pivoter les secrets d’infrastructure de fournisseurs de ressources suivants pour garantir qu’ils n’expirent pas :

- Certificat SSL externe [fourni au cours du déploiement](azure-stack-pki-certs.md).
- Mot de passe de compte d’administrateur local des machines virtuelles du fournisseur de ressources fourni au moment du déploiement.
- Mot de passe (dbadapterdiag) utilisateur de diagnostic du fournisseur de ressources.

### <a name="powershell-examples-for-rotating-secrets"></a>Exemples PowerShell pour la rotation des secrets

**Changer tous les secrets en même temps.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Changer le mot de passe utilisateur de diagnostic.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Changer le mot de passe de compte administrateur local des machines virtuelles.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Changer le mot de passe du certificat SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Paramètres SecretRotationSQLProvider.ps1

|Paramètre|Description|
|-----|-----|
|AzCredential|Informations d’identification du compte d’administration de service Azure Stack.|
|CloudAdminCredential|Informations d’identification du compte de domaine d’administrateur cloud d’Azure Stack.|
|PrivilegedEndpoint|Point de terminaison privilégié pour accéder à Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostique le mot de passe de compte d’utilisateur.|
|VMLocalCredential|Compte administrateur local de la machine virtuelle MySQLAdapter.|
|DefaultSSLCertificatePassword|Mot de passe du certificat SSL par défaut (*.pfx).|
|DependencyFilesLocalPath|Chemin local des fichiers de dépendances.|
|     |     |

### <a name="known-issues"></a>Problèmes connus

**Problème** : journaux de rotation des secrets.<br>
Les journaux de la rotation des secrets ne sont pas automatiquement collectés en cas d’échec de l’exécution du script personnalisé de la rotation des secrets.

**Solution de contournement** :<br>
Utilisez l’applet de commande Get-AzsDBAdapterLogs pour collecter tous les journaux du fournisseur de ressources, notamment AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, enregistré sous C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Mettre à jour le système d’exploitation de la machine virtuelle

Utilisez l’une des méthodes suivantes pour mettre à jour le système d’exploitation de la machine virtuelle.

- Installer le package de fournisseur de ressources le plus récent à l’aide d’une image corrigée de Windows Server 2016 Core.
- Installer un package Windows Update pendant l’installation ou la mise à jour du fournisseur de ressources.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Mettre à jour les définitions Windows Defender de machine virtuelle

Pour mettre à jour les définitions Windows Defender :

1. Télécharger la mise à jour des définitions Windows Defender à partir de [Définitions Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

   Sur la page de mise à jour des définitions, accédez à « Télécharger et installer les définitions manuellement ». Téléchargez le fichier « Antivirus Windows Defender pour Windows 10 et Windows 8.1 » 64 bits.

   Vous pouvez également utiliser [ce lien direct](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) pour télécharger/exécuter le fichier fpam-fe.exe.

2. Créer une session PowerShell sur le point de terminaison de maintenance de la machine virtuelle de l’adaptateur de fournisseur de ressources SQL.

3. Copier le fichier de mise à jour des définitions sur la machine virtuelle à l’aide de la session du point de terminaison de maintenance.

4. Sur la session PowerShell de maintenance, appeler la commande *Update-DBAdapterWindowsDefenderDefinitions*.

5. Après avoir installé les définitions, nous vous recommandons de supprimer le fichier de mise à jour de définitions à l’aide de la commande *Remove-ItemOnUserDrive*.

**Exemple de script PowerShell pour mettre à jour les définitions.**

Vous pouvez modifier et exécuter le script suivant pour mettre à jour les définitions Defender. Remplacez les valeurs dans le script avec les valeurs de votre environnement.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Collecter des journaux de diagnostic

S’il s’avère nécessaire de collecter des journaux à partir de la machine virtuelle verrouillée, un point de terminaison PowerShell JEA (Just Enough Administration) *DBAdapterDiagnostics* est fourni à cette fin. Ce point de terminaison propose les commandes suivantes :

- **Get-AzsDBAdapterLog**. Cette commande crée un package zip des journaux de diagnostic du fournisseur de ressources et enregistre le fichier sur le lecteur de l’utilisateur de la session. Vous pouvez exécuter cette commande sans aucun paramètre : les quatre dernières heures de journaux seront collectées.
- **Remove-AzsDBAdapterLog**. Cette commande nettoie les packages de journaux existants sur la machine virtuelle du fournisseur de ressources.

### <a name="endpoint-requirements-and-process"></a>Processus et exigences du point de terminaison

Lorsqu’un fournisseur de ressources est installé ou mis à jour, le compte d’utilisateur **dbadapterdiag** est créé. Ce compte vous permet de collecter les journaux de diagnostic.

>[!NOTE]
>Le mot de passe du compte dbadapterdiag est identique au mot de passe utilisé pour l’administrateur local sur la machine virtuelle créée au cours d’un déploiement ou d’une mise à jour de fournisseur.

Pour utiliser les commandes *DBAdapterDiagnostics*, créez une session PowerShell à distance sur la machine virtuelle du fournisseur de ressources, puis appelez la commande **Get-AzsDBAdapterLog**.

Vous définirez l’intervalle de temps pour la collecte de journaux à l’aide des paramètres **FromDate** et **ToDate**. Si vous ne spécifiez pas l’un de ces paramètres ou les deux, les valeurs par défaut suivantes sont utilisées :

- FromDate correspond à quatre heures avant l’heure actuelle.
- ToDate renvoie l’heure en cours.

**Exemple de script PowerShell pour collecter les journaux.**

Le script suivant montre comment collecter les journaux de diagnostic à partir de la machine virtuelle du fournisseur de ressources.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des serveurs d’hébergement SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
