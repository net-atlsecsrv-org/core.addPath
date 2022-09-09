---
title: 'Quickstart: Connect using Python - Azure Database for MySQL'
description: This quickstart provides several Python code samples you can use to connect and query data from Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: [mvc, seo-python-october2019, devx-track-python]
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
---

# Quickstart: Use Python to connect and query data in Azure Database for MySQL

In this quickstart, you connect to an Azure Database for MySQL by using Python. You then use SQL statements to query, insert, update, and delete data in the database from Mac, Ubuntu Linux, and Windows platforms. 

## Prerequisites
For this quickstart you need:

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free).
- Create an Azure Database for MySQL single server using [Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> or [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) if you do not have one.
- Based on whether you are using public or private access, complete **ONE** of the actions below to enable connectivity.

   |Action| Connectivity method|How-to guide|
   |:--------- |:--------- |:--------- |
   | **Configure firewall rules** | Public | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Configure Service Endpoint** | Public | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Configure private link** | Private | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Create a database and non-admin user](./howto-create-users.md)

## Install Python and the MySQL connector

Install Python and the MySQL connector for Python on your computer by using the following steps: 

> [!NOTE]
> This quickstart is using [MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/).

1. Download and install [Python 3.7 or above](https://www.python.org/downloads/) for your OS. Make sure to add Python to your `PATH`, because the MySQL connector requires that.
   
2. Open a command prompt or `bash` shell, and check your Python version by running `python -V` with the uppercase V switch.
   
3. The `pip` package installer is included in the latest versions of Python. Update `pip` to the latest version by running `pip install -U pip`. 
   
   If `pip` isn't installed, you can download and install it with `get-pip.py`. For more information, see [Installation](https://pip.pypa.io/en/stable/installing/). 
   
4. Use `pip` to install the MySQL connector for Python and its dependencies:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Having issues? Let us know](https://aka.ms/mysql-doc-feedback)

## Get connection information

Get the connection information you need to connect to Azure Database for MySQL from the Azure portal. You need the server name, database name, and login credentials.

1. Sign in to the [Azure portal](https://portal.azure.com/).
   
1. In the portal search bar, search for and select the Azure Database for MySQL server you created, such as **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL server name":::
   
1. From the server's **Overview** page, make a note of the **Server name** and **Server admin login name**. If you forget your password, you can also reset the password from this page.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL server name 2":::

## Step 1: Create a table and insert data

Use the following code to connect to the server and database, create a table, and load data by using an **INSERT** SQL statement.The code imports the mysql.connector library, and uses the method:
- [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) function to connect to Azure Database for MySQL using the [arguments](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in the config collection. 
- [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) method executes the SQL query against the MySQL database. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) when you are done using a cursor.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) to close the connection the connection.

> [!IMPORTANT]
> - SSL is enabled by default. You may need to download the [DigiCertGlobalRootG2 SSL certificate](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) to connect from your local environment.
> - Replace the `<mydemoserver>`, `<myadmin>`, `<mypassword>`, and `<mydatabase>` placeholders with the values for your MySQL server and database.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [ClientFlag.SSL],
  'ssl_cert': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[Having issues? Let us know](https://aka.ms/mysql-doc-feedback)

## Step 2: Read data

Use the following code to connect and read the data by using a **SELECT** SQL statement. The code imports the mysql.connector library, and uses [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) method executes the SQL query against the MySQL database. 

The code reads the data rows using the [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) method, keeps the result set in a collection row, and uses a `for` iterator to loop over the rows.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
  	print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## Step 3: Update data

Use the following code to connect and update the data by using an **UPDATE** SQL statement. The code imports the mysql.connector library, and uses [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) method executes the SQL query against the MySQL database. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## Step 4: Delete data

Use the following code to connect and remove data by using a **DELETE** SQL statement. The code imports the mysql.connector library, and uses [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) method executes the SQL query against the MySQL database. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

## Clean up resources

To clean up all resources used during this quickstart, delete the resource group using the following command:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## Next steps
> [!div class="nextstepaction"]
> [Manage Azure Database for MySQL server using Portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Manage Azure Database for MySQL server using CLI](./how-to-manage-single-server-cli.md)

[Cannot find what you are looking for? Let us know.](https://aka.ms/mysql-doc-feedback)
