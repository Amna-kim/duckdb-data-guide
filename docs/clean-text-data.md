# How to Remove Extra Spaces from Text in DuckDB

## Overview

CSV files can contain unwanted spaces before or after text values. These spaces can affect data quality and make values harder to compare.

DuckDB's `TRIM()` function removes spaces from the beginning and end of a string.

## Example problem

Consider a customer record with a name that contains a leading space:

```
" John"
```

The name looks almost identical to `John`, but the extra space is still part of the text value.

## Solution

Use `TRIM()` to remove the extra spaces.

```sql
SELECT
    first_name,
    TRIM(first_name) AS cleaned_name
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
WHERE first_name <> TRIM(first_name);
```

## Expected result

| first_name | cleaned_name |
|------------|--------------|
| `" John"`  | `"John"`     |

The quotation marks are added here only to make the leading space visible. The query displays the original value beside the cleaned value.

## How it works

- `TRIM(first_name)` removes spaces from the beginning and end of the name.
- `AS cleaned_name` gives the cleaned value a descriptive column name.
- `WHERE first_name <> TRIM(first_name)` returns only rows where trimming changes the original value.

## Important note

This query displays cleaned values but does not modify the original CSV file. To save the cleaned data, you need to export the results or use another workflow.

## Key takeaway

Use `TRIM()` to remove unwanted leading and trailing spaces from text values in DuckDB. Filtering for values that change after trimming helps you identify records that need attention.
