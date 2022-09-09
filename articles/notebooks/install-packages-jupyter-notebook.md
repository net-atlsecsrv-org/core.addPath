---
title: Install packages in a Jupyter notebook on Azure
description: How to install Python, R, and F# packages from within a Jupyter notebook running on Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
---

# Install packages from within a notebook

Although you can configure the [environment for your notebook on the project level](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), you may want to install packages directly within an individual notebook.

Packages installed from the notebook apply only to the current server session. Package installations aren't persisted once the server is shut down.

## Python

Packages in Python can be installed using either pip or conda using commands within code cells:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

If the command output indicates that the requirement is already satisfied, then Azure Notebooks may include the package by default. The package might also be installed through a [project environment setup step](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## R

Packages in R can be installed from CRAN or GitHub using the `install.packages` function in a code cell:

```r
install.packages("package_name")
```

You can also install prerelease versions and other development packages from GitHub using the devtools library:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## F#

Packages in F# can be installed from [nuget.org](https://www.nuget.org) by calling the Paket dependency manager from within code cells. First, load the Paket manager:

```fsharp
#load "Paket.fsx"
```

Then install packages:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Then load the paket generator:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Open the libray:
```fsharp
open MathNet.Numerics
```

## Next steps

- [How to: Configure and manage projects](configure-manage-azure-notebooks-projects.md)
- [How to: Present a slide show](present-jupyter-notebooks-slideshow.md)
