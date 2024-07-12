# NodeJs Mysql2 Utility

## Prerequisites

- [NodeJs](https://nodejs.org/en)
- MySql

## Setup

Run `npm install mysql2`

```js
const mysql = require("mysql2/promise");
// or import mysql from 'mysql2/promise'

const pool = mysql.createPool({
  host: // your host,
  user: // your user,
  password: // your password,
  database: // your database,
  multipleStatements: true,
});
```

## Query

```js
exports.Query = async (sql, params = []) => {
  try {
    const [result] = await pool.query(sql, params);
    if (sql.trim().toUpperCase().startsWith("INSERT")) {
      return { ...result, insertId: result.insertId };
    }
    return result;
  } catch (error) {
    console.error("Error executing query:", error);
    throw error;
  }
};
```

## Transaction

```js
exports.Transaction = async (queries) => {
  let connection;
  try {
    connection = await pool.getConnection();
    await connection.beginTransaction();

    const queryPromises = queries.map((query) => {
      return connection.execute(query.sql, query.values);
    });

    await Promise.all(queryPromises);
    await connection.commit();
    console.log("Transaction successfully committed.");
  } catch (error) {
    if (connection) {
      await connection.rollback();
    }
    console.error("Error executing transaction:", error);
    throw error;
  } finally {
    if (connection) {
      connection.release();
    }
  }
};
```

### Usage Query

```js
const { Query, Transaction } = require("./mysql2.util");

const Select = async () => {
  const sql = "SELECT * FROM users WHERE id = ?";
  const params = [1];
  const result = await Query(sql, params);
  console.log(result);
};

const Insert = async () => {
  const sql = "INSERT INTO users (name, email) VALUES (?, ?)";
  const params = ["Ralph", "ralph@example.com"];
  const result = await Query(sql, params);
  console.log(result);
};

const Update = async () => {
  const sql = "UPDATE users SET name = ? WHERE id = ?";
  const params = ["Ralph", 1];
  const result = await Query(sql, params);
  console.log(result);
};

const Delete = async () => {
  const sql = "DELETE FROM users WHERE id = ?";
  const params = [1];
  const result = await Query(sql, params);
  console.log(result);
};
```

### Usage Transaction

```js
const { Transaction } = require("./mysql2.util");

const TransactionExample = async () => {
  const queries = [
    {
      sql: "INSERT INTO users (name, email) VALUES (?, ?)",
      values: ["Ralph", "ralph@example.com"],
    },
    {
      sql: "INSERT INTO users (name, email) VALUES (?, ?)",
      values: ["Lenard", "lenard@example.com"],
    },
  ];
  await Transaction(queries);
};
```
