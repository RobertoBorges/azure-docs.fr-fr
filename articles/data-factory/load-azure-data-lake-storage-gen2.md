---
title: Charger des données dans Azure Data Lake Storage Gen2 (Preview) avec Azure Data Factory
description: Utiliser Azure Data Factory pour copier des données dans Azure Data Lake Storage Gen2 (Preview)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: jingwang
ms.openlocfilehash: 558b426ea85decb0309390e36910eb18719e6e99
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002525"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-preview-with-azure-data-factory"></a>Charger des données dans Azure Data Lake Storage Gen2 (Preview) avec Azure Data Factory

[Azure Data Lake Storage Gen2 (Préversion)](../storage/data-lake-storage/introduction.md) ajoute un protocole avec des fonctionnalités d’espace de noms et de sécurité d’un système de fichiers hiérarchique au Stockage Blob Azure, ce qui facilite la connexion de frameworks d’analytique à une couche de stockage durable. Dans Data Lake Storage Gen2 (Preview), toutes les qualités du stockage d’objets sont conservées, avec en plus les avantages d’une interface de système de fichiers.

Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez utiliser le service pour remplir le lac avec des données provenant d’un ensemble étendu de banques de données locales et cloud lors de la création de vos solutions d’analytique. Pour une liste détaillée des connecteurs pris en charge, consultez le tableau de [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre une solution de déplacement des données managées qui est évolutive. En raison de l’architecture évolutive d’Azure Data Factory elle peut ingérer des données à un débit élevé. Pour en savoir plus, voir [Performances de l’activité de copie](copy-activity-performance.md).

Cet article vous explique comment utiliser l’outil de copie de données de Data Factory pour charger des données depuis le _service ’Amazon Web Services S3_ dans _Azure Data Lake Store Gen2_. Vous pouvez procéder de même pour copier des données à partir d’autres types de banques de données.

>[!TIP]
>Pour copier des données à partir d’Azure Data Lake Storage Gen1 dans Gen2, reportez-vous à [cette procédure pas à pas spécifique](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Compte Stockage Azure avec Data Lake Storage Gen2 activé : si vous n’avez pas de compte de stockage, cliquez sur [ici](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) pour en créer un.
* Compte AWS avec un compartiment S3 qui contient des données : cet article explique comment copier des données depuis Amazon S3. Vous pouvez utiliser d’autres magasins de données en procédant de la même façon.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Nouveau** > **Données + Analytique** > **Data Factory** :
   
   ![Créer une fabrique de données](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante : 
      
   ![Page Nouvelle fabrique de données](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nom** : saisissez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données \"LoadADLSDemo\" n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement** : sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : sélectionnez un groupe de ressources existant dans la liste déroulante, ou sélectionnez l’option **Créer** et indiquez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * Pour **Version** : sélectionnez **V2**.
    * **Emplacement** : sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. 

3. Sélectionnez **Créer**.
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante : 
   
   ![Page d’accueil Data Factory](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Sélectionnez la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Charger des données dans Azure Data Lake Storage Gen2

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier les données** pour démarrer l’outil Copier les données : 

   ![Vignette de l’outil Copier les données](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Dans la page **Propriétés**, spécifiez **CopyFromAmazonS3ToADLS** dans le champ **Nom de tâche**, puis cliquez sur **Suivant** :

    ![Page Propriétés](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Dans la page **Banques de données sources**, cliquez sur **+ Créer une connexion** :

    ![Page Magasin de données sources](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Sélectionnez **Amazon S3** dans la galerie des connecteurs, puis sélectionnez **Continuer**.
    
    ![Page Banque de données sources s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Sur la page **Spécifier la connexion Amazon S3**, procédez comme suit :
   1. Spécifiez la valeur du champ **ID de clé d’accès**.
   2. Spécifiez la valeur **Clé d’accès secrète**.
   3. Cliquez sur **Tester la connexion** pour vérifier les paramètres, puis sélectionnez **Terminer**.
   
   ![Spécification du compte Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   
   4. Vous voyez qu’une nouvelle connexion est créée. Sélectionnez **Suivant**.
   
5. Sur la page de **sélection du fichier ou dossier d’entrée**, accédez au dossier et au fichier sur lesquels effectuer la copie. Sélectionnez le dossier/fichier, puis sélectionnez **Choisir** :

    ![Choisir le fichier ou le dossier d’entrée](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Spécifiez le comportement de copie en cochant les options **Copier les fichiers de façon récursive** et **Copie binaire**. Sélectionnez **Suivant** :

    ![Spécification du dossier de sortie](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Dans la page **Banque de données de destination**, cliquez sur **+ Créer une connexion**, puis sélectionnez **Azure Data Lake Storage Gen2 (Preview)** et sélectionnez **Continuer**  :

    ![Page Magasin de données de destination](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Dans la page **Spécifier la connexion Data Lake Store**, effectuez les étapes suivantes :

   1. Sélectionnez votre compte activé pour Data Lake Storage Gen2 dans la liste déroulante « Nom du compte de stockage ».
   2. Sélectionnez **Suivant**.
   
   ![Indiquer un compte Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Dans la page de **sélection du fichier ou dossier de sortie**, saisissez **copyfroms3** dans le champ du nom du dossier de sortie, puis sélectionnez **Suivant** : 

    ![Spécification du dossier de sortie](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Dans la page **Paramètres**, sélectionnez **Suivant** pour utiliser les paramètres par défaut.

    ![Page Paramètres](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant** :

    ![Page de résumé](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Dans la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline :

    ![Page Déploiement](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution de l’activité et de réexécuter le pipeline :

    ![Surveiller des exécutions de pipelines](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Pour surveiller les détails d’exécution de chaque activité de copie, sélectionnez le lien **Détails** (image de lunettes) sous **Actions** dans la vue de surveillance des activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées :

    ![Détails du suivi de l'exécution des activités](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Vérifiez que les données sont copiées dans votre compte Azure Data Lake Store Gen2 :

## <a name="best-practices"></a>Meilleures pratiques

Quand vous copiez un grand volume de données à partir de la banque de données de type fichiers, nous vous recommandons de :

- Partitionner les fichiers en ensembles de fichiers de 10 à 30 To chacun.
- Ne pas déclencher trop d’exécutions de copie simultanées, de façon à éviter les limitations des banques de données sources ou réceptrices. Vous pouvez commencer avec l’exécution d’une seule copie et en surveiller le débit, puis ajouter progressivement d’autres exécutions en fonction des besoins.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble des activités de copie](copy-activity-overview.md)
* [Connecteur Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)