# redux-devtools-state-store
A persistent storage module for [Redux Devtools](https://github.com/gaearon/redux-devtools) state


This project is currently only abstracting the persistState functionality into a separate module.

## Installation
`npm install --save-dev redux-devtools-state-store`  

## API format  
`redux-devtools-state-store` is a curried function, taking the "Store" as a first argument.  This store _must_ contain the following methods to be used by the persistStore enhancer.  
1. Store.getItem(key)  
2. Store.setItem(key, value)  
3. Store.removeItem(key)  

For instance, localStorage contains all the above 3 methods, and may be input as a state store.


## Examples
+ Example localStorage  

In your `store/configureStore.js` you can add a persistStore enhancer.   This enhancer has been extracted into a separate module taking a backing store with the api mentioned above.
An example of this is below, showing how to pass in the backing store before calling the persistStore enhancer.
  
```  
import { createStore, applyMiddleware, compose } from 'redux';
import rootReducer from '../reducers';
import stateStore from 'redux-devtools-state-store';
import DevTools from '../containers/DevTools';


//Add backing store here!!
//Store must have api in form of - getItem(), setItem, removeItem()
const persistStore = stateStore(localStorage);


const enhancer = compose(
  // Middleware you want to use in development:
  applyMiddleware(d1, d2, d3),
  // Required! Enable Redux DevTools with the monitors you chose
  DevTools.instrument(),
  persistStore(getDebugSessionKey())
);

function getDebugSessionKey() {
  // You can write custom logic here!
  // By default we try to read the key from ?debug_session=<key> in the address bar
  const matches = window.location.href.match(/[?&]debug_session=([^&#]+)\b/);
  return (matches && matches.length > 0)? matches[1] : null;
}

export default function configureStore(initialState) {
  // Note: only Redux >= 3.1.0 supports passing enhancer as third argument.
  // See https://github.com/rackt/redux/releases/tag/v3.1.0
  const store = createStore(rootReducer, initialState, enhancer);

  // Hot reload reducers (requires Webpack or Browserify HMR to be enabled)
  if (module.hot) {
    module.hot.accept('../reducers', () =>
      store.replaceReducer(require('../reducers')/*.default if you use Babel 6+ */)
    );
  }

  return store;
}  
```



## Contributions  
I'm very open to making changes, adding features, and all ideas! 

