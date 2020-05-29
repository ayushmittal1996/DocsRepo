# Java Small DB setup

## Dependency
```java
        <!-- https://mvnrepository.com/artifact/org.xerial/sqlite-jdbc -->
        <dependency>
            <groupId>org.xerial</groupId>
            <artifactId>sqlite-jdbc</artifactId>
            <version>3.8.11.2</version>
        </dependency>
```

## The Code
```java
package other;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class DBHandler {

  private Connection conn = null;
  private final static String dbFilePath = System.getProperty("user.dir") + "/test.db";

  public static void main(String args[]) {
    DBHandler dbOperator = new DBHandler();
    System.out.println(dbFilePath);
    try {
      // 1. Create a database if it does not exist
      dbOperator.createDatabaseFile(dbFilePath);
      // 2. Setup the DB with some default data
      dbOperator.populateDB();
      // 3 Retrieve all the data from the db
      dbOperator.retrieveAllData();
    } catch (ClassNotFoundException cnfe) {
      //System.err.println(""+cnfe.getLocalizedMessage());
      cnfe.printStackTrace();
    } catch (SQLException e) {
      System.err.println(e.getMessage());
    } finally {
      try {
        if (dbOperator.conn != null) {
          dbOperator.conn.close();
        }
      } catch (SQLException e) {
        // connection close failed.
        System.err.println(e);
      }
    }
  }

  public void createDatabaseFile(String filePath) throws SQLException, ClassNotFoundException {
    Class.forName("org.sqlite.JDBC");
    this.conn = DriverManager.getConnection("jdbc:sqlite:" + filePath);
    System.out.println("LOG: Opened database successfully");
  }

  public void populateDB() throws SQLException {
    Statement statement = conn.createStatement();
    statement.setQueryTimeout(5); // set timeout to 5 sec.
    statement.executeUpdate("drop table if exists person");
    System.out.println("LOG: Table Person dropped");
    statement.executeUpdate("create table person (id INT PRIMARY KEY, name VARCHAR (255))");
    System.out.println("LOG: Table Person created");
    statement.executeUpdate("insert into person(id, name) values ((1, 'Ram'), (2,'Shyam'))");
    PreparedStatement preapredStatement = conn
        .prepareStatement("insert into person(id, name) values (?,?)");
    preapredStatement.setObject(1, 1);
    preapredStatement.setObject(2, "Ram");
    preapredStatement.addBatch();
    preapredStatement.setObject(1, 2);
    preapredStatement.setObject(2, "Shyam");
    preapredStatement.addBatch();
    preapredStatement.setObject(1, 3);
    preapredStatement.setObject(2, "Rahul");
    preapredStatement.addBatch();
    int[] result = preapredStatement.executeBatch();
    System.out.println("LOG: Records inserted: " + result.length);
  }

  public void retrieveAllData() throws SQLException {
    Statement statement = conn.createStatement();
    statement.setQueryTimeout(5); // set timeout to 5 sec.
    ResultSet data = statement.executeQuery("select * from person");
    System.out.println("LOG: --------- Table Data -----------");
    while (data.next()) {
      System.out.println("Person => ID: " + data.getInt(1) + ", Name: " + data.getString("Name"));
    }
    System.out.println("LOG: --------- xxxxxxxxxx -----------");
  }

}

```

## Access the db file
Download the [DB Browser for SQLite](https://sqlitebrowser.org/)
Unzip it and open the **DB Browser for SQLite.exe**. 
Then import the DB file
