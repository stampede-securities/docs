# React

Highly recommended to go through the entire [React Quick Start](https://reactjs.org/docs/hello-world.html).

## Essential

* [Introducing JSX](https://reactjs.org/docs/introducing-jsx.html)
* [Components and props](https://reactjs.org/docs/components-and-props.html)
* [State and Lifestyle](https://reactjs.org/docs/state-and-lifecycle.html)
* [**Thinking in react**](https://reactjs.org/docs/thinking-in-react.html)
* [Presentational and Container components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

### Router

* [Basic Components](https://reacttraining.com/react-router/web/guides/basic-components)
* [Quick Start](https://reacttraining.com/react-router/web/guides/quick-start)

### Misc

* [Debugging](https://medium.com/@baphemot/intro-to-debugging-reactjs-applications-67cf7a50b3dd)

## Recommended

* [Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)
* [List and keys](https://reactjs.org/docs/lists-and-keys.html)
* [Forms](https://reactjs.org/docs/forms.html)
* [Handle events](https://reactjs.org/docs/handling-events.html)
* [Lifting state up](https://reactjs.org/docs/lifting-state-up.html)
* [Composition vs inheritance](https://reactjs.org/docs/composition-vs-inheritance.html)

### Router

* [URL Params](https://reacttraining.com/react-router/web/example/url-params)
* [Redirects (Auth)](https://reacttraining.com/react-router/web/example/auth-workflow)
* [Link](https://reacttraining.com/react-router/web/api/Link/to-string)
* [History](https://reacttraining.com/react-router/web/api/history)
* [Location](https://reacttraining.com/react-router/web/api/location)
* [Match](https://reacttraining.com/react-router/web/api/match)

### Etc

* [Use a render prop!](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)
* [React Patterns](https://reactpatterns.com/)

## Resources

* [Chat - Reactiflux](https://www.reactiflux.com/)
* [React/redux links](https://github.com/markerikson/react-redux-links)
* [Reactjs.org Examples](https://reactjs.org/community/examples.html)

## Project Organization

Start with a `containers` folder. Place each top-level view for a page (container) into the folder. Each page should be broken up into components.

If the component will be reused, place this component in a top-level `components` folder.

```
/src/
    containers/
              Page.js
    components/
              ReusableComponent.js
```

If the component will only be used in one page, it should be placed in a `components` folder within the folder for that page.

```
/src/
    containers/
              Page/
                  index.js
                  components/
                            PageSubcomponent.js
```

A container can be loosely defined as any react component which handles state. A container should handle as little presentation as possible.
