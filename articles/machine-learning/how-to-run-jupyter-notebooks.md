---
title: How to run Jupyter Notebooks in your workspace
titleSuffix: Azure Machine Learning
description: Learn how run a Jupyter Notebook without leaving your workspace in Azure Machine Learning studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
# As a data scientist, I want to run Jupyter notebooks in my workspace in Azure Machine Learning studio
---

# How to run Jupyter Notebooks in your workspace


Learn how to run your Jupyter Notebooks directly in your workspace in Azure Machine Learning studio. While you can launch [Jupyter](https://jupyter.org/) or [JupyterLab](https://jupyterlab.readthedocs.io), you can also edit and run your notebooks without leaving the workspace.

See how you can:

* Create Jupyter Notebooks in your workspace
* Run an experiment from a notebook
* Change the notebook environment
* Find details of the compute instances used to run your notebooks

## Prerequisites

* An Azure subscription. If you don't have an Azure subscription, create a [free account](https://aka.ms/AMLFree) before you begin.
* A Machine Learning workspace. See [Create an Azure Machine Learning workspace](how-to-manage-workspace.md).

## <a name="create"></a> Create notebooks

In your Azure Machine Learning workspace, create a new Jupyter notebook and start working. The newly created notebook is stored in the default workspace storage. This notebook can be shared with anyone with access to the workspace. 

To create a new notebook: 

1. Open your workspace in [Azure Machine Learning studio](https://ml.azure.com).
1. On the left side, select **Notebooks**. 
1. Select the  **Create new file** icon above the list **User files** in the **My files** section.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file":::

1. Name the file. 
1. For Jupyter Notebook Files, select **Notebook** as the file type.
1. Select a file directory.
1. Select **Create**.

You can create text files as well.  Select **Text** as the file type and add the extension to the name (for example, myfile.py or myfile.txt)  

You can also upload folders and files, including notebooks, with the tools at the top of the Notebooks page.  Notebooks and most text file types display in the preview section.  No preview is available for most other file types.

> [!IMPORTANT]
> Content in notebooks and scripts can potentially read data from your sessions and access data without your organization in Azure.  Only load files from trusted sources. For more information, see [Secure code best practices](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### Clone samples

Your workspace contains a **Samples** folder with notebooks designed to help you explore the SDK and serve as examples for your own machine learning projects.  You can clone these notebooks into your own folder on your workspace storage container.  

For an example, see [Tutorial: Create your first ML experiment](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="terminal"></a> Use files from Git and version my files

You can access all Git operations by using a terminal window. All Git files and folders will be stored in your workspace file system.

> [!NOTE]
> Add your files and folders anywhere under the **~/cloudfiles/code/Users** folder so they will be visible in all your Jupyter environments.

To access the terminal:

1. Open your workspace in [Azure Machine Learning studio](https://ml.azure.com).
1. On the left side, select **Notebooks**.
1. Select any notebook located in the **User files** section on the left-hand side.  If you don't have any notebooks there, first [create a notebook](#create)
1. Select a **Compute** target or create a new one and wait until it's running.
1. Select the **Open terminal** icon.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Open terminal":::

1. If you don't see the icon, select the **...** to the right of the compute target and then select **Open terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Open terminal from ...":::


Learn more about [cloning Git repositories into your workspace file system](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### Copy and Paste in Terminal

> * Windows: `Ctrl-Insert` to copy and use `Ctrl-Shift-v` or `Shift-Insert` to paste.
> * Mac OS: `Cmd-c` to copy and `Cmd-v` to paste.
> * FireFox/IE may not support clipboard permissions properly.

### Share notebooks and other files

Copy and paste the URL to share a notebook or file.  Only other users of the workspace can access this URL.  Learn more about [granting access to your workspace](how-to-assign-roles.md).

## Edit a notebook

To edit a notebook, open any notebook located in the **User files** section of your workspace. Click on the cell you wish to edit. 

You can edit the notebook without connecting to a compute instance.  When you want to run the cells in the notebook, select or create a compute instance.  If you select a stopped compute instance, it will automatically start when you run the first cell.

When a compute instance is running, you can also use code completion, powered by [Intellisense](https://code.visualstudio.com/docs/editor/intellisense), in any Python Notebook.

You can also launch Jupyter or JupyterLab from the Notebook toolbar.  Azure Machine Learning does not provide updates and fix bugs from Jupyter or JupyterLab as they are Open Source products outside of the boundary of Microsoft Support.

### Focus mode

Use focus mode to expand your current view so you can focus on your active tabs. Focus mode hides the Notebooks file explorer.

1. In the terminal window toolbar, select **Focus mode** to turn on focus mode. Depending on your window width, this may be located under the **...** menu item in your toolbar.
1. While in focus mode, return to the standard view by selecting **Standard view**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Toggle focus mode / standard view":::


### Use IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) is a code-completion aid that includes a number of features: List Members, Parameter Info, Quick Info, and Complete Word. These features help you to learn more about the code you're using, keep track of the parameters you're typing, and add calls to properties and methods with only a few keystrokes.  

When typing code, use Ctrl+Space to trigger IntelliSense.

### Clean your notebook (preview)

> [!IMPORTANT]
> The gather feature is currently in public preview.
> The preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Over the course of creating a notebook, you typically end up with cells you used for data exploration or debugging. The *gather* feature will help you produce a clean notebook without these extraneous cells.

1. Run all of your notebook cells.
1. Select the cell containing the code you wish the new notebook to run. For example, the code that submits an experiment, or perhaps the code that registers a model.
1. Select the **Gather** icon that appears on the cell toolbar.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Screenshot: select the Gather icon":::
1. Enter the name for your new "gathered" notebook.  

The new notebook contains only code cells, with all cells required to produce the same results as the cell you selected for gathering.

### Save and checkpoint a notebook

Azure Machine Learning creates a checkpoint file when you create an *ipynb* file.

In the notebook toolbar, select the menu and then **File&gt;Save and checkpoint** to manually save the notebook and it will add a checkpoint file associated with the notebook.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Screenshot of save tool in notebook toolbar":::

Every notebook is autosaved every 30 seconds. Autosave updates only the initial *ipynb* file, not the checkpoint file.
 
Select **Checkpoints** in the notebook menu to create a named checkpoint and to revert the notebook to a saved checkpoint.


### Useful keyboard shortcuts

|Keyboard  |Action  |
|---------|---------|
|Shift+Enter     |  Run a cell       |
|Ctrl+Space | Activate IntelliSense |
|Ctrl+M(Windows)     |  Enable/disable tab trapping in notebook.       |
|Ctrl+Shift+M(Mac & Linux)     |    Enable/disable tab trapping in notebook.     |
|Tab (when tab trap enabled) | Add a '\t' character (indent)
|Tab (when tab trap disabled) | Change focus to next focusable item (delete cell button, run button, etc.)

## Delete a notebook

You *can't* delete the **Samples** notebooks.  These notebooks are part of the studio and are updated each time a new SDK is published.  

You *can* delete **User files** notebooks in any of these ways:

* In the studio, select the **...** at the end of a folder or file.  Make sure to use a supported browser (Microsoft Edge, Chrome, or Firefox).
* From any Notebook toolbar, select [**Open terminal**](#terminal)  to access the terminal window for the compute instance.
* In either Jupyter or JupyterLab with their tools.

## Run an experiment

To run an experiment from a Notebook, you first connect to a running [compute instance](concept-compute-instance.md). If you don't have a compute instance, use these steps to create one: 

1. Select **+** in the Notebook toolbar. 
2. Name the Compute and choose a **Virtual Machine Size**. 
3. Select **Create**.
4. The compute instance is connected to the Notebook automatically and you can now run your cells.

Only you can see and use the compute instances you create.  Your **User files** are stored separately from the VM and are shared among all compute instances in the workspace.

### View logs and output

Use [Notebook widgets](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true) to view the progress of the run and logs. A widget is asynchronous and provides updates until training finishes. Azure Machine Learning widgets are also supported in Jupyter and JupterLab.

## Change the notebook environment

The Notebook toolbar allows you to change the environment on which your Notebook runs.  

These actions will not change the notebook state or the values of any variables in the notebook:

|Action  |Result  |
|---------|---------| --------|
|Stop the kernel     |  Stops any running cell. Running a cell will automatically restart the kernel. |
|Navigate to another workspace section     |     Running cells are stopped. |

These actions will reset the notebook state and will reset all variables in the notebook.

|Action  |Result  |
|---------|---------| --------|
| Change the kernel | Notebook uses new kernel |
| Switch compute    |     Notebook automatically uses the new compute. |
| Reset compute | Starts again when you try to run a cell |
| Stop compute     |    No cells will run  |
| Open notebook in Jupyter or JupyterLab     |    Notebook opened in a new tab.  |

### Add new kernels

The Notebook will automatically find all Jupyter kernels installed on the connected compute instance.  To add a kernel to the compute instance:

1. Select [**Open terminal**](#terminal) in the Notebook toolbar.
1. Use the terminal window to create a new environment.  For example, the code below creates `newenv`:
    ```shell
    conda create --name newenv
    ```
1. Activate the environment.  For example, after creating `newenv`:

    ```shell
    conda activate newenv
    ```
1. Install pip and ipykernel package to the new environment and create a kernel for that conda env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> For package management within a notebook, use **%pip** or **%conda** magic functions to automatically install packages into the **currently-running kernel**, rather than **!pip** or **!conda** which refers to all packages (including packages outside the currently-running kernel)

Any of the [available Jupyter Kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) can be installed.

### Status indicators

An indicator next to the **Compute** dropdown shows its status.  The status is also shown in the dropdown itself.  

|Color |Compute status |
|---------|---------| 
| Green | Compute running |
| Red |Compute failed | 
| Black | Compute stopped |
|  Light Blue |Compute creating, starting, restarting, setting Up |
|  Gray |Compute deleting, stopping |

An indicator next to the **Kernel** dropdown shows its status.

|Color |Kernel status |
|---------|---------|
|  Green |Kernel connected, idle, busy|
|  Gray |Kernel not connected |

## Find compute details 

Find details about your compute instances on the **Compute** page in [studio](https://ml.azure.com).

## Next steps

* [Run your first experiment](tutorial-1st-experiment-sdk-train.md)
* [Backup your file storage with snapshots](../storage/files/storage-snapshots-files.md)
