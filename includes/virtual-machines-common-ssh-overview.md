---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0363b719e2587226282257e19e58e3c4e55c2219
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454507"
---
## <a name="overview-of-ssh-and-keys"></a>Vue d’ensemble de SSH et des clés

SSH est un protocole de connexion chiffré qui permet d’ouvrir des sessions en toute sécurité à travers des connexions non sécurisées. SSH est le protocole de connexion par défaut pour les machines virtuelles Linux hébergées dans Azure. Bien que le protocole SSH lui-même offre une connexion chiffrée, l’utilisation de mots de passe avec des connexions SSH laisse néanmoins la machine virtuelle vulnérable aux attaques en force brute ou au piratage des mots de passe. Une méthode plus sûre et recommandée pour se connecter à une machine virtuelle à l’aide de SSH consiste à utiliser une paire de clés publique et privée, également appelées *clés SSH*. 

* La *clé publique* est placée sur votre machine virtuelle Linux, ou tout autre service que vous souhaitez utiliser avec le chiffrement à clé publique.

* La *clé privée* est fournie à votre machine virtuelle Linux lorsque vous effectuez une connexion SSH, afin de vérifier votre identité. Protégez cette clé privée. Ne la partagez pas.

Selon les stratégies de sécurité de votre organisation, vous pouvez réutiliser une paire de clés publique-privée unique pour accéder à plusieurs services et machines virtuelles Azure. Vous n’avez pas besoin d’une paire de clés distincte pour chaque machine virtuelle ou service auxquels vous souhaitez accéder. 

Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) devez connaître votre clé privée.