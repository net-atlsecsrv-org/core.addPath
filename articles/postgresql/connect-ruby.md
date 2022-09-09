---
title: 'Quickstart: Connect with Ruby - Azure Database for PostgreSQL - Single Server'
description: This quickstart provides a Ruby code sample you can use to connect and query data from Azure Database for PostgreSQL - Single Server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
---

# Quickstart: Use Ruby to connect and query data in Azure Database for PostgreSQL - Single Server

This quickstart demonstrates how to connect to an Azure Database for PostgreSQL using a [Ruby](https://www.ruby-lang.org) application. It shows how to use SQL statements to query, insert, update, and delete data in the database. The steps in this article assume that you are familiar with developing using Ruby, and are new to working with Azure Database for PostgreSQL.

## Prerequisites
This quickstart uses the resources created in either of these guides as a starting point:
- [Create DB - Portal](quickstart-create-server-database-portal.md)
- [Create DB - Azure CLI](quickstart-create-server-database-azure-cli.md)

You also need to have installed:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby pg](https://rubygems.org/gems/pg/), the PostgreSQL module for Ruby

## Get connection information
Get the connection information needed to connect to the Azure Database for PostgreSQL. You need the fully qualified server name and login credentials.

1. Log in to the [Azure portal](https://portal.azure.com/).
2. From the left-hand menu in Azure portal, click **All resources**, and then search for the server you have created (such as **mydemoserver**).
3. Click the server name.
4. From the server's **Overview** panel, make a note of the **Server name** and **Server admin login name**. If you forget your password, you can also reset the password from this panel.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="Azure Database for PostgreSQL server name":::

> [!NOTE]
> The `@` symbol in the Azure Postgres username has been url encoded as `%40` in all the connection strings.

## Connect and create a table
Use the following code to connect and create a table using **CREATE TABLE** SQL statement, followed by **INSERT INTO** SQL statements to add rows into the table.

The code uses a ```PG::Connection``` object with constructor ```new``` to connect to Azure Database for PostgreSQL. Then it calls method ```exec()``` to run the DROP, CREATE TABLE, and INSERT INTO commands. The code checks for errors using the ```PG::Error``` class. Then it calls method ```close()``` to close the connection before terminating. See [Ruby Pg reference documentation](https://www.rubydoc.info/gems/pg/PG) for more information on these classes and methods.

Replace the `host`, `database`, `user`, and `password` strings with your own values.


```ruby
require 'pg'

begin
	# Initialize connection variables.
	host = String('mydemoserver.postgres.database.azure.com')
	database = String('postgres')
    user = String('mylogin%40mydemoserver')
	password = String('<server_admin_password>')

	# Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
	puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## Read data
Use the following code to connect and read the data using a **SELECT** SQL statement.

The code uses a  ```PG::Connection``` object with constructor ```new``` to connect to Azure Database for PostgreSQL. Then it calls method ```exec()``` to run the SELECT command, keeping the results in a result set. The result set collection is iterated over using the `resultSet.each do` loop, keeping the current row values in the `row` variable. The code checks for errors using the ```PG::Error``` class. Then it calls method ```close()``` to close the connection before terminating. See [Ruby Pg reference documentation](https://www.rubydoc.info/gems/pg/PG) for more information on these classes and methods.

Replace the `host`, `database`, `user`, and `password` strings with your own values.

```ruby
require 'pg'

begin
	# Initialize connection variables.
	host = String('mydemoserver.postgres.database.azure.com')
	database = String('postgres')
    user = String('mylogin%40mydemoserver')
	password = String('<server_admin_password>')

	# Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## Update data
Use the following code to connect and update the data using a **UPDATE** SQL statement.

The code uses a  ```PG::Connection``` object with constructor ```new``` to connect to Azure Database for PostgreSQL. Then it calls method ```exec()``` to run the UPDATE command. The code checks for errors using the ```PG::Error``` class. Then it calls method ```close()``` to close the connection before terminating. See [Ruby Pg reference documentation](https://www.rubydoc.info/gems/pg/PG) for more information on these classes and methods.

Replace the `host`, `database`, `user`, and `password` strings with your own values.

```ruby
require 'pg'

begin
	# Initialize connection variables.
	host = String('mydemoserver.postgres.database.azure.com')
	database = String('postgres')
    user = String('mylogin%40mydemoserver')
	password = String('<server_admin_password>')

	# Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## Delete data
Use the following code to connect and read the data using a **DELETE** SQL statement.

The code uses a  ```PG::Connection``` object with constructor ```new``` to connect to Azure Database for PostgreSQL. Then it calls method ```exec()``` to run the UPDATE command. The code checks for errors using the ```PG::Error``` class. Then it calls method ```close()``` to close the connection before terminating.

Replace the `host`, `database`, `user`, and `password` strings with your own values.

```ruby
require 'pg'

begin
	# Initialize connection variables.
	host = String('mydemoserver.postgres.database.azure.com')
	database = String('postgres')
    user = String('mylogin%40mydemoserver')
	password = String('<server_admin_password>')

	# Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
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
> [Migrate your database using Export and Import](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [Ruby Pg reference documentation](https://www.rubydoc.info/gems/pg/PG)
