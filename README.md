# Prep


# JDBC Class Flow 

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

 

* Java cannot directly communicate with database
* JDBC works as a bridge
* JDBC = Java Database Connectivity

---

## Create database in CMD 

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

 

> "Database and table are ready. Now Java will insert values."

---

# Create Java file in Notepad

File name:

```text
Demo.java
```

 

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


### Import package

```java
import java.sql.*;
```

 

> Contains JDBC classes

---

### Load Driver

```java
Class.forName("com.mysql.cj.jdbc.Driver");
```

 

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


> SQL query written inside Java

---

### PreparedStatement

```java
PreparedStatement ps=
con.prepareStatement(q);
```


> Sends SQL query to database

---

### Execute query

```java
ps.executeUpdate();
```


> Executes insert operation

---

## Compile and Run

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

## Verify in MySQL

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


> "Java successfully sent data into database."


---

# Move to Scanner Input


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


# 1. Statement vs PreparedStatement

## Statement

Syntax:

```java id="6lhx8q"
Statement st=con.createStatement();
```

Example:

```java id="9jv7ej"
st.executeUpdate(
"insert into student values(101,'Mahesh','CSE')");
```

Explanation:

> SQL query is written directly inside Java.

Problems:

* Less secure
* SQL Injection possible
* Not efficient for repeated queries

---

## PreparedStatement

Syntax:

```java id="o6x7rr"
PreparedStatement ps=
con.prepareStatement(
"insert into student values(?,?,?)");
```

Example:

```java id="quj7wa"
ps.setInt(1,101);
ps.setString(2,"Mahesh");
ps.setString(3,"CSE");
```

Explanation:

> Query is prepared first and values are inserted later.

Advantages:

* More secure
* Faster
* Avoids SQL injection
* Easy for dynamic input

 

> "In real projects we mostly use PreparedStatement."

---

# 2. executeUpdate()

Syntax:

```java id="n61m7q"
ps.executeUpdate();
```

Used for:

```text id="k9s5k8"
INSERT
UPDATE
DELETE
CREATE
ALTER
DROP
```

Explanation:

> Used when data changes in database.

Examples:

Insert:

```java id="5fgj3m"
String q=
"insert into student values(101,'Mahesh','CSE')";
```

Update:

```java id="prsgs4"
String q=
"update student set branch='ECE' where id=101";
```

Delete:

```java id="9a9ye4"
String q=
"delete from student where id=101";
```

Execute:

```java id="x5u0yu"
ps.executeUpdate();
```

---

# 3. executeQuery()

Syntax:

```java id="wwn56r"
ResultSet rs=
ps.executeQuery();
```

Used for:

```text id="39v3dd"
SELECT
```

Explanation:

> Used to retrieve data from database.

Example:

```java id="slf0dm"
String q="select * from student";

PreparedStatement ps=
con.prepareStatement(q);

ResultSet rs=
ps.executeQuery();

while(rs.next())
{
System.out.println(
rs.getInt(1)+" "+
rs.getString(2)+" "+
rs.getString(3));
}
```

---

# Simple table for board explanation

```text id="6vpk0v"
executeUpdate()            executeQuery()

Insert                     Select
Update
Delete
Create
Alter
Drop

Returns int                Returns ResultSet
Changes data               Retrieves data
```

---

One easy line for students:

> "If data changes → executeUpdate() ; If data displays → executeQuery()"
# JDBC executeQuery() and executeUpdate() – Complete Notes

# 1. What is JDBC?

JDBC stands for **Java Database Connectivity**.

JDBC is used to connect Java applications with databases like:

* MySQL
* Oracle
* PostgreSQL
* SQL Server

Using JDBC we can:

* Insert data
* Retrieve data
* Update data
* Delete data

---

# 2. Important JDBC Steps

## Step 1: Import Packages

```java
import java.sql.*;
```

---

## Step 2: Load Driver

```java
Class.forName("com.mysql.cj.jdbc.Driver");
```

This loads MySQL JDBC Driver.

---

## Step 3: Create Connection

```java
Connection con = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/studentdb",
    "root",
    "password"
);
```

### Explanation

* `localhost` → Database running in same system
* `3306` → MySQL port number
* `studentdb` → Database name
* `root` → Username
* `password` → MySQL password

---

## Step 4: Create Statement

```java
Statement st = con.createStatement();
```

Statement is used to execute SQL queries.

---

# 3. executeQuery()

## Definition

`executeQuery()` is used to retrieve data from database.

It is mainly used with:

* SELECT query

---

# Syntax

```java
ResultSet rs = st.executeQuery("SELECT * FROM students");
```

---

# Important Point

`executeQuery()` always returns a:

```java
ResultSet
```

Object.

---

# What is ResultSet?

`ResultSet` stores data returned from database.

Think like:

Database table data comes into Java and is stored inside ResultSet.

---

# Example Table

## students

| id | name | marks |
| -- | ---- | ----- |
| 1  | Ram  | 90    |
| 2  | Sita | 85    |
| 3  | Ravi | 70    |

When query executes:

```sql
SELECT * FROM students
```

All rows are stored inside `ResultSet rs`.

---

# Full Program Using executeQuery()

```java
import java.sql.*;

public class SelectDemo {
    public static void main(String[] args) {

        try {

            // Load Driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // Create Connection
            Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/studentdb",
                "root",
                "password"
            );

            // Create Statement
            Statement st = con.createStatement();

            // Execute Query
            ResultSet rs = st.executeQuery("SELECT * FROM students");

            // Fetch Data
            while(rs.next()) {

                int id = rs.getInt("id");
                String name = rs.getString("name");
                int marks = rs.getInt("marks");

                System.out.println(id + " " + name + " " + marks);
            }

            // Close Connection
            con.close();

        } catch(Exception e) {
            System.out.println(e);
        }
    }
}
```

---

# 4. Understanding while(rs.next())

This is MOST IMPORTANT.

```java
while(rs.next())
```

---

# What does rs.next() do?

`rs.next()` moves cursor to next row.

It checks:

* Is next row available?
* If available → returns TRUE
* If no rows → returns FALSE

---

# Cursor Concept

Initially cursor is before first row.

| Cursor Position |
| --------------- |
| Before Row 1    |

---

## First rs.next()

Cursor moves to Row 1

| id | name | marks |
| -- | ---- | ----- |
| 1  | Ram  | 90    |

Returns:

```java
true
```

---

## Second rs.next()

Cursor moves to Row 2

| id | name | marks |
| -- | ---- | ----- |
| 2  | Sita | 85    |

Returns:

```java
true
```

---

## Third rs.next()

Cursor moves to Row 3

Returns:

```java
true
```

---

## Fourth rs.next()

No more rows.

Returns:

```java
false
```

Loop stops.

---

# Visualization

```java
while(rs.next())
```

Means:

```java
Move to next row
If row exists:
    execute code
Else:
    stop loop
```

---

# Fetching Data from ResultSet

## Integer Data

```java
rs.getInt("id");
```

---

## String Data

```java
rs.getString("name");
```

---

## Float Data

```java
rs.getFloat("salary");
```

---

# We can also use column index

```java
rs.getInt(1);
rs.getString(2);
```

But column names are better and readable.

---

# 5. executeUpdate()

## Definition

`executeUpdate()` is used for:

* INSERT
* UPDATE
* DELETE

---

# Important Point

`executeUpdate()` returns integer value.

That integer represents:

```java
Number of rows affected
```

---

# Syntax

```java
int rows = st.executeUpdate(query);
```

---

# 6. INSERT Example

## SQL Query

```sql
INSERT INTO students VALUES(4, 'Kiran', 88)
```

---

# Java Program

```java
import java.sql.*;

public class InsertDemo {

    public static void main(String[] args) {

        try {

            // Load Driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // Connection
            Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/studentdb",
                "root",
                "password"
            );

            // Statement
            Statement st = con.createStatement();

            // Insert Query
            String query = "INSERT INTO students VALUES(4, 'Kiran', 88)";

            // Execute Update
            int rows = st.executeUpdate(query);

            // Output
            System.out.println(rows + " row inserted");

            con.close();

        } catch(Exception e) {
            System.out.println(e);
        }
    }
}
```

---

# Output

```java
1 row inserted
```

---

# Why output is 1?

Because:

* One row inserted into database.

---

# 7. UPDATE Example

## SQL Query

```sql
UPDATE students SET marks = 95 WHERE id = 1
```

---

# Java Program

```java
import java.sql.*;

public class UpdateDemo {

    public static void main(String[] args) {

        try {

            Class.forName("com.mysql.cj.jdbc.Driver");

            Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/studentdb",
                "root",
                "password"
            );

            Statement st = con.createStatement();

            String query =
                "UPDATE students SET marks = 95 WHERE id = 1";

            int rows = st.executeUpdate(query);

            System.out.println(rows + " row updated");

            con.close();

        } catch(Exception e) {
            System.out.println(e);
        }
    }
}
```

---

# Output

```java
1 row updated
```

---

# 8. DELETE Example

## SQL Query

```sql
DELETE FROM students WHERE id = 3
```

---

# Java Program

```java
import java.sql.*;

public class DeleteDemo {

    public static void main(String[] args) {

        try {

            Class.forName("com.mysql.cj.jdbc.Driver");

            Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/studentdb",
                "root",
                "password"
            );

            Statement st = con.createStatement();

            String query =
                "DELETE FROM students WHERE id = 3";

            int rows = st.executeUpdate(query);

            System.out.println(rows + " row deleted");

            con.close();

        } catch(Exception e) {
            System.out.println(e);
        }
    }
}
```

---

# Output

```java
1 row deleted
```

---

# Difference Between executeQuery() and executeUpdate()

| executeQuery()         | executeUpdate()                 |
| ---------------------- | ------------------------------- |
| Used for SELECT        | Used for INSERT, UPDATE, DELETE |
| Returns ResultSet      | Returns int                     |
| Retrieves data         | Modifies data                   |
| Used with SELECT query | Used with non-select queries    |

---

# Important Interview Questions

## Q1. What does executeQuery() return?

Answer:

```java
ResultSet
```

---

## Q2. What does executeUpdate() return?

Answer:

```java
int
```

(Number of rows affected)

---

## Q3. Why do we use rs.next()?

Answer:
To move cursor to next row and check whether row exists or not.

---

## Q4. What happens if rs.next() is not used?

Answer:
Cursor stays before first row and data cannot be accessed.

---

# Real Time Flow

```java
Database Table
        ↓
executeQuery()
        ↓
ResultSet rs
        ↓
while(rs.next())
        ↓
Fetch each row data
        ↓
Display Output
```

---

# Simple Memory Trick

## executeQuery()

Q → Query → SELECT → Returns ResultSet

---

## executeUpdate()

U → Update Database → INSERT/UPDATE/DELETE → Returns int

---

# Final Summary

## executeQuery()

* Used for SELECT
* Returns ResultSet
* Data retrieval operation

---

## ResultSet

* Stores database records
* rs.next() moves row by row

---

## executeUpdate()

* Used for INSERT
* Used for UPDATE
* Used for DELETE
* Returns number of rows affected

---

# Mini Practice Tasks for Students

## Task 1

Insert your own data into students table.

---

## Task 2

Display all student records.

---

## Task 3

Update marks of one student.

---

## Task 4

Delete one student record.

---

# End

