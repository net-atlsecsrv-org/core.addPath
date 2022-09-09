---
title: Delete image resources in Azure Container Registry
description: Details on how to effectively manage registry size by deleting container image data using Azure CLI commands.
services: container-registry
author: dlepow
manager: gwallace

ms.service: container-registry
ms.topic: article
ms.date: 07/31/2019
ms.author: danlep
---

# Delete container images in Azure Container Registry using the Azure CLI

To maintain the size of your Azure container registry, you should periodically delete stale image data. While some container images deployed into production may require longer-term storage, others can typically be deleted more quickly. For example, in an automated build and test scenario, your registry can quickly fill with images that might never be deployed, and can be purged shortly after completing the build and test pass.

Because you can delete image data in several different ways, it's important to understand how each delete operation affects storage usage. This article covers several methods for deleting image data:

* Delete a [repository](#delete-repository): Deletes all images and all unique layers within the repository.
* Delete by [tag](#delete-by-tag): Deletes an image, the tag, all unique layers referenced by the image, and all other tags associated with the image.
* Delete by [manifest digest](#delete-by-manifest-digest): Deletes an image, all unique layers referenced by the image, and all tags associated with the image.

Sample scripts are provided to help automate delete operations.

For an introduction to these concepts, see [About registries, repositories, and images](container-registry-concepts.md).

## Delete repository

Deleting a repository deletes all of the images in the repository, including all tags, unique layers, and manifests. When you delete a repository, you recover the storage space used by the images that reference unique layers in that repository.

The following Azure CLI command deletes the "acr-helloworld" repository and all tags and manifests within the repository. If layers referenced by the deleted manifests are not referenced by any other images in the registry, their layer data is also deleted, recovering the storage space.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## Delete by tag

You can delete individual images from a repository by specifying the repository name and tag in the delete operation. When you delete by tag, you recover the storage space used by any unique layers in the image (layers not shared by any other images in the registry).

To delete by tag, use [az acr repository delete][az-acr-repository-delete] and specify the image name in the `--image` parameter. All layers unique to the image, and any other tags associated with the image are deleted.

For example, deleting the "acr-helloworld:latest" image from registry "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Deleting *by tag* shouldn't be confused with deleting a tag (untagging). You can delete a tag with the Azure CLI command [az acr repository untag][az-acr-repository-untag]. No space is freed when you untag an image because its [manifest](container-registry-concepts.md#manifest) and layer data remain in the registry. Only the tag reference itself is deleted.

## Delete by manifest digest

A [manifest digest](container-registry-concepts.md#manifest-digest) can be associated with one, none, or multiple tags. When you delete by digest, all tags referenced by the manifest are deleted, as is layer data for any layers unique to the image. Shared layer data is not deleted.

To delete by digest, first list the manifest digests for the repository containing the images you wish to delete. For example:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Next, specify the digest you wish to delete in the [az acr repository delete][az-acr-repository-delete] command. The format of the command is:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

For example, to delete the last manifest listed in the preceding output (with the tag "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

The `acr-helloworld:v2` image is deleted from the registry, as is any layer data unique to that image. If a manifest is associated with multiple tags, all associated tags are also deleted.

## Delete digests by timestamp

To maintain the size of a repository or registry, you might need to periodically delete manifest digests older than a certain date.

The following Azure CLI command lists all manifest digest in a repository older than a specified timestamp, in ascending order. Replace `<acrName>` and `<repositoryName>` with values appropriate for your environment. The timestamp could be a full date-time expression or a date, as in this example.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

After identifying stale manifest digests, you can run the following Bash script to delete manifest digests older than a specified timestamp. It requires the Azure CLI and **xargs**. By default, the script performs no deletion. Change the `ENABLE_DELETE` value to `true` to enable image deletion.

> [!WARNING]
> Use the following sample script with caution--deleted image data is UNRECOVERABLE. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Deleting the manifest digests will prevent those systems from pulling the images from your registry. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## Delete untagged images

As mentioned in the [Manifest digest](container-registry-concepts.md#manifest-digest) section, pushing a modified image using an existing tag **untags** the previously pushed image, resulting in an orphaned (or "dangling") image. The previously pushed image's manifest--and its layer data--remains in the registry. Consider the following sequence of events:

1. Push image *acr-helloworld* with tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Check manifests for repository *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modify *acr-helloworld* Dockerfile
1. Push image *acr-helloworld* with tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Check manifests for repository *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

As you can see in the output of the last step in the sequence, there is now an orphaned manifest whose `"tags"` property is an empty list. This manifest still exists within the registry, along with any unique layer data that it references. **To delete such orphaned images and their layer data, you must delete by manifest digest**.

## Delete all untagged images

You can list all untagged images in your repository using the following Azure CLI command. Replace `<acrName>` and `<repositoryName>` with values appropriate for your environment.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Using this command in a script, you can delete all untagged images in a repository.

> [!WARNING]
> Use the following sample scripts with caution--deleted image data is UNRECOVERABLE. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Deleting untagged images will prevent those systems from pulling the images from your registry. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md).

**Azure CLI in Bash**

The following Bash script deletes all untagged images from a repository. It requires the Azure CLI and **xargs**. By default, the script performs no deletion. Change the `ENABLE_DELETE` value to `true` to enable image deletion.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI in PowerShell**

The following PowerShell script deletes all untagged images from a repository. It requires PowerShell and the Azure CLI. By default, the script performs no deletion. Change the `$enableDelete` value to `$TRUE` to enable image deletion.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## Automatically purge tags and manifests (preview)

As an alternative to scripting Azure CLI commands, run an on-demand or scheduled ACR task to delete all tags that are older than a certain duration or match a specified name filter. For more information, see [Automatically purge images from an Azure container registry](container-registry-auto-purge.md).

Optionally set a [retention policy](container-registry-retention-policy.md) for each registry, to manage untagged manifests. When you enable a retention policy, image manifests in the registry that don't have any associated tags, and the underlying layer data, are automatically deleted after a set period.

## Next steps

For more information about image storage in Azure Container Registry see [Container image storage in Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
