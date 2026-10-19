
# How to Join Two CSV Files in DuckDB

## Overview

DuckDB lets you query and join CSV files directly without creating database tables first.

In this guide, you join `customers.csv` and `orders.csv` using a shared customer ID.

## Before you begin

You need two CSV files:

- `customers.csv`, containing customer IDs, names, countries, and scores.
- `orders.csv`, containing order IDs, customer IDs, order dates, and sales.

Both files must be available in your DuckDB session.

## Query

```sql
SELECT
    c.id,
    c.first_name,
    c.country,
    o.order_id,
    o.order_date,
    o.sales
FROM read_csv(
    'customers.csv',
    header = false,
    columns = {
        'id': 'INTEGER',
        'first_name': 'VARCHAR',
        'country': 'VARCHAR',
        'score': 'INTEGER'
    }
) AS c
INNER JOIN read_csv(
    'orders.csv',
    delim = ',',
    header = false,
    columns = {
        'order_id': 'INTEGER',
        'customer_id': 'INTEGER',
        'order_date': 'DATE',
        'sales': 'INTEGER'
    }
) AS o
ON c.id = o.customer_id
LIMIT 10;
```

## How it works

- `read_csv()` reads each CSV file directly.
- `header = false` tells DuckDB that the files don't have column headers.
- `columns` specifies the column names and data types.
- `AS c` and `AS o` assign short aliases to the two datasets.
- `INNER JOIN` returns rows where a customer ID matches between the two files.
- `ON c.id = o.customer_id` defines the matching condition.
- `LIMIT 10` restricts the output to a maximum of 10 rows.

## Expected result

The query returns matching customer and order information, including the customer name, country, order date, and sales amount.

For example, the first matching row in our test returned customer Maria from Germany, order ID 1001, order date 2021-01-11, and sales of 35.

Your output may contain fewer than 10 rows if fewer matching records exist.

## Troubleshooting

### Error: No files found that match the pattern

DuckDB cannot find one or both CSV files in the current session. Check the available files using `.files` and register or upload any missing files again.

### Unexpected column names

If DuckDB displays names such as `column0` and `column1`, check whether the CSV has a header row. For files without headers, specify `header = false` and define the columns explicitly.

## Key takeaway

You can join CSV files directly in DuckDB using `read_csv()` and a shared key. This lets you explore related datasets without first importing them into permanent database tables.
