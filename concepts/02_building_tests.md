Building Tests in dbt
In dbt, testing ensures that your data models behave as expected and that the SQL transformations you've written produce clean, reliable outputs. 
Testing plays a key role in validating data quality, debugging issues early, and building stakeholder trust in analytics deliverables.

Types of Tests in dbt
dbt supports two main types of tests:

1) Generic Tests
Defined in YAML files.
Reusable and built-in to dbt.
Applied directly to specific columns.

Common types:
unique: Ensures all values in a column are distinct.
not_null: Ensures no nulls are present.
accepted_values: Restricts a column to specific expected values.
relationships: Ensures referential integrity between models (foreign key points to valid primary key).

2) Singular Tests
Written as custom SQL files.
Designed for specific, one-off data validations.
Fail when the SQL query returns one or more rows (representing violations).

Key Concepts
dbt tests are SELECT queries that return failing rows.
Tests run against materialized models or sources.
You can use command-line options to run all tests or filter by type or target.

Common commands
dbt test                          # Run all tests
dbt test --select source:*        # Run only source tests
dbt test --select test_type:generic
dbt test --select test_type:singular
dbt test --select my_model_name   # Test a specific model

Best Practices
For primary keys, apply both unique and not_null tests.
For foreign keys, use relationships tests to validate referential integrity.
For categorical fields, use accepted_values to limit values to a known list.
Use freshness tests on source tables to ensure timely data ingestion.

What Happens When You Run a Test?
dbt compiles your test into SQL.
The SQL runs against your warehouse.
A test fails if any rows are returned (i.e., data violates the assertion).
dbt does not enforce database constraints; it only checks and reports violations.

Source Freshness Testing
You can configure freshness tests on sources using the loaded_at_field and thresholds for warning and error.

Example:
freshness:
  warn_after: {count: 1, period: day}
  error_after: {count: 7, period: day}

A warning is triggered if the data is between 1 and 7 days old.
An error is triggered if the data is older than 7 days.

Example YAML Configuration:

sources:
  - name: salesforce
    tables:
      - name: accounts
        loaded_at_field: _updated_at
        freshness:
          warn_after: {count: 1, period: day}
          error_after: {count: 7, period: day}
        columns:
          - name: account_id
            tests:
              - unique
              - not_null

This example applies uniqueness and not-null tests to the account_id column, and monitors the freshness of the source data using the _updated_at column.

Common Causes of Test Failures
If your dbt build ran successfully one day but fails tests the next, the likely cause is:
A new unexpected value appeared (breaking an accepted_values test).
The freshness threshold was exceeded.
Failing a uniqueness test typically means a new duplicate was introduced.
