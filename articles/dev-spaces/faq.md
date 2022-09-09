---
title: "Frequently asked questions about Azure Dev Spaces"
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: "conceptual"
description: "Find answers to some of the common questions about Azure Dev Spaces"
keywords: "Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s "
---

# Frequently asked questions about Azure Dev Spaces

This addresses frequently asked questions about Azure Dev Spaces.

## Which Azure regions currently provide Azure Dev Spaces?

For a complete list of available regions, see [supported regions and configurations][supported-regions].

## Can I use Azure Dev Spaces without a public IP address?

No, you can't provision Azure Dev Spaces on an AKS Cluster without a public IP. A public IP is [needed by Azure Dev Spaces for routing][dev-spaces-routing].

## Can I use my own ingress with Azure Dev Spaces?

Yes, you can configure your own ingress along side the one Azure Dev Spaces creates. For example, you can use [traefik][ingress-traefik].

## Can I use HTTPS with Azure Dev Spaces?

Yes, you can configure your own ingress with HTTPS using [traefik][ingress-https-traefik].

## Can I use Azure Dev Spaces on a cluster that uses CNI rather than kubenet? 

Yes, you can use Azure Dev Spaces on an AKS cluster that uses CNI for networking. For example, you can use Azure Dev Spaces on an AKS cluster with [existing Windows containers][windows-containers], which uses CNI for networking.

## Can I use Azure Dev Spaces with Windows Containers?

Currently, Azure Dev Spaces is intended to run on Linux pods and nodes only, but you can run Azure Dev Spaces on an AKS cluster with [existing Windows containers][windows-containers].

## Can I use Azure Dev Spaces on AKS clusters with API server authorized IP address ranges enabled?

Yes, you can use Azure Dev Spaces on AKS clusters with [API server authorized IP address ranges][aks-auth-range] enabled. When [creating][aks-auth-range-create] your cluster, you must [allow additional ranges based on your region][aks-auth-range-ranges]. You can also [update][aks-auth-range-update] an existing cluster to allow those additional ranges.

### Can I use Azure Dev Spaces on AKS clusters with restricted egress traffic for cluster nodes?

Yes, you can use Azure Dev Spaces on AKS clusters with [Restricted egress traffic for cluster nodes][aks-restrict-egress-traffic] enabled once the following FQDNs have been allowed:

| FQDN                                    | Port      | Use      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | To pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 | To pull helm/tiller images|
| storage.googleapis.com | HTTP:443 | To pull helm/tiller images|


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md