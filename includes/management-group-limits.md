---
 title: include file
 description: include file
 author: tfitzmac
 ms.service: governance
 ms.topic: include
 ms.date: 03/26/2020
 ms.author: tomfitz
 ms.custom: include file
---

| Resource | Limit |
| --- | --- |
| Management groups per directory | 10,000 |
| Subscriptions per management group | Unlimited. |
| Levels of management group hierarchy | Root level plus 6 levels<sup>1</sup> |
| Direct parent management group per management group | One |
| [Management group level deployments](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per location | 800<sup>2</sup> |

<sup>1</sup>The 6 levels don't include the subscription level.

<sup>2</sup>If you reach the limit of 800 deployments, delete deployments from the history that are no longer needed. To delete management group level deployments, use [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) or [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
