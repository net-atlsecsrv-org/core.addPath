---
title: Azure HDInsight Tools - Use Visual Studio Code for Hive, LLAP, or PySpark
description: Learn how to use the Azure HDInsight Tools for Visual Studio Code to create and submit queries and scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
---

# Use Azure HDInsight Tools for Visual Studio Code

Learn how to use Azure HDInsight Tools for Visual Studio Code to create and submit Apache Hive batch jobs, interactive Hive queries, and PySpark scripts for Apache Spark. First we'll describe how to install the HDInsight tools in Visual Studio Code and then we'll walk through how to submit jobs to Hive and Spark.  

Azure HDInsight Tools can be installed on platforms that are supported by Visual Studio Code, which include Windows, Linux, and macOS. Below you'll find the prerequisites for different platforms.


## Prerequisites

The following items are required for completing the steps in this article:

- An HDInsight cluster. To create a cluster, see [Get started with HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono is only required for Linux and macOS.
- The [Azure Account extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) for Visual Studio Code.
- [Set up PySpark interactive environment for Visual Studio Code](set-up-pyspark-interactive-environment.md).
- A local directory named **HDexample**.  This article uses  **C:\HD\HDexample**.

## Install Azure HDInsight Tools

After you have completed the prerequisites, you can install  Azure HDInsight Tools for Visual Studio Code.  Complete the following steps to install Azure HDInsight Tools:

1. Open Visual Studio Code.

2. From the menu bar, navigate to **View** > **Extensions**.

3. In the search box, enter **HDInsight**.

4. Select **Azure HDInsight Tools** from the search results, and then select **Install**.  

   ![HDInsight for Visual Studio Code Python install](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Select **Reload** to activate the **Azure HDInsight Tools** extension after it installs.


## Open HDInsight work folder

Complete the following steps to open a work folder, and create a file in Visual Studio Code:

1. From the menu bar, navigate to **File** > **Open Folder...** > **C:\HD\HDexample**, then select the **Select Folder** button. The folder appears in the **Explorer** view on the left.

2. From the **Explorer** view, select the folder, **HDexample**, and then the **New File** icon next to the work folder.

   ![New file](./media/hdinsight-for-vscode/new-file.png)

3. Name the new file with either the .hql (Hive queries) or the .py (Spark script) file extension.  This example uses **HelloWorld.hql**.

## Connect to HDInsight Cluster

Before you can submit scripts to HDInsight clusters from Visual Studio Code, you need to either connect to your Azure account, or link a cluster (using Ambari username/password or domain joined account).  Complete the following steps to connect to Azure:

1. From the menu bar navigate to **View** > **Command Palette...**, and enter **HDInsight: Login**.

    ![HDInsight Tools for Visual Studio Code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Follow the sign in instructions in the **OUTPUT** pane.
    + For Azure global environment, **HDInsight: Login** command will trigger **Sign in to Azure** action in the HDInsight explorer and vice versa.

        ![Sign in instructions for azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + For Other environments, follow the sign in instructions.

        ![Sign in instructions for other environment](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   After you're connected, your Azure account name is shown on the status bar at the bottom left of the Visual Studio Code window.  
  

<h2 id="linkcluster">Create link: Azure HDInsight</h2>

You can link a normal cluster by using an [Apache Ambari](https://ambari.apache.org/) managed username or link an Enterprise Security Pack secure Hadoop cluster by using a domain username (such as: user1@contoso.com).

1. From the menu bar navigate to **View** > **Command Palette...**, and enter **HDInsight: Link a Cluster**.

   ![link cluster command](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Select linked cluster type **Azure HDInsight**.

3. Enter HDInsight cluster URL.

4. Enter Ambari user name, default is **admin**.

5. Enter Ambari password.

6. Select cluster type.

7. Review **OUTPUT** view for verification.

   > [!NOTE]  
   > The linked username and password are used if the cluster both logged in Azure subscription and Linked a cluster.  


## Create link: Generic Livy Endpoint

1. From the menu bar navigate to **View** > **Command Palette...**, and enter **HDInsight: Link a Cluster**.

2. Select linked cluster type **Generic Livy Endpoint**.

3. Enter the generic Livy endpoint, for example: http\://10.172.41.42:18080.

4. Select authorization type **Basic** or **None**.  If **Basic**, then:  
    &emsp;a. Enter Ambari user name, default is **admin**.  
    &emsp;b. Enter Ambari password.

5. Review **OUTPUT** view for verification.

## List HDInsight clusters

1. From the menu bar navigate to **View** > **Command Palette...**, and enter **HDInsight: List Cluster**.

2. Select the desired subscription.

3. Review the **OUTPUT** view.  The view will show your linked cluster(s) and all cluster(s) under your Azure subscription.

    ![Set a default cluster configuration](./media/hdinsight-for-vscode/list-cluster-result.png)

## Set default cluster

1. Re-Open the folder **HDexample** created [earlier](#open-hdinsight-work-folder) if closed.  

2. Select the file  **HelloWorld.hql** created [earlier](#open-hdinsight-work-folder) and it will open in the script editor.

3. [Connect](#connect-to-hdinsight-cluster) to your Azure account if you haven't yet done so.

4. Right-click the script editor, and select **HDInsight: Set Default Cluster**.  

5. Select a cluster as the default cluster for the current script file. The tools automatically update the configuration file **.VSCode\settings.json**. 

   ![Set default cluster configuration](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## Set the Azure environment

1. [Connect](#connect-to-hdinsight-cluster) to your Azure account if you haven't yet done so.

2. From the menu bar navigate to **View** > **Command Palette...**, and enter **HDInsight: Set Azure Environment**.

3. Select an environment as your default login entry.

4. Meanwhile, the tool has already saved your default login entry in **.VSCode\settings.json**. You can also directly update it in this configuration file. 

   ![Set default login entry configuration](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## Submit interactive Hive queries, Hive batch scripts

With HDInsight Tools for Visual Studio Code, you can submit interactive Hive queries, and Hive batch scripts to HDInsight clusters.

1. Reopen the folder **HDexample** created [earlier](#open-hdinsight-work-folder) if closed.  

2. Select the file  **HelloWorld.hql** created [earlier](#open-hdinsight-work-folder) and it will open in the script editor.

3. [Connect](#connect-to-hdinsight-cluster) to your Azure account if you haven't yet done so.

4. Copy and paste the following code into your Hive file, and then save it.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Right-click the script editor, select **HDInsight: Hive Interactive** to submit the query, or use shortcut **Ctrl + Alt + I**.  Select **HDInsight: Hive Batch** to submit the script, or use shortcut **Ctrl + Alt + H**.  

6. Select the cluster if you haven't specified a default cluster. The tools also allow you to submit a block of code instead of the whole script file using the context menu. After a few moments, the query results appear in a new tab.

   ![Interactive Hive result](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTS** panel: You can save the whole result as CSV, JSON, or Excel file to local path, or just select multiple lines.

    - **MESSAGES** panel: When you select **Line** number, it jumps to the first line of the running script.

## Submit interactive PySpark queries

1. Reopen the folder **HDexample** created [earlier](#open-hdinsight-work-folder) if closed.  

2. Create a new file **HelloWorld.py** following the [earlier](#open-hdinsight-work-folder) steps.

3. You will receive a Python extension recommendation dialog if you did not install Python for the prerequisite.  Install and reload Visual Studio Code to complete the installation.

    >![HDInsight for Visual Studio Code Python install](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Connect](#connect-to-hdinsight-cluster) to your Azure account if you haven't yet done so.

5. Copy and paste the following code into the script file:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

6. Right-click the script editor, select **HDInsight: PySpark Interactive** to submit the query, or use shortcut **Ctrl + Alt + I**.  

7. Select the cluster if you haven't specified a default cluster. The tools also allow you to submit a block of code instead of the whole script file using the context menu. After a few moments, the query results appear in a new tab.

   ![Submit Python job result](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. The tool also supports the **SQL Clause** query.

   ![Submit Python job result](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)
   The submission status appears on the left of the bottom status bar when you're running queries. Don't submit other queries when the status is **PySpark Kernel (busy)**.  

>[!NOTE]  
>The clusters can maintain session information. The defined variable, function and corresponding values are kept in the session, so they can be referenced across multiple service calls for the same cluster. 

### PySpark3 is not supported with Spark2.2/2.3

PySpark3 is not supported anymore with Spark 2.2 cluster and Spark2.3 cluster, only "PySpark" is supported for Python. It is known issue that submits to spark 2.2/2.3 fail with Python3.

   ![Submit to python3 get error](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Follow the steps to use Python2.x: 

1. Install Python 2.7 to local computer and add it to system path.

2. Restart Visual Studio Code.

3. Switch to Python 2 by clicking the **Python XXX** at the status bar then choose the target Python.

   ![Select python version](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## Submit PySpark batch job

1. Reopen the folder **HDexample** created [earlier](#open-hdinsight-work-folder) if closed.  

2. Create a new file **BatchFile.py** following the [earlier](#open-hdinsight-work-folder) steps.

3. [Connect](#connect-to-hdinsight-cluster) to your Azure account if you haven't yet done so.

4. Copy and paste the following code into the script file:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. Right-click the script editor, and then select **HDInsight: PySpark Batch**, or use shortcut **Ctrl + Alt + H**. 

5. Select a cluster to which to submit your PySpark job. 

   ![Submit Python job result](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

After you submit a Python job, submission logs appear in the **OUTPUT** window in Visual Studio Code. The **Spark UI URL** and **Yarn UI URL** are shown as well. You can open the URL in a web browser to track the job status.

## Apache Livy configuration

[Apache Livy](https://livy.incubator.apache.org/) configuration is supported, it can be set at the **.VSCode\settings.json** in the work space folder. Currently, livy configuration only supports Python script. More details, see [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**How to trigger livy configuration**

Method 1  
1. From the menu bar, navigate to **File** > **Preferences** > **Settings**.  
2. In the **Search settings** text box enter **HDInsight Job Sumission: Livy Conf**.  
3. Select **Edit in settings.json** for the relevant search result.

Method 2   
Submit a file, notice the .vscode folder is added automatically to the work folder. You can find the livy configuration by clicking **.vscode\settings.json**.

+ The project settings:

    ![Livy configuration](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>For settings **driverMomory** and **executorMomry**, set the value with unit, for example 1g or 1024m. 

+ The supported Livy configurations:   

    **POST /batches**   
    Request Body

    | name | description | type | 
    | :- | :- | :- | 
    | file | File containing the application to execute | path (required) | 
    | proxyUser | User to impersonate when running the job | string | 
    | className | Application Java/Spark main class | string |
    | args | Command line arguments for the application | list of strings | 
    | jars | jars to be used in this session | List of string | 
    | pyFiles | Python files to be used in this session | List of string |
    | files | files to be used in this session | List of string |
    | driverMemory | Amount of memory to use for the driver process | string |
    | driverCores | Number of cores to use for the driver process | int |
    | executorMemory | Amount of memory to use per executor process | string |
    | executorCores | Number of cores to use for each executor | int |
    | numExecutors | Number of executors to launch for this session | int |
    | archives | Archives to be used in this session | List of string |
    | queue | The name of the YARN queue to which submitted | string |
    | name | The name of this session | string |
    | conf | Spark configuration properties | Map of key=val |

    Response Body   
    The created Batch object.

    | name | description | type | 
    | :- | :- | :- | 
    | id | The session id | int | 
    | appId | The application id of this session | 	String |
    | appInfo | The detailed application info | Map of key=val |
    | log | The log lines | list of strings |
    | state | 	The batch state | string |

>[!NOTE]
>The assigned livy config will display in output pane when submit script.

## Integrate with Azure HDInsight from Explorer

**Azure HDInsight** has been added to the Explorer view. You can browse and manage you cluster(s) directly through **Azure HDInsight**.

1. [Connect](#connect-to-hdinsight-cluster) to your Azure account if you haven't yet done so.

2. From the menu bar, navigate to **View** > **Explorer**.

3. From the left pane, expand **AZURE HDINSIGHT**.  The available subscriptions and clusters (Spark, Hadoop, and HBase are supported) will be listed. 

   ![Azure HDInsight Subscription](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expand the cluster to view hive metadata database and table schema.

   ![Azure HDInsight cluster](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## Additional features

HDInsight for Visual Studio Code supports the following features:

- **IntelliSense autocomplete**. Suggestions pop up for keyword, methods, variables, and so on. Different icons represent different types of objects.

    ![HDInsight Tools for Visual Studio Code IntelliSense object types](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense error marker**. The language service underlines the editing errors for the Hive script.     
- **Syntax highlights**. The language service uses different colors to differentiate variables, keywords, data type, functions, and so on. 

    ![HDInsight Tools for Visual Studio Code syntax highlights](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## Unlink cluster

1. From the menu bar navigate to **View** > **Command Palette...**, and then enter **HDInsight: Unlink a Cluster**.  

2. Select cluster to unlink.  

3. Review **OUTPUT** view for verification.  


## Logout  

From the menu bar navigate to **View** > **Command Palette...**, and then enter **HDInsight: Logout**.  There will be a pop-up in the bottom right-hand corner stating **Logout successfully!**.


## Next steps
For a demonstration video of using HDInsight for Visual Studio Code, see [HDInsight for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
