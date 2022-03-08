# Script injections / safety issues

## Learning aims
- [What is a script injection and how do these happen?](#1)
- [How would you prevent script injections?](#2)
- [What is the Parameterized query?](#3)
- [Bad practices, including some sample code.](#4)

![](https://uploads.sitepoint.com/wp-content/uploads/2016/09/1473921124injection-attack.jpg)

## What is a code injection? <span id="1"></span>
Code Injection is an attack perpetrated by an attackers ability to inject and execute malicious code into an application.

There are mainly four types of code injections: SQL injection, Script injection, Shell injection, and Dynamic evaluation.


## What is a SQL injection?

SQL injection attacks are staged by sending malicious SQL commands to database servers through web requests. Any input channel can be used to send the malicious commands, including <input> elements, query strings, cookies and files.

 ## How do these happen?
 
 <img src="https://i.imgur.com/zLVshfy.png">
 
## How would you prevent script injections? <span id="2"></span>
To avoid SQL injection flaws is simple. Developers need to either: a) stop writing dynamic queries; and/or b) prevent user supplied input which contains malicious SQL from affecting the logic of the executed query.

![](https://i.imgur.com/awZobed.png)

## 1: Input Validation

![](https://i.imgur.com/hR3vtHI.png)

## 2: Limiting Use Special Character

![](https://i.imgur.com/bk8aF9g.png)

## 3: Parameterized query

## What is meant by a parameterized query? <span id="3"></span>
A parameterized query is where you use parameretes instead of constants in a SQL query. So that only when the query is called at exection time the parameter takes the parameter value.

## Expample:

Let's take a look at what can happen if we don't use parameterized queries. Consider the following code that concatenates user input with SQL syntax:


```js
const addCustomer= (name, email) => {
  return connection.query({
        text: `INSERT INTO Customers (name, email) 
        VALUES (${name}, ${email});`
    });
};
```
 suppose that the user enter the following!
 ![](https://i.imgur.com/o973Vbl.png)
 
 This is a perfectly valid SQL query, and, as you can see, the results of executing this on the server would not be desired becouse the it'll drop the whole Customer Table . 
 
 The simplest and most effective way to avoid the scenario described above is to use parameterized queries. Here is how the code above would look when using a parameterized query:
 

 ```js
const addCustomer= (name, email) => {
  return connection.query({
    text: 'INSERT INTO Customers (name, email) VALUES ($1, $2);',
    values: [name, location]
  });
};
```
     
 
 The difference here is that a query plan is constructed on the server before the query is executed with parameter values.

When you execute this query using parameterized values with the same user input , only the INSERT query is executed. The server accepts the whole user input as a string which will causse no harm.

## Why parameterized query?
- Protect against SQL injection attaks.
- Using a parameterized query allows you to handle less malicious scenarios.
- Allows the query to be reused with different values and for different purposes.


<img src="https://miro.medium.com/max/1332/1*QQJPSw8vXNjdAiWPVa74Lg.png" />

## Bad Practice <span id="4"></span>

- Poor Normalization
- Composite Primary Keys
- Using `SELECT *`
- Using `WHILE(1=1)` or `WHILE(""="")`
- Using batched SQL statements

### Poor Normalization

Database Normalization is the process of organizing the fields and tables in a relational database in order to reduce any unnecessary redundancy.
The process usually involves breaking down larger tables (many columns) into smaller more manageable tables and relating the two using primary keys and foreign keys. It usually follows common logic.

<img src= "https://bs-uploads.toptal.io/blackfish-uploads/uploaded_file/file/191035/image-1582221259279-2f1ab5023099241f071fd10726b0511d.png" />

### Composite Primary Keys

Many database designers talk nowadays about using an integer ID auto-generated field as the primary key instead of a composite one defined by the combination of two or more fields. This is currently defined as the “best practice” and, personally, I tend to agree with it.

<img src ="https://bs-uploads.toptal.io/blackfish-uploads/uploaded_file/file/191036/image-1582221333714-d003c063c7d927fb3c6193be1798f162.png" />

### Using `SELECT *`

- Unneccessary I/O
- Increased Network traffic
- More Application memory
- Risky while copying data

### Using `WHILE(1=1)` or `WHILE(""="")`

<img src="https://miro.medium.com/max/504/1*ibLcqnPa3NrXQeETn5GDNg.png" />


```JS
customerName = getRequestString('customerName');
sqlQuery = 'SELECT * FROM Customers WHERE customerName = ' + customerName;
```

Look at the example above. The original purpose of the code was to create an SQL statement to select a customer, with a given customer name.

If there is nothing to prevent a user from entering "wrong" input, the user can enter some "smart" input like this:

<img src="https://e.top4top.io/p_2258aeci11.png" />

Then, the SQL statement will look like this:

```sql
SELECT * FROM Customers WHERE customerName = Ted OR 1=1;
```

The SQL above is valid and will return ALL rows from the Customers table, since OR 1=1 is always TRUE.

A hacker might get access to all the customers information in a database, by simply inserting Ted OR 1=1 into the input field.

### Using batched SQL statements

A batch of SQL statements is a group of two or more SQL statements, separated by semicolons.

The SQL statement below will return all rows from the "Customers" table, then delete the "Orders" table.

```sql
 SELECT * FROM Customers; DROP TABLE orders 
 ```
Look at the following example: 

```JS
customerName = getRequestString('customerName');
sqlQuery = 'SELECT * FROM Customers WHERE customerName = ' + customerName;
```

And the following input:

<img src="https://d.top4top.io/p_2258c69s01.jpg" />

The valid SQL statement would look like this:
```sql
SELECT * FROM Customers WHERE customerName = 'Ted'; Drop Table orders;
```
So the SQL statement above will return all information about Ted from the "Customers" table, then delete the "Orders" table.

## Conclusion
SQL injection is one of the most common and most effective forms of attack on a system. Controlling the malicious SQL code/script on the web application and maintaining the end privacy is still a key challenge for the web developer. These issues must be considered seriously by the web developers involved in developing websites using databases.

