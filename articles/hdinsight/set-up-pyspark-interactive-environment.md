---
title: PySpark interactive environment with Azure HDInsight Tools
description: Learn how to use the Azure HDInsight Tools for Visual Studio Code to create and submit queries and scripts.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
---

# Set up the PySpark interactive environment for Visual Studio Code

The following steps show you how to set up the PySpark interactive environment in VS Code.

We use **python/pip** command to build virtual environment in your Home path. If you want to use another version, you need to change default version of **python/pip** command manually. More details see [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Install [Python](https://www.python.org/downloads/) and [pip](https://pip.pypa.io/en/stable/installing/).

   * Install Python from [https://www.python.org/downloads/](https://www.python.org/downloads/).
   * Install pip from [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (if it's not installed from the Python installation).
   * Validate that Python and pip are installed successfully by using the following commands. (Optional)

        ![Check Python pip version command](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > It is recommended to manually install Python instead of using the macOS default version.

2. Install **virtualenv** by running command below.

   ```bash
   pip install virtualenv
   ```

## Other packages

If you come across an error message, install the required packages by running the following commands:

   ![Install libkrb5 package for python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Restart VS Code, and then go back to the script editor that's running **HDInsight: PySpark Interactive**.

## Next steps

### Demo

* HDInsight for VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### Tools and extensions

* [Use Azure HDInsight Tool for Visual Studio Code](hdinsight-for-vscode.md)
* [Use Azure Toolkit for IntelliJ to create and submit Apache Spark Scala applications](spark/apache-spark-intellij-tool-plugin.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster](spark/apache-spark-jupyter-notebook-install-locally.md)
