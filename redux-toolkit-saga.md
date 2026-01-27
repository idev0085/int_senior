# Redux Toolkit (RTK) & Redux Saga - Complete Guide

## Redux Toolkit - Modern Redux Development

**What is Redux Toolkit?**

Redux Toolkit is the official recommended way to write Redux. It reduces boilerplate and provides best practices out of the box.

**Key Features:**
- `createSlice`: Combines actions and reducers
- `createAsyncThunk`: Handles async logic
- Immer.js integrated: Allows "mutating" state
- Redux DevTools: Built-in time-travel debugging

**Complete RTK Setup:**

```javascript
// 1. Create a slice
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (userId, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      if (!response.ok) throw new Error('Failed to fetch');
      return response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

const userSlice = createSlice({
  name: 'user',
  initialState: {
    data: null,
    loading: false,
    error: null,
  },
  reducers: {
    clearUser: (state) => {
      state.data = null;
      state.error = null;
    },
    updateUserName: (state, action) => {
      if (state.data) {
        state.data.name = action.payload;
      }
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.loading = false;
        state.data = action.payload;
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.loading = false;
        state.error = action.payload;
      });
  },
});

export const { clearUser, updateUserName } = userSlice.actions;
export default userSlice.reducer;

// 2. Create store
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';
import productsReducer from './productsSlice';

export const store = configureStore({
  reducer: {
    user: userReducer,
    products: productsReducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: ['user/fetchUser/fulfilled'],
      },
    }).concat(customMiddleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

// 3. Setup app with provider
import { Provider } from 'react-redux';

function App() {
  return (
    <Provider store={store}>
      <MainApp />
    </Provider>
  );
}

// 4. Use in component
import { useDispatch, useSelector } from 'react-redux';

function UserProfile({ userId }) {
  const dispatch = useDispatch();
  const { data, loading, error } = useSelector(state => state.user);
  
  useEffect(() => {
    dispatch(fetchUser(userId));
  }, [userId, dispatch]);
  
  return (
    <div>
      {loading && <p>Loading...</p>}
      {error && <p>Error: {error}</p>}
      {data && <p>Welcome, {data.name}!</p>}
    </div>
  );
}
```

**RTK Query - Data Fetching:**

```javascript
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const productsApi = createApi({
  reducerPath: 'productsApi',
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  endpoints: (builder) => ({
    // GET request
    getProducts: builder.query({
      query: (filters) => ({
        url: '/products',
        params: filters,
      }),
      transformResponse: (response) => response.data,
      // Refetch every 30 seconds
      keepUnusedDataFor: 30,
    }),
    
    // POST request
    createProduct: builder.mutation({
      query: (product) => ({
        url: '/products',
        method: 'POST',
        body: product,
      }),
      // Invalidate cache
      invalidatesTags: ['Products'],
    }),
    
    // PUT request
    updateProduct: builder.mutation({
      query: ({ id, ...patch }) => ({
        url: `/products/${id}`,
        method: 'PUT',
        body: patch,
      }),
      invalidatesTags: (result, error, { id }) => [
        { type: 'Products', id },
      ],
    }),
    
    // DELETE request
    deleteProduct: builder.mutation({
      query: (id) => ({
        url: `/products/${id}`,
        method: 'DELETE',
      }),
      invalidatesTags: ['Products'],
    }),
  }),
});

export const {
  useGetProductsQuery,
  useCreateProductMutation,
  useUpdateProductMutation,
  useDeleteProductMutation,
} = productsApi;

// Usage in component
function ProductsList() {
  const { data: products, isLoading, error } = useGetProductsQuery({ page: 1 });
  const [createProduct] = useCreateProductMutation();
  
  const handleCreate = async (product) => {
    try {
      const result = await createProduct(product).unwrap();
      console.log('Created:', result);
    } catch (error) {
      console.error('Error:', error);
    }
  };
  
  return (
    <div>
      {isLoading && <p>Loading...</p>}
      {error && <p>Error loading products</p>}
      {products?.map(p => (
        <div key={p.id}>{p.name}</div>
      ))}
    </div>
  );
}
```

---

## Redux Saga - Handling Side Effects

**What is Redux Saga?**

Redux Saga is middleware for managing side effects (API calls, timers, etc.) in Redux. It uses ES6 generators for cleaner async code.

**Why use Redux Saga?**

| Feature | Redux Thunk | Redux Saga |
|---------|------------|-----------|
| Syntax | Functions | Generators |
| Side effects | Direct | Declarative |
| Testing | Functions | Pure functions |
| Complex flows | Chains | Watchers |
| Concurrency | Manual | Built-in |

**Core Concepts:**

```javascript
import { call, put, take, fork, select } from 'redux-saga/effects';
import { productsAPI } from './api';

// 1. Worker Saga - Does the actual work
function* fetchProductsSaga(action) {
  try {
    // call = execute function (like async/await)
    const products = yield call(productsAPI.getAll, action.payload.page);
    
    // put = dispatch action
    yield put(fetchProductsSuccess(products));
  } catch (error) {
    yield put(fetchProductsError(error.message));
  }
}

// 2. Watcher Saga - Listens for actions
function* watchFetchProducts() {
  // take = listen for action
  while (true) {
    const action = yield take('FETCH_PRODUCTS');
    // fork = non-blocking call
    yield fork(fetchProductsSaga, action);
  }
}

// 3. Root Saga
function* rootSaga() {
  yield fork(watchFetchProducts);
  yield fork(watchCreateProduct);
  yield fork(watchUpdateProduct);
}

// 4. Setup with store
import createSagaMiddleware from 'redux-saga';

const sagaMiddleware = createSagaMiddleware();

const store = createStore(
  rootReducer,
  applyMiddleware(sagaMiddleware)
);

sagaMiddleware.run(rootSaga);
```

**Real-world Example - Authentication:**

```javascript
// Actions
const LOGIN_REQUEST = 'LOGIN_REQUEST';
const LOGIN_SUCCESS = 'LOGIN_SUCCESS';
const LOGIN_FAILURE = 'LOGIN_FAILURE';
const LOGOUT = 'LOGOUT';

// API
const authAPI = {
  login: (username, password) =>
    fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify({ username, password }),
    }).then(r => r.json()),
  
  logout: () =>
    fetch('/api/auth/logout', { method: 'POST' }),
};

// Sagas
function* loginSaga(action) {
  try {
    const { username, password } = action.payload;
    
    // Call API
    const response = yield call(authAPI.login, username, password);
    
    // Store token
    localStorage.setItem('token', response.token);
    
    // Dispatch success
    yield put({
      type: LOGIN_SUCCESS,
      payload: response.user,
    });
    
    // Navigate after login
    yield put(navigateTo('/dashboard'));
  } catch (error) {
    yield put({
      type: LOGIN_FAILURE,
      payload: error.message,
    });
  }
}

function* logoutSaga() {
  try {
    yield call(authAPI.logout);
    localStorage.removeItem('token');
    yield put(navigateTo('/login'));
  } catch (error) {
    console.error('Logout error:', error);
  }
}

function* watchAuth() {
  yield takeEvery(LOGIN_REQUEST, loginSaga);
  yield takeEvery(LOGOUT, logoutSaga);
}

// Reducer
const initialState = {
  user: null,
  loading: false,
  error: null,
  isAuthenticated: false,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_REQUEST:
      return { ...state, loading: true, error: null };
    case LOGIN_SUCCESS:
      return {
        ...state,
        loading: false,
        user: action.payload,
        isAuthenticated: true,
      };
    case LOGIN_FAILURE:
      return { ...state, loading: false, error: action.payload };
    case LOGOUT:
      return initialState;
    default:
      return state;
  }
}
```

---

## Advanced Saga Patterns - Handling Complex Side Effects

**1. Debounce - Wait before executing (search, filter)**

```javascript
function* searchSaga() {
  yield debounce(
    500,
    'SEARCH_REQUEST',
    function* (action) {
      const results = yield call(searchAPI.search, action.payload);
      yield put(searchSuccess(results));
    }
  );
}
```

**2. Throttle - Execute max once per interval (scroll, click)**

```javascript
function* rateLimitedApi() {
  yield throttle(
    1000,
    'API_CALL',
    function* (action) {
      const data = yield call(apiCall, action.payload);
      yield put(apiSuccess(data));
    }
  );
}
```

**3. Race - First effect wins (fetch with timeout)**

```javascript
function* fetchWithTimeout() {
  const { response, timeout } = yield race({
    response: call(fetchData),
    timeout: delay(5000),
  });
  
  if (timeout) {
    yield put(fetchError('Request timeout'));
  } else {
    yield put(fetchSuccess(response));
  }
}
```

**4. All - Run in parallel (fetch multiple resources)**

```javascript
function* initAppSaga() {
  const [user, products, settings] = yield all([
    call(fetchUser),
    call(fetchProducts),
    call(fetchSettings),
  ]);
  
  yield put(appInitialized({ user, products, settings }));
}
```

**5. Select - Get current state (conditional logic)**

```javascript
function* wiseSaga(action) {
  const state = yield select();
  const isAuthenticated = yield select(state => state.auth.isAuthenticated);
  
  if (isAuthenticated) {
    yield fork(fetchProtectedData);
  } else {
    yield put(navigateTo('/login'));
  }
}
```

**6. Channel - Custom messaging (WebSocket)**

```javascript
function* watchWebSocket() {
  const socket = new WebSocket('ws://...');
  const channel = eventChannel(emit => {
    socket.onmessage = (event) => emit(JSON.parse(event.data));
    return () => socket.close();
  });
  
  while (true) {
    const message = yield take(channel);
    yield put(receiveMessage(message));
  }
}
```

**7. Cancellation - Stop effect on action**

```javascript
function* fetchWithCancellation() {
  while (true) {
    const action = yield take('FETCH_START');
    
    // Start fetch
    const task = yield fork(fetchDataSaga, action.payload);
    
    // Wait for either success or cancel
    yield race({
      success: take('FETCH_COMPLETE'),
      cancel: take('FETCH_CANCEL'),
    });
    
    // Cancel the task if needed
    if (action.type === 'FETCH_CANCEL') {
      yield cancel(task);
    }
  }
}
```

---

## Side Effects in Redux Saga

Side effects are operations that interact with the outside world (API calls, browser APIs, timers, etc.).

**Common Side Effects:**

**1. API Calls**

```javascript
function* fetchUserSaga(action) {
  try {
    // Side effect: HTTP request
    const user = yield call(
      fetch,
      `/api/users/${action.payload}`
    ).then(r => r.json());
    
    yield put(fetchUserSuccess(user));
  } catch (error) {
    yield put(fetchUserError(error));
  }
}
```

**2. Local Storage**

```javascript
function* persistStateSaga(action) {
  try {
    // Side effect: Write to localStorage
    yield call(
      localStorage.setItem,
      'appState',
      JSON.stringify(action.payload)
    );
    yield put(persistSuccess());
  } catch (error) {
    yield put(persistError(error));
  }
}
```

**3. Timers & Delays**

```javascript
function* timerSaga() {
  // Side effect: Wait (delay)
  yield delay(3000);
  yield put(timerExpired());
}
```

**4. Navigation**

```javascript
function* redirectAfterLoginSaga() {
  yield put(loginSuccess(user));
  // Side effect: Change browser URL
  yield put(push('/dashboard'));
}
```

**5. External Library Calls**

```javascript
function* analyticsEventSaga(action) {
  // Side effect: Send analytics
  yield call(
    analyticsService.track,
    action.payload.event,
    action.payload.data
  );
}
```

**6. Browser Geolocation**

```javascript
function* getLocationSaga() {
  try {
    // Side effect: Access browser geolocation
    const location = yield call(
      new Promise((resolve, reject) => {
        navigator.geolocation.getCurrentPosition(
          position => resolve(position.coords),
          error => reject(error)
        );
      })
    );
    yield put(locationReceived(location));
  } catch (error) {
    yield put(locationError(error));
  }
}
```

**7. Notifications**

```javascript
function* showNotificationSaga(action) {
  // Side effect: Show notification
  yield call(
    notifications.show,
    action.payload.message,
    action.payload.type
  );
}
```

**8. Combining Multiple Side Effects**

```javascript
function* completeOrderSaga(action) {
  try {
    // 1. API call
    const order = yield call(apiService.createOrder, action.payload);
    
    // 2. Store in localStorage
    yield call(localStorage.setItem, 'lastOrder', JSON.stringify(order));
    
    // 3. Send analytics
    yield call(analytics.track, 'order_created', { orderId: order.id });
    
    // 4. Dispatch notification
    yield put(showNotification('Order created successfully!'));
    
    // 5. Navigate
    yield put(push(`/orders/${order.id}`));
    
    // 6. Update state
    yield put(orderCreated(order));
  } catch (error) {
    yield put(orderError(error.message));
  }
}
```

---

## Testing Sagas

```javascript
import { runSaga } from 'redux-saga';
import { call, put } from 'redux-saga/effects';

// Test loginSaga
const test = async () => {
  const dispatched = [];
  
  await runSaga(
    {
      dispatch: (action) => dispatched.push(action),
      getState: () => ({ auth: {} }),
    },
    loginSaga,
    { payload: { username: 'user', password: 'pass' } }
  );
  
  expect(dispatched[0]).toEqual({
    type: LOGIN_SUCCESS,
    payload: expect.any(Object),
  });
};

// Test with jest (generator testing)
describe('loginSaga', () => {
  it('should login successfully', () => {
    const generator = loginSaga({
      payload: { username: 'user', password: 'pass' },
    });
    
    // First yield: call(authAPI.login, ...)
    const callEffect = generator.next();
    expect(callEffect.value).toEqual(
      call(authAPI.login, 'user', 'pass')
    );
    
    // Mock API response
    const result = { user: { id: 1 }, token: 'abc' };
    const putEffect = generator.next(result);
    expect(putEffect.value).toEqual(
      put({ type: LOGIN_SUCCESS, payload: result.user })
    );
  });
});
```

---

## Saga vs Redux Thunk vs RTK

```javascript
// Redux Thunk (Simple async)
const fetchUser = (id) => async (dispatch) => {
  dispatch(fetchUserStart());
  try {
    const data = await fetch(`/api/users/${id}`).then(r => r.json());
    dispatch(fetchUserSuccess(data));
  } catch (error) {
    dispatch(fetchUserError(error));
  }
};

// Redux Saga (Complex side effects, better testing)
function* fetchUserSaga(action) {
  try {
    yield put(fetchUserStart());
    const data = yield call(fetch, `/api/users/${action.id}`);
    yield put(fetchUserSuccess(data));
  } catch (error) {
    yield put(fetchUserError(error));
  }
}

// Redux Toolkit (Modern, recommended, built-in async)
export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (id, { rejectWithValue }) => {
    try {
      return await fetch(`/api/users/${id}`).then(r => r.json());
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);
```

**When to use each:**

| Use Case | Best Choice | Why |
|----------|------------|-----|
| Simple async API calls | RTK | Modern, simpler syntax |
| Complex side effect flows | Saga | Generators, testable, cancellation |
| Basic projects | Thunk | Lightweight, less boilerplate |
| Large enterprise apps | Saga | Better organization, testability |
| New projects | RTK | Official recommendation |
| Legacy Redux apps | Thunk | Already using it |
