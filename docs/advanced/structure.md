# Structure

You can structure your application however you want, but there is a recommended approach based on common experiences building Cerebral applications.

## File structure

```js
src/
  main/
    modules/
      ...
    actions.js
    factories.js
    sequences.js
    providers.js
    computed.js
    reactions.js
    errors.js
    index.js
  controller.js
```

This structure favors a single file for each type of composable piece of logic. The root module is named **main** and will hold submodules in the **modules** folder, where each module has the same structure. You will of course not create all these files for every module, only those needed.

## Actions

```js
import { state } from 'cerebral'

export function actionA({ store }) {
  store.set(state.foo, 'bar')
}

export function actionB({ store }) {
  store.set(state.foo, 'bar')
}
```

You export multiple actions from each modules *actions.js* file.

If you prefer arrow functions, you can write:

```js
import { state } from 'cerebral'

export const actionA = ({ store }) => store.set(state.foo, 'bar')

export const actionB = ({ store }) => store.set(path.foo, 'bar')
```

## Factories

Factories are similar to actions:

```js
// Normal function
export function setLoadingApp (isLoading) {
  return function setLoadingApp({ store }) {
    store.set(state.isLoading, isLoading)
  }
}

// Arrow function
// We return a named function for debugging purposes
export const setLoadingApp = (isLoading) =>
  function setLoadingApp({ store }) {
    store.set(state.isLoading, isLoading)
  }
```

## Sequences

You import all actions and factories into the *sequences.js* file. This will give you autosuggestions on available actions and factories. You can combine this with factories from Cerebral:

```js
import { set } from 'cerebral/factories'
import { state } from 'cerebral'
import * as actions from './actions'
import * as factories from './factories'

export const initialize = [
  factories.setLoadingApp(true),
  actions.getUser,
  actions.setUser,
  set(state.isLoading, false)
]
```

## Modules

You import all your files into the *index.js* file, attaching them like this:

```js
import { Module } from 'cerebral'
import * as sequences from './sequences'
import * as errors from './errors'
import * as computed from './computeds'
import * as providers from './providers'
import * as reactions from './reactions'

export default Module({
  state: {
    isLoading: false
  },
  sequences,
  computed,
  reactions,
  providers,
  catch: [[errors.AuthError, sequences.catchAuthError]]
})
```
