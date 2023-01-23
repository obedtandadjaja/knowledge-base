# React Query

Practical react query (BEST PRACTICES): https://tkdodo.eu/blog/practical-react-query

## Our Problem - How does this help me?

Right now all of my FE projects rely on Redux + Axios for handling API calls. The actual API call is handled by Axios but retained by Redux to be accessed by multiple React components. Potential issues:

1. Redux saving states for longer than necessary. Pages that should be loaded with a fresh state, end up accessing old states.
1. Redux boilerplate. There is more code to write. It is easier to call the API from the component directly. The cons of this approach is that we need to pass the data down to the leaf components.
1. Calling the same API multiple times. In the Redux state, the response of the API is saved in a singular state. Calling the same API multiple times result in overrides and not being able to retain multiple states of the same response.

## Motivation

Opinionated way of fetching and updating data for React apps. Helps solve the above issues.

Out of the box functionalities for us to use:

1. Caching
1. Deduping requests
1. Update out of date data in the background
1. Lazy loading and paginations
1. Managing memory and garbage collection
1. Structural sharing

## Example

```
 import {
   useQuery,
   useMutation,
   useQueryClient,
   QueryClient,
   QueryClientProvider,
 } from 'react-query'
 import { getTodos, postTodo } from '../my-api'
 
 // Create a client
 const queryClient = new QueryClient()
 
 function App() {
   return (
     // Provide the client to your App
     <QueryClientProvider client={queryClient}>
       <Todos />
     </QueryClientProvider>
   )
 }
 
 function Todos() {
   // Access the client
   const queryClient = useQueryClient()
 
   // Queries
   const query = useQuery('todos', getTodos)
 
   // Mutations
   const mutation = useMutation(postTodo, {
     onSuccess: () => {
       // Invalidate and refetch
       queryClient.invalidateQueries('todos')
     },
   })
 
   return (
     <div>
       <ul>
         {query.data.map(todo => (
           <li key={todo.id}>{todo.title}</li>
         ))}
       </ul>
 
       <button
         onClick={() => {
           mutation.mutate({
             id: Date.now(),
             title: 'Do Laundry',
           })
         }}
       >
         Add Todo
       </button>
     </div>
   )
```

## Details

### Query

To subscribe to a query, call `useQuery` with **unique** key and function that resolves the data.

```
const result = useQuery('todos', fetchTodoList)

// Available states:
// - result.error: the error object
// - result.data: the response from API
// - result.isFetching: whether the query is currently fetching data
// - result.isLoading
// - result.isError
// - result.isSuccess
// - result.isIdle

// Most common
const { isLoading, isError, data, error } = useQuery('todos', fetchTodoList)
```

#### Query Keys

Query keys do not have to be a string. It can be any type as long as itis serializable.

```
// If the query depends on a variable, include it in the query key
useQuery(['todos', todoId], ...)

// If the query depends on a set of params, include it as well
useQuery(['todos', todoId, {page, status, otherParams}], ...)
```

According to https://tkdodo.eu/blog/effective-react-query-keys, **always use Array keys**. Structure it from most generic to most specific with as many levels of granularity in between.

```
['todos', 'list', { filters: 'all' }]
['todos', 'list', { filters: 'done' }]
['todos', 'detail', 1]
['todos', 'detail', 2]
```

The above configuration allows for updating mutation responses

```
function useUpdateTitle() {
  return useMutation({
    mutationFn: updateTitle,
    onSuccess: (newTodo) => {
      // ✅ update the todo detail
      queryClient.setQueryData(['todos', 'detail', newTodo.id], newTodo)

      // ✅ update all the lists that contain this todo
      queryClient.setQueriesData(['todos', 'list'], (previous) =>
        previous.map((todo) => (todo.id === newTodo.id ? newtodo : todo))
      )
    },
  })
}
```

To make the query less error-prone (due to manual declaration), we can use query key factory

```
const todoKeys = {
  all: ['todos'] as const,
  lists: () => [...todoKeys.all, 'list'] as const,
  list: (filters: string) => [...todoKeys.lists(), { filters }] as const,
  details: () => [...todoKeys.all, 'detail'] as const,
  detail: (id: number) => [...todoKeys.details(), id] as const,
}
```

#### Dynamic parallel queries

Make use of `useQueries`

```
const userQueries = useQueries(
    users.map(user => {
    return {
        queryKey: ['user', user.id],
        queryFn: () => fetchUserById(user.id),
    }
    })
)
```

#### Dependent/Serial queries

Make use of the `enabled` option

```
 // Get the user
 const { data: user } = useQuery(['user', email], getUserByEmail)
 
 const userId = user?.id
 
 // Then get the user's projects
 const { isIdle, data: projects } = useQuery(
   ['projects', userId],
   getProjectsByUser,
   {
     // The query will not execute until the userId exists
     enabled: !!userId,
   }
 )
```

#### Disable automatic query

Configure `enabled: false` and use `refetch()` to trigger query.

#### Paginated/Lagged query

[Less Preferred] Configure `keepPreviousData: true`.

```
const [page, setPage] = React.useState(0)
const fetchProjects = (page = 0) => fetch('/api/projects?page=' + page).then((res) => res.json())

const { data, isPreviousData } = useQuery(['projects', page], () => fetchProjects(page), { keepPreviousData : true })
```

[Preferred] Infinite queries

- `data` is now an object containing infinite query data
- `data.pages` array containing fetched pages
- `data.pageParams` array combining the page params used to fetch pages
- `fetchNextPage` and `fetchPreviousPage` functions
- `getNextPageParam` and `getPreviousPageParam` determine if there is more data to load
- `isFetchingNextPage` and `isFetchingPreviousPage` booleans

```
const fetchProjects = ({ pageParam = 0 }) =>
    fetch('/api/projects?cursor=' + pageParam)

const {
    data,
    error,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    status,
} = useInfiniteQuery('projects', fetchProjects, {
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
})
```

Reverse order?

```
useInfiniteQuery('projects', fetchProjects, {
   select: data => ({
     pages: [...data.pages].reverse(),
     pageParams: [...data.pageParams].reverse(),
   }),
 })
```

#### Prefetching

Prefetch data from another component

```
 const prefetchTodos = async () => {
   // The results of this query will be cached like a normal query
   await queryClient.prefetchQuery('todos', fetchTodos, {staleTime: 5000})
 }
```

#### Cancelation

```
const query = useQuery('todos', ({ signal }) =>
   axios.get('/todos', {
     // Pass the signal to `axios`
     signal,
   })
 )
```

### Mutations

For create/update/delete actions. `useMutation` hook.

```
const mutation = useMutation(postTodo, {
     onSuccess: (data) => {
       // Invalidate and refetch
       queryClient.invalidateQueries('todos')

       // Or update detail
       queryClient.setQueryData('todos', data)
     },
   })
```

See https://tkdodo.eu/blog/react-query-and-forms for how mutations can be used in forms.

#### Side-effects

```
useMutation(addTodo, {
   onMutate: variables => {
     return { id: 1 }
   },
   onError: (error, variables, context) => {
     // An error happened!
     console.log(`rolling back optimistic update with id ${context.id}`)
   },
   onSuccess: (data, variables, context) => {
     // Boom baby!
   },
   onSettled: (data, error, variables, context) => {
     // Error or success... doesn't matter!
   },
 })
```

Note that we have a separate side-effects on the `mutate` function. It is best to keep the two side-effects separate, i.e. the `useMutation` declaration should be declared as a separate hook. In this way, the side-effects related to query logic will be contained in the custom hook, while UI related actions are contained in the `mutate` call in the component.

```
const useUpdateTodo = () =>
  useMutation({
    mutationFn: updateTodo,
    // ✅ always invalidate the todo list
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['todos', 'list'] })
    },
  })

// in the component

const updateTodo = useUpdateTodo()
updateTodo.mutate(
  { title: 'newTitle' },
  // ✅ only redirect if we're still on the detail page
  // when the mutation finishes
  { onSuccess: () => history.push('/todos') }
)
```

#### Promises

```
const mutation = useMutation(addTodo)
const todo = await mutation.mutateAsync(todo)
```

#### Retry

By default mutations will not retry on error.

```
const mutation = useMutation(addTodo, { retry: 3 })
```

#### Optimistic updates

Add your mutation to the list before the update went through

```
useMutation(updateTodo, {
   // When mutate is called:
   onMutate: async newTodo => {
     // Cancel any outgoing refetches (so they don't overwrite our optimistic update)
     await queryClient.cancelQueries('todos')
 
     // Snapshot the previous value
     const previousTodos = queryClient.getQueryData('todos')
 
     // Optimistically update to the new value
     queryClient.setQueryData('todos', old => [...old, newTodo])
 
     // Return a context object with the snapshotted value
     return { previousTodos }
   },
   // If the mutation fails, use the context returned from onMutate to roll back
   onError: (err, newTodo, context) => {
     queryClient.setQueryData('todos', context.previousTodos)
   },
   // Always refetch after error or success:
   onSettled: () => {
     queryClient.invalidateQueries('todos')
   },
 })
```

## More info

- By default `useQuery` consider cached data as stale. We can specify `staleTime` to not refetch data as often.
- Stale queries are refetched in the background when:
  - New instances of the query mount
  - Window is refocused (this will happen a lot during development. Modify this behavior by changing `refetchOnWindowFocus`)
  - Network is reconnected
  - Query configured with a refetch interval
- Query results that do not have any active instances of `useQuery` will be labeled as inactive.
- Inactive queries are garbage collected after 5 minutes. (`cacheTime` to change).
- Queries that fail are silently retried 3 times, with exponential backoff delay before capturing and displaying an error to the UI.
- Query results are structurally shared to detect if data has actually changed. If not, the data reference remained unchanged. `useMemo` and `useCallback` will not be invoked. This feature only works for JSON.

## Next.js SSR Support

https://react-query-v3.tanstack.com/guides/ssr