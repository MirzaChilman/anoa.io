---
id: working-with-a-project-store
title: Store
---

Store in here is a [React Redux](https://react-redux.js.org) Store. You use this command to do something with React Redux:

```bash
$ anoa store
```

or

```bash
$ anoa s
```

## Create new reducer

Executing command above you'll get several option to choose:

```bash
$ anoa store
? What would you like to do with store?
> Create new reducer
  Add new fields into existing state
  Add new action creator
  Update AppStore
```

Let start by choosing **Create new reducer**, and specify the Reducer name with `todo` for example:

```bash
$ anoa store
? What would you like to do with store? Create new reducer
? Reducer name: todo
```

Now specify the state. In this example I'd like to have `tasks` and `completedTasks` which are array of string so I just do this:

```bash
$ anoa store
? What would you like to do with store? Create new reducer
? Reducer name: todo
? State fields (separated with space, eg: foo:string='some value' bar:number=26), or leave it blank, we'll create an example for you: tasks:string[] completedTasks:string[]
```

For the very first time you add store in your application, Anoa will installs new packages from npm which are :

```
react-redux
redux
redux-thunk
```

and also

```
@types/react-redux
```

for typings support for Typescript. Just wait for a moment...

```bash
| Adding required packages...
```

and done:

```bash
$ anoa store
? What would you like to do with store? Create new reducer
? Reducer name: todo
? State fields (separated with space, eg: foo:string='some value' bar:number=26), or leave it blank, we'll create an example for you: tasks:string[] completedTasks:string[]
√ Required packages added.
√ Required packages added.
New reducer was successfully created on src/store/reducers/todo/index.ts
```

It says that `New reducer was successfully created on src/store/reducers/todo/index.ts` you can check this file to see your new reducer.

### index.ts

```typescript
import { Reducer } from 'redux'
import TodoActions from './actions'
import TodoState from './state'

const TodoReducer: Reducer<TodoState, TodoActions> = (
  state = {
    tasks: [],
    completedTasks: []
  },
  action
) => {
  switch (action.type) {
    case 'TODO/TASKS':
      return { ...state, tasks: action.payload }

    case 'TODO/COMPLETED_TASKS':
      return { ...state, completedTasks: action.payload }

    default:
      return state
  }
}

export default TodoReducer
```

There are also two files generated as well which are `actions.ts` and `state.ts`.

### actions.ts

```typescript
type TodoActions =
  | {
      type: 'TODO/TASKS'
      payload: string[]
    }
  | {
      type: 'TODO/COMPLETED_TASKS'
      payload: string[]
    }

export default TodoActions
```

### state.ts

```typescript
export default interface TodoState {
  tasks: string[]
  completedTasks: string[]
}
```

Feel free to modify those files as if needed.

## AppStore

Beside of generating those reducer files, at the very first time you create a reducer, this command will also generates `src/store/reducers/index.ts`:

```typescript
import { combineReducers } from 'redux'
import TodoReducer from './todo'
import TodoActions from './todo/actions'

export const reducers = combineReducers({
  todo: TodoReducer
})

export type AppRootActions = TodoActions

export type AppRootState = ReturnType<typeof reducers>
```

Where `AppRootActions` and `AppRootState` automatically generated based on reducers you have.
You should not touch this file since this file will always get modified when you add or modify your reducers
by using anoa's commands.

And also `src/store/index.ts` :

```typescript
import { ReduxStore } from 'anoa'
import { ThunkAction } from 'redux-thunk'
import { AppRootActions, AppRootState, reducers } from './reducers'

export const AppStore = new ReduxStore<AppRootState, AppRootActions>(reducers)

export type AppThunkAction<TResult = void> = ThunkAction<
  TResult,
  AppRootState,
  undefined,
  AppRootActions
>
```

Where `AppStore` is our redux store. If you check on `App.tsx`, the `mainRender` function is also changed:

```typescript
renderMain() {
  return (
    <AppStore.Provider>
      <MainScreen />
    </AppStore.Provider>
  )
}
```

That now `<MainScreen/>` component wrapped with `AppStore.Provider`. And in this `App.tsx`, if check on `prepare()` method:

```typescript
async prepare(): Promise<void> {
  await Asset.loadAsync(require('../assets/logo.png'))
  await AppStore.init()
}
```

On the last line `await AppStore.init()` was added to initialize our redux store.

## Add new fields into existing state

Rather than editing reducer files, if you only want to add more fields in your existing state, you can use this command bellow:

```bash
$ anoa store
? What would you like to do with store?
  Create new reducer
> Add new fields into existing state
  Add new action creator
  Update AppStore
  Connect store to view
```

> Notice there is also new command called **Connect store to view**. I'll explain this later on the next chapter.

OK, now press ENTER and you'll select the **state** that you want to modify:

```bash
$ anoa store
? What would you like to do with store? Add new fields into existing state
? Select state:
> todo
```

After the state is selected, let say I want to add new field named `importantTasks` and the type is array of string too, then on the next step:

```bash
$ anoa store
? What would you like to do with store? Add new fields into existing state
? Select state: todo
? Field(s) (separated with space, eg: foo:string='some value' bar:number=26): importantTasks:string[]
```

Press ENTER and done:

```
New field(s) was successfully added to TodoState on src/store/reducers/todo/index.ts
```

You can check the changes being made on both of `index.ts`, `action.ts` and `state.ts` file.

## Add new action creator

This command will generates [redux-thunk](https://github.com/reduxjs/redux-thunk) action creator for you:

```bash
$ anoa store
? What would you like to do with store?
  Create new reducer
  Add new fields into existing state
> Add new action creator
  Update AppStore
  Connect store to view
```

For example I would like to create action creator named `addTask`:

```bash
$ anoa store
? What would you like to do with store? Add new action creator
? Action name addTask
```

And select an action type from our `TodoActions`:

```bash
$ anoa store
? What would you like to do with store? Add new action creator
? Action name addTask
? Action type (Leave blank -- we'll let you to pick it):
? Select action from
> TodoActions
```

Since this action will mutate the `tasks` state from `todo` reducer, then let's choose `TODO/TASKS` action type:

```bash
$ anoa store
? What would you like to do with store? Add new action creator
? Action name addTask
? Action type (Leave blank -- we'll let you to pick it):
? Select action from TodoActions
? Select type
> TODO/TASKS
  TODO/COMPLETED_TASKS
  TODO/IMPORTANT_TASKS
```

Now enter the file name. This file will be stored under `src/store/actions` directory. Let's give it name with `todo`:

```bash
$ anoa store
? What would you like to do with store? Add new action creator
? Action name addTask
? Action type (Leave blank -- we'll let you to pick it):
? Select action from TodoActions
? Select type TODO/TASKS
? Save this action to file name: todo
```

And done!

```bash
New action was successfully created on src/store/actions/todo.ts
```

Here's our new redux-thunk action creator:

```typescript
import { AppThunkAction } from '..'

export function addTaskAction(payload: string[]): AppThunkAction {
  return dispatch => {
    dispatch({ type: 'TODO/TASKS', payload })
  }
}
```

This command simply transforms the state's field(s) of action type as a payload parameter(s).
You can modify this action creator to fullfill your needs, for example:

```typescript
export function addTaskAction(task: string): AppThunkAction {
  return async (dispatch, getState) => {
    dispatch({
      type: 'TODO/TASKS',
      payload: [...getState().todo.tasks, task]
    })
  }
}
```

And create new action creator on the same file or new file.

## Update AppStore

You will need to use this command when you make changes on your reducers manually.

```bash
$ anoa store
? What would you like to do with store?
  Create new reducer
  Add new fields into existing state
  Add new action creator
> Update AppStore
  Connect store to view
```

This action will update the application root state according to those changes.
For instance if you delete one of your reducer, rename it or make a new one by coding it manually,
then you need to call this command so it will update the `src/store/reducers/index.ts` file where `AppRootActions` and `AppRootState` generated.

```bash
$ anoa store
? What would you like to do with store? Update AppStore
Store was successfully updated.
```

## Connect store to view

Here's the nice part! That anoa can modify your existings views (screens or components) code to connect it to your reducers.

```bash
$ anoa store
? What would you like to do with store?
  Create new reducer
  Add new fields into existing state
  Add new action creator
  Update AppStore
> Connect store to view
```

Select the kind of view:

```bash
$ anoa store
? What would you like to do with store? Connect store to view
? What kind of view would you like to connect to theme? (Use arrow keys)
  Component
> Screen
```

In this example I selected **Screen**, now I can select the screen that I want to connect to (**TodoListScreen** for example):

```bash
$ anoa store
? What would you like to do with store? Connect store to view
? What kind of view would you like to connect to theme? Screen
? Select the Screen you want to connect to
  CompletedTaskListScreen [completed-task-list]
  MainScreen [main]
> TodoListScreen [todo-list]
```

Now let's select state to map:

```bash
$ anoa store
? What would you like to do with store? Connect store to view
? What kind of view would you like to connect to theme? Screen
? Select the Screen you want to connect to TodoListScreen [todo-list]
? Select state(s) you want to map
 ( ) all
 ( ) none
 ────────
 ( ) TodoState
>  √ todo.tasks
   [ ] todo.completedTasks
   [ ] todo.importantTasks
```

Following by selecting action to map:

```bash
$ anoa store
? What would you like to do with store? Connect store to view
? What kind of view would you like to connect to theme? Screen
? Select the Screen you want to connect to TodoListScreen [todo-list]
? Select state(s) you want to map todo.tasks
? Select action(s) you want to map
>(*) addTaskAction({payload:string[]})
```

By the way you can map the **state(s)** or **action(s)** only -- or both of it based on your needs.
And done:

```bash
? What would you like to do with store? Connect store to view
? What kind of view would you like to connect to theme? Screen
? Select the Screen you want to connect to TodoListScreen [todo-list]
? Select state(s) you want to map todo.tasks
? Select action(s) you want to map addTaskAction({payload:string[]})
Store was successfully connected to TodoListScreen screen on src/views/screens/todo-list/index.tsx
```

You can go to your view file, that now is decorated with `@AppStore.withStoreClass` class decorator:

```typescript
import React from 'react'
import { Text, View } from 'react-native'
import { AppStore } from '../../../store'
import { addTaskAction } from '../../../store/actions/todo'
import {
  TodoListScreenActionProps,
  TodoListScreenProps,
  TodoListScreenStateProps
} from './props'

@AppStore.withStoreClass<TodoListScreenStateProps, TodoListScreenActionProps>(
  state => ({ todoTasks: state.todo.tasks }),
  dispatch => ({ addTask: payload => dispatch(addTaskAction(payload)) })
)
export class TodoListScreen extends React.Component<TodoListScreenProps> {
  constructor(props: TodoListScreenProps) {
    super(props)
  }

  public render() {
    return (
      <View>
        <Text>TodoListScreen</Text>
      </View>
    )
  }
}
```

And also your props also extended to state(s) and action(s) you have picked:

```typescript
export interface TodoListScreenProps
  extends Partial<TodoListScreenStateProps>,
    Partial<TodoListScreenActionProps> {}

export interface TodoListScreenStateProps {
  todoTasks: string[]
}

export interface TodoListScreenActionProps {
  addTask: (payload: string[]) => void
}
```
