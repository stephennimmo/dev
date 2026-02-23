# Postgres
- The table names should be singular, not plural. Its the 'customer' table, not 'customers'.
- The primary key should be the table name combined with 'Id' as a suffix. 'customer' has a 'customerId'. 'order' has an 'orderId'.
- Always use BIGSERIAL and BIGINT for PRIMARY KEY and FOREIGN KEY fields. Do not use UUID.
- Always set the starting sequence values - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- Generated sequences from BIGSERIAL and SERIAL fields should also be set to a number to fill all the digits - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- TEXT should be used for alphanumeric data, not VARCHAR.
- Use `created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` and `updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` where it makes sense

## Flyway Database Schema
- Use Flyway for all schema management
    - Test Data for Flyway should be placed in a file named `src/main/resources/db/testdata/V999__testdata.sql`
    - dev and test profiles should include the `db/testdata` folder with the `locations: db/migration,db/testdata` but not prod
- Test data should be generated using primary key values lower than the starting sequence to easily be able to delete them
