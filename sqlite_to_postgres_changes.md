# Entity Relation Diagram
![](ER_the_hospital.png)

# Creating wikibooks hospital database and tables for postgres
- In case of postgres, use 't' or 'f' for boolean instead of 1 and 0 for sqlite3.
- Use data type `date (2020-01-06) or timestamp (2021-01-06 23:32)` in postgres instead of `datetime` in sqlite3.
- The column name `End` is reserved, use it with quotes `"End"`.
- Some of the constraints in tables are not working, simply remove those constraints.