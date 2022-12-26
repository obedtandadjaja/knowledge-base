# Database Design Ideas

## About

Database schema ideas for some projects that I have worked on in the past. You don't have to agree with the approaches. This collection of database designs serve as a reminder for me of what I've done in the past and the lessons I've learned along the way.

## Postgres

* Use `identity` columns instead of `serial`
* Use `generated columns` instead of triggers if you need a column with computed values within the same table
* Use `text` always. `varchar` does not provide any performance difference
* `tstzrange` and `tstzmultirange` for time-based applications
* Use `decimal(13,4)` for currencies. Compliant with Generally Accepted Accounting Principles (GAAP)

## Principles

* ID over UUID for small scale applications
  * Less load on memory
  * User-friendly for referencing entities, e.g. Order #111
  * Serial and can be used to sort without help of datetimes
* UUID for larger scale applications where multiple shards/leaders are present
* `created_at`, `updated_at`, and `deleted_at` are a must
  * Should be maintained by ORMs
* Always comply to normal forms. Shoot for 4NF. Know also when to break them
  * Denormalization in favor of speed
* Try to avoid STI unless the ORM that you are using has great support for it, e.g. Rails
* Always use international standards or w3c standards for codes, e.g. language code, country code
* All date-related columns should be saved in UTC
* Try to find what you are looking for in Postgres first, e.g. full-text search or geospatial search. If there is something you can quickly leverage then try to use that first before investing more time to build something else (especially at a very early startup)

## Schema design steps

1. Know the problem
2. Know how the problem could scale. What other potential things will the business tackle in the future?
3. Does it contain financial data? More likely than not you will need ACID properties then
4. How big is the scale of the data and does it need immediate considerations for sharding and horizontal scaling? Decide on relational vs. nosql
5. Model the nouns of the business to database entities
6. Determine relationship between entities
7. Double check on analytics and ease of data querying
8. (Optional, but more important than most people think) Double check with product on the entity namings. Having the same lingo between product and tech will prevent a lot of confusions down the line
9. (If applicable) Plan out migration process, views/materialized views, triggers, constraints, etc.

## Books / Readings

* The Data Model Resource Book, Vol. 3: Universal Patterns for Data Modeling
* https://www.postgresql.org/docs/
