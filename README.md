# redux-act-async

Create async actions based on [redux-act](https://github.com/pauldijou/redux-act)

## Install

```bash
npm install redux-act-async --save
```
## Badges

[![Build Status](https://travis-ci.org/FredericHeem/redux-act-async.svg?branch=master)](https://travis-ci.org/FredericHeem/redux-act-async)

## Usage


```js

import thunk from 'redux-thunk'
import {createStore, applyMiddleware} from 'redux';
import thunkMiddleware from 'redux-thunk';
import {createReducer} from 'redux-act';
import {createActionAsync} from 'redux-act-async';

// The async api to call, must be a function that return a promise
let user = {id: 8};
function apiOk(){
  return Promise.resolve(user);
}

// createActionAsync will create 3 synchronous action creators: login.request, login.ok and login.error
const login = createActionAsync('LOGIN', apiOk);

const initialState = {
  authenticated: false,
};

let reducer = createReducer({
    [login.request]: (state, payload) => ({
        ...state,
        request: payload,
        loading: true,
        error: null
    }),
    [login.ok]: (state, payload) => ({
        ...state,
        loading: false,
        data: payload.response
    }),
    [login.error]: (state, payload) => ({
        ...state,
        loading: false,
        error: payload.error
    }),
}, initialState);

const store = createStore(reducer, applyMiddleware(thunk));

let run = login({username:'lolo', password: 'password'});

await store.dispatch(run);

```

## Legacy redux

In a nutshell, the following code:

```js
const login = createActionAsync('LOGIN', api);
```

is equivalent to:

```js
const LOGIN_REQUEST = 'LOGIN_REQUEST'
const LOGIN_OK = 'LOGIN_OK'
const LOGIN_ERROR = 'LOGIN_ERROR'

const loginRequest = (value) => ({
  type: LOGIN_REQUEST,
  payload: value
})

const loginOk = (value) => ({
  type: LOGIN_OK,
  payload: value
})

const loginError = (value) => ({
  type: LOGIN_ERROR,
  payload: value
})

export const login = (payload) => {
  return (dispatch) => {
    dispatch(loginRequest(payload));
    return api(payload)
    .then(res => {
      dispatch(loginOk(res))
    })
    .catch(err => {
      dispatch(loginError(err))
      throw err;
    })
}
```
