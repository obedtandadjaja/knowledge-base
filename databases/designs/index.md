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

