# Code Splitting and React Suspense

## React Suspense

New top-level API introduced in React 18: https://reactjs.org/docs/react-api.html#reactsuspense.

Lets developers to specify loading indicator in case some components are not yet ready to render.

```
// This component is loaded dynamically
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // Displays <Spinner> until OtherComponent loads
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

This change is introduced in conjunction with the new code-splitting guide. The new code-splitting guidance is an effort to reduce bundle size of React apps. Code splitting will help lazy load just the things that the user needs, which can dramatically improve the performance of your app.

## Code Splitting

### import()

Dynamic import to inline import logic only when you actually need it.

```
const calculateTotals = (data) => {
  import("./math").then(math => math.sum(data))
}
```

### React.lazy

Lets you render a dynamic import as a regular component.

```
const OtherComponent = React.lazy(() => import('./OtherComponent'))
```

The lazy component must be rendered inside `Suspense` component, which allows us to show some fallback content while the component loads.

You can have multiple lazily loaded components.

```
const Comments = React.lazy(() => import('./Comments'));
const Photos = React.lazy(() => import('./Photos'));

function MyComponent() {
  const [tab, setTab] = React.useState('photos');
  
  function handleTabSelect(tab) {
    setTab(tab);
  };

  return (
    <div>
      <Tabs onTabSelect={handleTabSelect} />
      <Suspense fallback={<Glimmer />}>
        {tab === 'photos' ? <Photos /> : <Comments />}
      </Suspense>
    </div>
  );
}
```

If you choose to not switch tabs while the component loads, you can use the `startTransition` API.

```
function handleTabSelect(tab) {
  startTransition(() => {
    setTab(tab);
  });
}
```