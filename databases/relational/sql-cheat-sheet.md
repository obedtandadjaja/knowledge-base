# SQL Cheat Sheet

## Joins

![img](https://i.stack.imgur.com/4zjxm.png)

## Select

```sql
-- simplest for of select. Get all columns from table
select * from orders;

-- add additional column data to the rows
select
  *,
  'I will show up in every row', -- you can declare static scalar data that will be the same for all rows
  current_timestamp as time -- call dynamic functions, and name the column "time"
from orders;

-- Do math
select (102341 * 122354) / 100, 1340-3, 7*191, 8022/6;

-- Display only 10 rows from the table
select * from table_name limit 10;

-- Display rows ordered by first column ascending then second column descending
select name, age
from employees
order by name asc, age desc;

-- Combine data from multiple columns
select
  products.price * products.count as total_asset_amount,
  concat(products.brand, ' ', products.name) as name
from products;

-- if/then/else logic
select
  case
  when x > y then z
  when a < b then c
  else d
  end
from alphabet;

-- Connect 2 disjoint queries with the same columns
select x, y, z
from table1
union all
select a, b, c
from table2;

-- Select rows that occur in both queries
select * from table1
intersect
select * from table2;

-- Select rows that occur in first statement but not in second
select * from table1
except
select * from table2;

-- Set default values when null
select coalesce(iamnull, 'this is a default value')
from table;

-- Remove duplicates
select distinct firstname, lastname
from employees;
```

### Get one record based on column and ordered

```sql
-- distinct on (X) - X determines the uniqueness. In this case, the rows must have different orderId
select distinct on ("orderId") *
from transactions
where "paidAt" is not null
-- the first record by this ordering is used as the result for the uniqueness criteria
ORDER BY
  "orderId",
  "paidAt" asc
```

### Recursion

```sql
with recursive product_type_tree as (
    select *
    from product_types
    where id = :id and "deletedAt" is null
    union all
    select product_types.*
    from product_types
    inner join product_type_tree
    on product_type_tree."parentId" = product_types.id
    where product_types."deletedAt" is null
) select * from product_type_tree where id != :id;
```

## Where

```sql
select *
from orders
where 
  "userId" = 1 -- value equality
  and "createdAt" >= '2022-01-01' -- comparison
  and "createdAt" <= '2022-12-31'
  and source like '%bioma%' -- wildcard selection, must have "bioma"
  and "deletedAt" is not null -- null check
  and id not in (1, 2, 3) -- inclusivity check
```

## Group by

Groups multiple rows together based on common data points. For columns that are not included in these data points, aggregate functions have to be used.

```sql
select
  "userId",
  date,
  count(*) as order_count,
  max(amount) as most_expensive,
  min(amount) as least_expensive,
  array_agg(id) as order_ids,
from orders
group by "userId", date; -- These match the select columns without aggregate functions

-- Having - search condition based on aggregate
select
  "userId",
  count(*) as order_count
from orders
group by "userId"
having count(*) > 5;

select
  "userId",
  sum(amount) as total_amount
from orders
group by "userId"
having count(amount) > 1000000000;

-- Grouping set - apply multiple group by
select
  brand,
  segment,
  SUM (quantity)
from sales
group by
    grouping sets (
        (brand, segment),
        (brand),
        (segment),
        ()
    );
```

## Transaction

DB transactions can help ensure that operations are atomic. Everything fails and succeeds together.

```sql
begin transaction; -- always declared first before your insert|update|delete statements.

-- Your insert|update|delete statements

-- Your select statement to check if the mutation is correct

abort; -- do this if there are any bugs or failures with the query
commit; -- only do this if you want to persist the change
```

## Insert

```sql
-- Vanilla insert
insert into employee (emp_name, emp_salary) values
('John', 5000),
('Jack', 4568.0),
('Robert',7500.50)
returning employee.emp_name;

-- Insert from table
insert into employee (emp_name, emp_salary)
from (
	select name, salary
	from legacytable
) as data
returning employee.id;
```

## Update

```sql
-- Vanilla update
update users set name = 'Obed' where id = 2;

-- Update from table
update order_items
set status = a.status 
from (
select oi.id, oi."orderId", a.status
from order_items oi
left join assets a on a.id = oi."assetId" 
where oi.status != a.status 
and oi.status in ('processing_for_outbound')
and a.status in ('processing_for_outbound)
and oi."startsAt" < current_timestamp and oi."endsAt" > current_timestamp - interval '14' day) a
where order_items.id = a.id;
```

## Delete

```sql
-- Vanilla delete
delete from users where id = 2;

-- Delete from subquery
delete from users where id in (...subquery)
```

## Subquery

Subquery can be useful for making:
1. Pseudo tables
1. Separate calculations apart to make queries easier to understand

```sql
select
  u.id,
  -- subquery at select must return 1 row
  (
    select o.id 
    from orders 
    where "userId" = u.id
    order by date desc 
    limit 1)
from users u
left join addresses a on u.id = a."userId"
-- subquery in from-clause to make pseudo-tables
left join (
    select 
      o.id,
      o."userId",
      sum(t.amount) as amount,
      max(t."paidAt") as latestpayment
    from orders o
    left join transactions t on o.id = t."orderId"
    group by 1, 2
) ordertransactions on u.id = ordertransactions."userId"
where
  -- subquery in where-clause
  u.id not in (
    select distinct "userId"
    from fraudulent_activities fa
    where date > current_timestamp - interval '30' days
  );
```

## View

View is a stored query on the database. Think subquery but persistent.

```
-- Create view
create view myquery as
select * from customers;

-- Delete view
drop view myquery;
```