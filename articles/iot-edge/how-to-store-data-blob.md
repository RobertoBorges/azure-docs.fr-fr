---
title: Stockage Blob Azure sur les appareils Azure IoT Edge | Microsoft Docs
description: Déployez un module de stockage Blob Azure sur votre appareil IoT Edge pour stocker des données en périphérie.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fdfc1002528fa48145e577dfee3eac935f31fcd
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344844"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Stocker des données à la périphérie avec le Stockage Blob Azure sur IoT Edge (préversion)

Le Stockage Blob Azure sur IoT Edge fournit une solution de stockage des objets blob de blocs en périphérie. Un module de stockage d’objets blob sur votre appareil IoT Edge se comporte comme un service d’objets blob Azure, à ceci près que les objets blob de blocs sont stockés localement sur votre appareil IoT Edge. Vous pouvez accéder à vos objets blob avec les mêmes méthodes que celles du kit SDK de stockage Azure, ou aux appels d’API objets blob de blocs auxquels vous êtes déjà habitué. 

Cet article fournit des instructions pour le déploiement d’un stockage Blob Azure sur le conteneur IoT Edge qui exécute un service d’objets blob sur votre appareil IoT Edge. 

>[!NOTE]
>Le Stockage Blob Azure sur IoT Edge est disponible en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).
* Le module de stockage Blob Azure sur IoT Edge prend en charge les configurations d’appareil suivantes :

   | Système d’exploitation | Architecture |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Standard (mise à jour d’octobre) | AMD64 |
   | Windows 10 IoT Enterprise (mise à jour d’octobre) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Ressources cloud :

* Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Déployer le stockage d’objets blob sur votre appareil

Le stockage Blob Azure sur IoT Edge offre trois images de conteneur standard, deux pour les conteneurs Linux (architectures AMD64 et ARM32) et un pour les conteneurs Windows (AMD64). Lorsque vous utilisez une de ces images de module pour déployer le stockage d’objets blob sur votre appareil IoT Edge, vous fournissez trois informations pour configurer l’instance du module pour votre appareil :

* Un **nom de compte** et une **clé de compte**. Pour maintenir une cohérence avec le stockage Azure, les modules de stockage d’objets blob utilisent des noms de compte et des clés de compte pour la gestion de l’accès. Les noms de compte doivent être compris entre trois et vingt-quatre caractères, et inclure des lettres minuscules et des chiffres. Les clés de compte doivent être encodées en base64 et être d’une longueur de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64).
* Une **option de stockage local**. Le module de stockage d’objets blob stocke les objets blob localement sur l’appareil IoT Edge, afin que les objets blob persistent si le module s’arrête ou redémarre. Déclarez un [volume](https://docs.docker.com/storage/volumes
) existant ou un chemin de dossier local où stocker les objets blob sur votre appareil. 

Il existe plusieurs façons de déployer des modules sur un appareil IoT Edge, et toutes fonctionnent pour les modules de stockage Blob Azure sur IoT Edge. Les deux méthodes les plus simples consistent à utiliser les modèles de Visual Studio Code ou du portail Azure. 

### <a name="azure-portal"></a>Portail Azure

Pour déployer le stockage d’objets blob via le portail Azure, suivez les étapes dans [Déployer des modules Azure IoT Edge à partir du portail Azure](how-to-deploy-modules-portal.md). Avant de procéder au déploiement de votre module, copiez l’URI de l’image et préparez les options de création du conteneur en fonction votre système d’exploitation conteneur. Utilisez ces valeurs à la section **Configurer un manifeste de déploiement** dans l’article de déploiement. 

Fournissez l’URI de l’image pour le module de stockage d’objets blob : **mcr.microsoft.com/azure-blob-storage:latest**. 
   
Utilisez le modèle JSON suivant pour le champ **Options de création de conteneur**. Configurez le fichier JSON avec le nom du compte de stockage, la clé du compte de stockage et la liaison au répertoire de stockage.  
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":{
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       }
   }
   ```   
   
Dans le fichier JSON des options de création, mettez à jour `\<your storage account name\>` avec le nom de votre choix. Mettez à jour `\<your storage account key\>` avec une clé en base64 de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64), qui vous permettent de sélectionner la longueur en octets. Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules.

Dans le fichier JSON des options de création, mettez à jour `<storage directory bind>` en fonction de votre système d’exploitation conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données.  

   * Conteneurs Linux : **\<chemin de stockage>:/blobroot**. Par exemple, /srv/containerdata:/blobroot. Ou, my-volume:/blobroot. 
   * Conteneurs Windows : **\<chemin de stockage>:C:/BlobRoot**. Par exemple, C:/ContainerData:C:/BlobRoot. Ou, my-volume:C:/blobroot.
   
   > [!CAUTION]
   > Ne changez pas le "/blobroot" pour Linux ni le "C:/BlobRoot" pour Windows, pour les valeurs **\<liaison du répertoire de stockage>**.

Il n’est pas nécessaire de fournir des informations d’identification de registre pour accéder au stockage Blob Azure sur IoT Edge, ni de déclarer des itinéraires pour votre déploiement. 

### <a name="visual-studio-code-templates"></a>Modèles Visual Studio Code

Azure IoT Edge fournit des modèles dans Visual Studio Code pour vous aider à développer des solutions de périphérie. Ces étapes nécessitent l’installation de [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement, et sa configuration au moyen de l’[extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Effectuez les étapes suivantes pour créer une solution IoT Edge avec un module de stockage d’objets blob, et configurer le manifeste de déploiement. 

1. Sélectionnez **Affichage** > **Palette de commandes**. 

2. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge : Nouvelle solution IoT Edge**. 

3. Suivez les invites pour créer une solution : 

   1. **Sélectionnez un dossier** - Accédez au dossier dans lequel vous souhaitez créer la solution.  
   
   2. **Indiquez un nom de solution** - Entrez un nom pour votre solution ou acceptez le nom par défaut.
   
   3. **Sélectionnez un modèle de module** - Choisissez **Module existant (Entrez l’URL de l’image complète)**.
   
   4. **Indiquez un nom de module** - Entrez un nom identifiable pour votre module, par exemple **stockageBlobAzure**.
   
   5. **Indiquez l’image Docker pour le module** - Indiquez l’URI de l’image : **mcr.microsoft.com/azure-blob-storage:latest**

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre. 

Le modèle de solution crée un modèle de manifeste de déploiement qui inclut votre image du module de stockage d’objets blob, à condition pour cela de configurer les options de création du module. 

1. Ouvrez **deployment.template.json** dans l’espace de travail de votre nouvelle solution et recherchez la section **modules**. 

2. Supprimez le module **tempSensor**, car il est inutile pour ce déploiement. 

3. Copiez et collez le code suivant dans le champ **createOptions** de votre module de stockage d’objets blob : 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![Mise à jour des options de création du module](./media/how-to-store-data-blob/create-options.png)

4. Dans le fichier JSON des options de création, mettez à jour `<storage directory bind>` en fonction de votre système d’exploitation conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données.  

   * Conteneurs Linux : **\<chemin de stockage>:/blobroot**. Par exemple, /srv/containerdata:/blobroot. Ou, my-volume:/blobroot.
   * Conteneurs Windows : **\<chemin de stockage>:C:/BlobRoot**. Par exemple, C:/ContainerData:C:/BlobRoot. Ou, my-volume:C:/blobroot.
   
   > [!CAUTION]
   > Ne changez pas le "/blobroot" pour Linux ni le "C:/BlobRoot" pour Windows, pour les valeurs **\<liaison du répertoire de stockage>**.

5. Enregistrez **deployment.template.json**.

6. Ouvrez **.env** dans l’espace de travail de votre solution. 

7. Vous n’êtes pas obligé d’entrer des valeurs de registre du conteneur pour l’image du stockage d’objets blob puisqu’il est publiquement disponible. Ajoutez plutôt deux nouvelles variables d’environnement : 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Indiquez un nom de compte de stockage, et fournissez une clé en base64 de 64 octets comme clé de compte de stockage. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules. 

9. Enregistrez **.env**. 

10. Cliquez avec le bouton droit sur **deployment.template.json** et sélectionnez **Générer le manifeste de déploiement IoT Edge**. 

Visual Studio Code récupère les informations que vous avez fournies dans les fichiers deployment.template.json et .env, et les utilise pour créer un fichier manifeste de déploiement. Le manifeste de déploiement est créé dans un nouveau dossier **config** dans l’espace de travail de votre solution. Dès que vous disposez de ce fichier, vous pouvez suivre les étapes décrites dans [Déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md) ou dans [Déployer des modules Azure IoT Edge avec Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Se connecter au module de stockage d’objets blob

Vous pouvez utiliser le nom du compte et la clé de compte que vous avez configurés pour que votre module accède au stockage d’objets blob sur votre appareil IoT Edge. 

Spécifiez votre appareil IoT Edge en tant que point de terminaison d’objets blob pour toutes les demandes de stockage que vous lui adressez. Vous pouvez [Créer une chaîne de connexion pour un point de terminaison de stockage explicite](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) en utilisant les informations de l’appareil IoT Edge et le nom du compte que vous avez configuré. 

1. Pour les modules qui sont déployés sur le même appareil en périphérie, où « Stockage Blob Azure sur IoT Edge » est en cours d’exécution, le point de terminaison d’objet blob est : `http://<Module Name>:11002/<account name>`. 
2. Pour les modules qui sont déployés sur un appareil en périphérie autre que l’appareil en périphérie où « Stockage Blob Azure sur IoT Edge » est en cours d’exécution, le point de terminaison blob est : `http://<device IP >:11002/<account name>` ou `http://<IoT Edge device hostname>:11002/<account name>` ou `http://<FQDN>:11002/<account name>` selon votre configuration.

## <a name="logs"></a>Journaux

Vous trouverez les journaux dans le conteneur, sous : 
* Pour Linux : /blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>Déployer plusieurs instances

Si vous souhaitez déployer plusieurs instances de Stockage Blob Azure sur IoT Edge, il vous suffit de modifier la valeur de HostPort à laquelle le module est lié. Les modules de stockage d’objets blob exposent toujours le port 11002 dans le conteneur, mais vous pouvez déclarer le port auquel il est lié sur l’hôte. 

Modifiez les options de création du module pour changer la valeur de HostPort :

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Lorsque vous vous connectez à des modules de stockage d’objets blob supplémentaires, modifiez le point de terminaison pour qu’il pointe vers le port hôte mis à jour. 

### <a name="try-it-out"></a>Faites un essai

La documentation du stockage Blob Azure comprend des guides de démarrage rapide qui fournissent des exemples de code dans différents langages. Vous pouvez exécuter ces exemples pour tester le stockage Blob Azure sur IoT Edge en changeant le point de terminaison des objets blob pour qu’il pointe vers votre module de stockage d’objets blob.

Les guides de démarrage rapide suivants utilisent des langages qui sont également pris en charge par IoT Edge, vous pouvez donc les déployer en tant que modules IoT Edge en même temps que le module de stockage d’objets blob :

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Opérations de stockage prises en charge

Les modules de stockage d’objets blob sur IoT Edge utilisent les mêmes kits SDK de stockage Azure et sont compatibles avec la version 28-03-2018 de l’API de stockage Azure pour les points de terminaison d’objets blob de blocs. Les versions ultérieures dépendent des besoins des clients. 

Les opérations de stockage Blob Azure ne sont pas toutes prises en charge par le stockage Blob Azure sur IoT Edge. Les sections suivantes détaillent les opérations qui ne sont pas prises en charge. 

### <a name="account"></a>Compte

Prises en charge : 
* Répertorier les conteneurs

Non prises en charge : 
* Obtenir et définir les propriétés du service BLOB
* Demande d’objet blob préliminaire
* Obtenir les statistiques du service BLOB
* Obtenir les informations de compte

### <a name="containers"></a>Containers

Prises en charge : 
* Créer et supprimer un conteneur
* Obtenir les métadonnées et les propriétés de conteneur
* Liste des objets blob

Non prises en charge : 
* Obtenir et définir une ACL de conteneur
* Louer à bail un conteneur
* Définir les métadonnées de conteneur

### <a name="blobs"></a>Objets blob

Prises en charge : 
* Placer, obtenir et supprimer un objet blob
* Obtenir et définir les propriétés d’un objet blob
* Obtenir et définir les métadonnées d’un objet blob

Non prises en charge : 
* Louer à bail un objet blob
* Faire une capture instantanée d’un objet blob
* Copier et abandonner la copie d’un objet blob
* Annuler la suppression d’un objet blob
* Définir un niveau d’objet blob

### <a name="block-blobs"></a>Objets blob de blocs

Prises en charge : 
* Placer le bloc :- Le bloc doit être inférieur ou égal à 4 Mo
* Placer et obtenir une liste de blocs

Non prises en charge :
* Placer un bloc à partir d’une URL

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md)

