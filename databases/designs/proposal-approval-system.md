# Proposal Approval System

Some workflows require request and approval system before making changes or insertion to the database, e.g. Order changes.

## Schema

The idea is to keep one table for changes and requests. We can also keep them separate for better separation: (A) for log-based changes and (B) for requests/approval flow. However, I prefer to keep them together unless the business requires clean separation of the two.

The changes table will keep metadata such as the status of the request, the reviewer id, versioning, expiration, and type of the change.

```sql
create table orders (
  id integer primary key
  ...order fields...
)

create table order_changes (
  id integer primary key
  mode text -- either 'insert' or 'update'
  status text -- either 'open', 'expired', 'rejected' or 'approved'
  expires_at timestamp
  version integer -- epoch timestamp or app-based versioning
  reviewer_id integer nullable
  order_id integer nullable
  ...order fields...
)
```

## Insertion process - Rejection

1. Create `order_change` record with `mode` set to 'insert'. Status will defaulted to 'open'
1. Reviewer rejects the proposal.
1. The record is updated with `status` set to 'rejected' and 'reviewer_id' set to current reviewer.

## Insertion process - Approval

1. Create `order_change` record with `mode` set to 'insert'. Status will defaulted to 'open'. (Optional) Reserve order id in `order_change` record so that the created order will be automatically tied to its original request record.
1. Reviewer approves the proposal.
1. Create a transaction.
1. The record is updated with `status` set to 'rejected' and 'reviewer_id' set to current reviewer.
1. Insert order record with the order fields in `order_change` record.
1. Commit transaction.

## Update process - Rejection

1. Create `order_change` record with `mode` set to 'update'. Status will defaulted to 'open'. `order_id` set to the updated order id.
1. Reviewer rejects the proposal.
1. The record is updated with `status` set to 'rejected' and 'reviewer_id' set to current reviewer.

## Update process - Approval

1. Create `order_change` record with `mode` set to 'insert'. Status will defaulted to 'open'. (Optional) Reserve order id in `order_change` record so that the created order will be automatically tied to its original request record.
1. Reviewer approves the proposal.
1. Create a transaction.
1. The record is updated with `status` set to 'rejected' and 'reviewer_id' set to current reviewer.
1. Update order record with the order fields in `order_change` record.
1. Commit transaction.
