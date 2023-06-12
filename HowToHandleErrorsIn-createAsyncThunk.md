# How to handle errors in createAsyncThunk


When working with Redux Toolkit and using the `createAsyncThunk` function, you can handle errors by utilizing the `rejectWithValue` callback provided by Redux Toolkit. Here's how you can handle errors in `createAsyncThunk`:

1. Define your async thunk action using `createAsyncThunk`. The second argument to `createAsyncThunk` is an async callback function that performs the asynchronous operation:

```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';

const fetchData = createAsyncThunk(
  'data/fetchData',
  async (params, { rejectWithValue }) => {
    try {
      // Perform your asynchronous operation here (e.g., API request)
      const response = await fetch('https://api.example.com/data');
      const data = await response.json();
      return data;
    } catch (error) {
      // Handle the error using rejectWithValue
      return rejectWithValue(error.response.data);
    }
  }
);
```

2. In the `catch` block of your async operation, instead of throwing the error, you call `rejectWithValue` with the value you want to include in the `rejected` action payload. In this example, we're assuming the API returns an error response with a `response.data` field containing error details. You can modify this based on your specific API response structure.

3. In your slice, handle the `rejected` action type to update your state with the error information:

```javascript
const dataSlice = createSlice({
  name: 'data',
  initialState: {
    loading: false,
    error: null,
    data: null,
  },
  reducers: {},
  extraReducers: (builder) => {
    builder.addCase(fetchData.pending, (state) => {
      state.loading = true;
    });
    builder.addCase(fetchData.fulfilled, (state, action) => {
      state.loading = false;
      state.data = action.payload;
    });
    builder.addCase(fetchData.rejected, (state, action) => {
      state.loading = false;
      state.error = action.payload; // Assign the error payload to the state
    });
  },
});
```

4. Use the `error` state in your component to display the error message or handle it in any other way:

```javascript
import { useDispatch, useSelector } from 'react-redux';

function MyComponent() {
  const dispatch = useDispatch();
  const { loading, error, data } = useSelector((state) => state.data);

  useEffect(() => {
    dispatch(fetchData());
  }, [dispatch]);

  if (loading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error}</div>;
  }

  return <div>Data: {data}</div>;
}
```

By utilizing `rejectWithValue` in the async callback of `createAsyncThunk`, you can handle errors and update the state with the error information, allowing you to display or handle it appropriately in your components.