# User Defined Order

Some applications, such as todo lists, need to maintain a user-defined order of items.

## (Naive) Approach 1: Integer Position Column

Add an auto-incrementing integer column to track each item's position. Very simple but when the user reorders an item to the start of the list, the other items will need to shift down by 1. The problem would be problematic for very large lists.

## Approach 2: Adding Gaps

Add 2^16 per item. This will give us 16 chances to add other items between 2 items. If the 16 is hit then we have to go back to approach 1 and update the gaps back to 2^16.

## Approach 3: Decimal Position

Do `(a_pos + b_pos)/2` when inserting an item between a and b. Danger is on the limited precision of floating point numbers. Most will fail on the 36th iteration.

## Approach 4: Arbitrary Precision

Use decimal or numeric to store exact precision values. However, after some iterations the number will be very large and heavy.

## Approach 5: True Fractions [Recommended]

Instead of keeping floating point numbers, just keep the fraction, e.g. 5/2. `pg_rational` has support for this (https://github.com/begriffs/pg_rational). See Stern-Brocot tree for how fractions can be used for traversal in O(logN) time.

Beware of pathological paths. When going down the middle of the tree (L, R, L, R, L), the denominators increases in a Fibonacci sequence: 1/1, 1/2, 2/3, 3/5, 5/8. The 47th Fibonacci number is too big to fit in a 32-bit integer.

## Approach 6: Order Array [Postgres Only - Recommended]

Postgres supports array columns, instead of creating a new column for positions. Have the parent entity to hold a new column called `item_positions` of type `ARRAY(INTEGER)`. The array will hold a list of item ids in order.

Triggers or application logic can ensure that the array only holds ids of items that still exist.
