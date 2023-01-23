# React Error Boundary

Error boundaries are React components that catch Javascript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed.

Error boundaries do not catch errors for:
- Event handlers
- Async code
- Server side rendering
- Errors thrown in the error boundary itself

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}

<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

The granularity of error boundaries is up to developers. It can be at the top-level route components to display "Something went wrong" or at individual widgets to protect users from crashing the rest of the application.

Note that as of React 16, errors that were not caught by any error boundary will result in unmounting of the whole React component tree.