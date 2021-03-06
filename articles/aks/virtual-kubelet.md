---
title: Exécuter Virtual Kubelet dans un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser Virtual Kubelet avec Azure Kubernetes Service (AKS) pour exécuter des conteneurs Linux et Windows sur Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/14/2018
ms.author: iainfou
ms.openlocfilehash: b52e491162dcf17eff2ca07bc067586358aa9a35
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393286"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Utiliser Virtual Kubelet avec Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) fournit un environnement hébergé pour l’exécution de conteneurs dans Azure. Lorsque vous utilisez ACI, il n’est pas nécessaire de gérer l’infrastructure sous-jacente de calcul ; Azure le fait pour vous. Lorsque vous exécutez des conteneurs dans ACI, vous êtes facturés à la seconde d’exécution de chaque conteneur.

Lorsque vous utilisez le fournisseur Virtual Kubelet pour Azure Container Instances, il est possible de programmer à la fois les conteneurs Linux et Windows sur une instance de conteneur, comme s’il s’agissait d’un nœud Kubernetes standard. Cette configuration vous permet de tirer parti à la fois des fonctionnalités de Kubernetes et de la facilité de gestion et des économies des instances de conteneurs.

> [!NOTE]
> Virtual Kubelet est un projet open source expérimental, et doit donc être utilisé dans cette optique. Pour contribuer, signaler des problèmes avec les fichiers, et en savoir plus sur virtual kubelet, consultez la page du [projet Virtual Kubelet GitHub][vk-github].

Ce document détaille comment configurer Virtual Kubelet pour les instances de conteneur sur AKS.

## <a name="prerequisite"></a>Configuration requise

Ce document suppose que vous disposez d’un cluster AKS. Si vous avez besoin d’un cluster AKS, consultez le [Guide de démarrage rapide : déployer un cluster Azure Kubernetes Service (AKS)][aks-quick-start].

Vous devez également disposer d’Azure CLI version **2.0.33** ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

Pour installer Virtual Kubelet, [Helm](https://docs.helm.sh/using_helm/#installing-helm) est également requis.

### <a name="for-rbac-enabled-clusters"></a>Pour les clusters où RBAC est activé

Si RBAC est activé dans votre cluster AKS, vous devez créer un compte de service et une liaison de rôle pour une utilisation avec Tiller. Pour plus d’informations, consultez [Contrôle d'accès à Helm basé sur un rôle][helm-rbac]. Pour créer un compte de service et une liaison de rôle, créez un fichier nommé *rbac-virtual-kubelet.yaml* et collez-y la définition suivante :

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Appliquez le compte de service et la liaison avec la commande [kubectl apply][kubectl-apply] et spécifiez votre fichier *rbac-virtual-kubelet.yaml*, comme indiqué dans l’exemple suivant :

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configurez Helm pour utiliser le compte de service tiller :

```console
helm init --service-account tiller
```

Vous pouvez maintenant continuer à installer Virtual Kubelet dans votre cluster AKS.

## <a name="installation"></a>Installation

Utilisez la commande [az aks install-connector][aks-install-connector] pour installer Virtual Kubelet. L’exemple suivant permet de déployer les connecteurs Linux et Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Ces arguments sont disponibles pour la commande `aks install-connector`.

| Argument : | Description | Obligatoire |
|---|---|:---:|
| `--connector-name` | Nom du connecteur ACI.| Oui |
| `--name``-n` | Nom du cluster géré. | Oui |
| `--resource-group``-g` | Nom du groupe de ressources. | Oui |
| `--os-type` | Type de système d’exploitation des instances de conteneur. Valeurs autorisées : Both, Linux, Windows. Par défaut : Linux. | Non  |
| `--aci-resource-group` | Le groupe de ressources dans lequel créer les groupes de conteneurs ACI. | Non  |
| `--location``-l` | L’emplacement de création des groupes de conteneurs ACI. | Non  |
| `--service-principal` | Principal de service utilisé pour l’authentification auprès des API Azure. | Non  |
| `--client-secret` | Secret associé au principal du service. | Non  |
| `--chart-url` | URL du chart Helm qui installe le connecteur ACI. | Non  |
| `--image-tag` | La balise d’image de l’image du conteneur virtual kubelet. | Non  |

## <a name="validate-virtual-kubelet"></a>Valider Virtual Kubelet

Pour valider la version de Virtual Kubelet installée, retournez une liste de nœuds Kubernetes à l’aide de la commande [kubectl get nodes][kubectl-get].

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Exécuter un conteneur Linux

Créez un fichier nommé `virtual-kubelet-linux.yaml` et copiez-y le YAML suivant. Remplacez la valeur `kubernetes.io/hostname` avec le nom du nœud Virtual Kubelet pour Linux. Veuillez noter que [nodeSelector][node-selector] et [toleration][toleration] sont utilisés pour planifier le conteneur au niveau du nœud.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour sortir une liste des pods avec le nœud planifié. Remarquez que le pod `aci-helloworld` a été planifié sur le nœud `virtual-kubelet-virtual-kubelet-linux`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Exécuter un conteneur Windows

Créez un fichier nommé `virtual-kubelet-windows.yaml` et copiez-y le YAML suivant. Remplacez la valeur `kubernetes.io/hostname` avec le nom du nœud Virtual Kubelet pour Linux. Veuillez noter que [nodeSelector][node-selector] et [toleration][toleration] sont utilisés pour planifier le conteneur au niveau du nœud.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour sortir une liste des pods avec le nœud planifié. Remarquez que le pod `nanoserver-iis` a été planifié sur le nœud `virtual-kubelet-virtual-kubelet-win`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Supprimer Virtual Kubelet

Utilisez la commande [az aks remove-connector][aks-remove-connector] pour désinstaller Virtual Kubelet. Remplacez les valeurs de l’argument par le nom du connecteur, le cluster AKS, et le groupe de ressources du cluster AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

> [!NOTE]
> Si vous rencontrez des erreurs lors de la suppression des deux connecteurs de système d’exploitation, ou si vous souhaitez supprimer uniquement le connecteur de système d’exploitation Windows ou Linux, vous pouvez spécifier manuellement le type du système d’exploitation. Ajoutez le paramètre `--os-type` à la commande `az aks remove-connector` précédente, puis spécifiez `Windows` ou `Linux`.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les problèmes susceptibles d’affecter Virtual Kubelet, consultez [Bizarreries connues et solutions de contournement][vk-troubleshooting]. Pour signaler des problèmes avec Virtual Kubelet, [ouvrez un problème GitHub][vk-issues].

Pour en savoir plus sur Virtual Kubelet, consultez la page du [projet Virtual Kubelet Github][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
