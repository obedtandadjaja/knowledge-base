# Stack
Stacks are dynamic sets in which the element from the set is the one most recently inserted: LIFO (Last In First Out). Think of stacks like a stack of dishes where you can only insert at the top and take out elements from the top too.x

The insert operation on a stack is called `PUSH`, and the delete operation is called `POP`.

## Implementation
We can use a simple array to implement a stack.

```python
isStackEmpty(S): # O(1)
  return S.size == 0
  
push(S, x): # O(1)
  S.size += 1
  S[S.size] = x

pop(S): # O(1)
  if isStackEmpty(S):
    error "stack is empty"
  
  S.size -= 1
  return S[S.size + 1]
```

We can also use a doubly linkedlist to implement a stack, in that way we do not have to grow the array when the stack exceeds the array size.
