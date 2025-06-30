What Is a Source in dbt?
A source in dbt represents a raw table that already exists in your data warehouse. Declaring it allows dbt to reference and document this external data without transforming or loading it.

Purpose:
The source() macro tells dbt where to query pre-existing data from your data platform (e.g., BigQuery, Snowflake).

Declaring a Source (YAML Example):

version: 2

sources:
  - name: jaffle_shop
    database: raw
    schema: jaffle_shop_dataset
    tables:
      - name: orders
        identifier: jaffle_orders_information_table
      - name: customers
        identifier: jaffle_customers_information_table

Using the source() Macro
To reference a declared source in your SQL model:
select * from {{ source('jaffle_shop', 'orders') }}

'jaffle_shop' is the source name
'orders' is the logical table name (mapped to the actual table using the identifier field)

You do not reference the identifier directly in your code — dbt handles that mapping.

Where Do Sources Appear in the DAG?
Sources appear all the way to the left — they are the starting nodes in the dbt DAG (Directed Acyclic Graph).
They represent raw, untransformed data that other models build on.

Common Errors with Sources
Use the name field, not the identifier, in the source() macro.
Do not include schema or database names in the source() macro.
YAML is indentation-sensitive. Use consistent spacing and avoid mixing tabs and spaces.
Ensure your database and schema fields match your actual warehouse structure.

Tips for Debugging Source Issues
Check that your database, schema, and table names are correct.
Use dbt debug or dbt parse to catch YAML or config issues.
Use an online YAML validator to ensure your file structure is valid.

Folder Structure Tip
It’s common to define sources in files like:

models/staging/jaffle_shop/stg_jaffle_shop.yml

Organizing by source or domain keeps your project maintainable and clear.



