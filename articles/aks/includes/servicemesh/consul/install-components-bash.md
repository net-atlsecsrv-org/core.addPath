---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
---

```bash
helm install -f consul-helm/values.yaml --name consul --namespace consul ./consul-helm \
  --set connectInject.enabled=true --set connectInject.nodeSelector="beta.kubernetes.io/os: linux" \
  --set client.enabled=true --set client.grpc=true --set client.nodeSelector="beta.kubernetes.io/os: linux" \
  --set server.nodeSelector="beta.kubernetes.io/os: linux" \
  --set syncCatalog.enabled=true --set syncCatalog.nodeSelector="beta.kubernetes.io/os: linux"
```
