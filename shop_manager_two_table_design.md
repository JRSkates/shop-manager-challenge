# Shop Manager Tables Design Recipe Template

## 1. Extract nouns from the user stories or specification

```
# EXAMPLE USER STORY:
As a shop manager
So I can know which items I have in stock
I want to keep a list of my shop items with their name and unit price.

As a shop manager
So I can know which items I have in stock
I want to know which quantity (a number) I have for each item.

As a shop manager
So I can manage items
I want to be able to create a new item.

As a shop manager
So I can know which orders were made
I want to keep a list of orders with their customer name.

As a shop manager
So I can know which orders were made
I want to assign each order to their corresponding item.

As a shop manager
So I can know which orders were made
I want to know on which date an order was placed. 

As a shop manager
So I can manage orders
I want to be able to create a new order.

```

```
Nouns:
item, item name, item unit price, item quantity, order, order customer name, order date, order item_id 

```

## 2. Infer the Table Name and Columns

| Record                | Properties                         |
| --------------------- | ---------------------------------- |
| item                  | name, unit_price, stock_quantity   |
| order                 | customer_name, order_date, item_id |

1. Name of the first table (always plural): `items` 

    Column names: `name`, `unit_price`, `stock_quantity`

2. Name of the second table (always plural): `orders` 

    Column names: `customer_name`, `order_date`, `item_id`

## 3. Decide the column types.

[Here's a full documentation of PostgreSQL data types](https://www.postgresql.org/docs/current/datatype.html).

Most of the time, you'll need either `text`, `int`, `bigint`, `numeric`, or `boolean`. If you're in doubt, do some research or ask your peers.

Remember to **always** have the primary key `id` as a first column. Its type will always be `SERIAL`.

```
# EXAMPLE:

Table: items
id: SERIAL
name: text
unit_price: int
stock_quantity: int

Table: 
id: SERIAL
customer_name: text
order_date: date
item_id: foreign

```

## 4. Decide on The Tables Relationship


```
# EXAMPLE

1. Can one Item have many Orders? YES
2. Can one Order have many Items? NO

-> Therefore,
-> An Item HAS MANY Orders
-> An Order BELONGS TO an Item

-> Therefore, the foreign key is on the Orders table.
```

*If you can answer YES to the two questions, you'll probably have to implement a Many-to-Many relationship, which is more complex and needs a third table (called a join table).*

## 4. Write the SQL.

```sql
-- EXAMPLE
-- file: shop_manager_table.sql

-- Replace the table name, columm names and types.

-- Create the table without the foreign key first.
CREATE TABLE Items (
  id SERIAL PRIMARY KEY,
  name text,
  unit_price int,
  stock_quantity int
);

-- Then the table with the foreign key.
CREATE TABLE Orders (
  id SERIAL PRIMARY KEY,
  customer_name text,
  order_date date,

-- The foreign key name is always {other_table_singular}_id
  item_id int,
  constraint fk_item foreign key(item_id)
    references items(id)
    on delete cascade
);

```

## 5. Create the tables.

```bash
psql -h 127.0.0.1 shop_manager < shop_manager_table.sql;
psql -h 127.0.0.1 shop_manager_test < shop_manager_table.sql;
```