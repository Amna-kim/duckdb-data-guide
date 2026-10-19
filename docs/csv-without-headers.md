# Why DuckDB Shows column0 Instead of CSV Column Names

## Problem

When reading a CSV file in DuckDB, you may see generic column names such as `column0`, `column1`, and `column2` instead of meaningful names like `id`, `first_name`, and `country`.

This can happen when the CSV file doesn't contain a header row.

## Example CSV

Consider this file named `customers.csv`:

```csv
1,Maria,Germany,350
2," John",USA,900
3,Georg,UK,750
4,Martin,Germany,500
5,Peter,USA,0
```

The first row contains customer data rather than column names.

## Solution

Use DuckDB's `read_csv()` function to specify the column names and data types explicitly.

```sql
SELECT *
FROM read_csv(
    'customers.csv',
    header = false,
    columns = {
        'id': 'INTEGER',
        'first_name': 'VARCHAR',
        'country': 'VARCHAR',
        'score': 'INTEGER'
    }
)
LIMIT 5;
```

## Expected result

The query returns the customer records with these columns:

| id | first_name | country | score |
|----|------------|---------|-------|
| 1  | Maria      | Germany | 350   |
| 2  | John       | USA     | 900   |
| 3  | Georg      | UK      | 750   |
| 4  | Martin     | Germany | 500   |
| 5  | Peter      | USA     | 0     |

The `header = false` option tells DuckDB not to interpret the first row as column names. The `columns` option defines the names and data types to use.

## Alternative: add a header row

If you can edit the CSV file, add a header as its first line:

```csv
id,first_name,country,score
1,Maria,Germany,350
2," John",USA,900
3,Georg,UK,750
4,Martin,Germany,500
5,Peter,USA,0
```

Then read the file with `header = true`:

```sql
SELECT *
FROM read_csv_auto('customers.csv', header = true)
LIMIT 5;
```

## Key takeaway

Check whether a CSV file contains a header row before reading it. If it doesn't, you can either define column names explicitly or add a header row to the file.
