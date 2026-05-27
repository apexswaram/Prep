# Prep


# JDBC Class Flow 
## Step 1: Start with a question

Ask students:

> "How can Java store student details permanently?"

Students may say:

* File
* Database

Then say:

> "Today Java will send data into MySQL database."

Draw this on the board:

```text
Java Program
      ↓
JDBC Driver
      ↓
MySQL Database
      ↓
Student Table
```

Explain:

* Java cannot directly communicate with database
* JDBC works as a bridge
* JDBC = Java Database Connectivity

---

## Step 2: Create database in CMD (5–10 min)

Open CMD:

```sql
mysql -u root -p
```

Enter password.

Create database:

```sql
CREATE DATABASE studentdb;
```

Use database:

```sql
USE studentdb;
```

Create table:

```sql
CREATE TABLE student(
id INT,
name VARCHAR(50),
branch VARCHAR(50)
);
```

Check:

```sql
DESC student;
```

Output:

```text
+---------+-------------+
| Field   | Type        |
+---------+-------------+
| id      | int         |
| name    | varchar(50) |
| branch  | varchar(50) |
+---------+-------------+
```

Tell students:

> "Database and table are ready. Now Java will insert values."

---

# Step 3: Create Java file in Notepad

File name:

```text
Demo.java
```

Tell students:

> "First we directly insert values. No Scanner."

Code:

```java
import java.sql.*;

class Demo
{
    public static void main(String args[])
    {
        try
        {
            //Load Driver

            Class.forName("com.mysql.cj.jdbc.Driver");

            //Create Connection

            Connection con=
            DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/studentdb",
            "root",
            "root");

            //SQL Query

            String q=
            "insert into student values(101,'Mahesh','CSE')";

            //Prepare Statement

            PreparedStatement ps=
            con.prepareStatement(q);

            //Execute

            ps.executeUpdate();

            System.out.println("Data Inserted");

            con.close();
        }

        catch(Exception e)
        {
            System.out.println(e);
        }

    }
}
```

---

## Step 4: Explain line by line

### Import package

```java
import java.sql.*;
```

Explain:

> Contains JDBC classes

---

### Load Driver

```java
Class.forName("com.mysql.cj.jdbc.Driver");
```

Explain:

> Loads MySQL JDBC driver

---

### Connection

```java
Connection con=
DriverManager.getConnection(
"jdbc:mysql://localhost:3306/studentdb",
"root",
"root");
```

Explain:

```text
localhost → current system
3306 → MySQL port
studentdb → database
root → username
root → password
```

---

### SQL Query

```java
String q=
"insert into student values(101,'Mahesh','CSE')";
```

Explain:

> SQL query written inside Java

---

### PreparedStatement

```java
PreparedStatement ps=
con.prepareStatement(q);
```

Explain:

> Sends SQL query to database

---

### Execute query

```java
ps.executeUpdate();
```

Explain:

> Executes insert operation

---

## Step 5: Compile and Run

Compile:

```cmd
javac -cp ".;mysql-connector-j-9.4.0.jar" Demo.java
```

Run:

```cmd
java -cp ".;mysql-connector-j-9.4.0.jar" Demo
```

Output:

```text
Data Inserted
```

---

## Step 6: Verify in MySQL

```sql
SELECT * FROM student;
```

Output:

```text
+------+---------+---------+
| id   | name    | branch |
+------+---------+---------+
| 101  | Mahesh  | CSE    |
+------+---------+---------+
```

Tell students:

> "Java successfully sent data into database."

Students usually get excited here.

---

# Step 7: Move to Scanner Input

Now say:

> "Currently values are fixed. Let's make them dynamic."

Code:

```java
import java.sql.*;
import java.util.*;

class Demo
{
    public static void main(String args[])
    {
        Scanner sc=new Scanner(System.in);

        System.out.println("Enter ID:");
        int id=sc.nextInt();

        sc.nextLine();

        System.out.println("Enter Name:");
        String name=sc.nextLine();

        System.out.println("Enter Branch:");
        String branch=sc.nextLine();

        try
        {
            Class.forName(
            "com.mysql.cj.jdbc.Driver");

            Connection con=
            DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/studentdb",
            "root",
            "root");

            String q=
            "insert into student values(?,?,?)";

            PreparedStatement ps=
            con.prepareStatement(q);

            ps.setInt(1,id);
            ps.setString(2,name);
            ps.setString(3,branch);

            ps.executeUpdate();

            System.out.println(
            "Inserted Successfully");

            con.close();
        }

        catch(Exception e)
        {
            System.out.println(e);
        }

    }
}
```

---

## Explain `?`

```java
?,?,?
```

means:

> placeholders for values

```java
ps.setInt(1,id);
ps.setString(2,name);
ps.setString(3,branch);
```

Explain:

```text
1 → first ?
2 → second ?
3 → third ?
```

---

## Final summary for board

```text
1. Create Database
2. Create Table
3. Import package
4. Load Driver
5. Create Connection
6. Write SQL Query
7. Create PreparedStatement
8. Execute Query
9. Close Connection
```

This order is easier because students see **working JDBC first**, then **dynamic input later**.
