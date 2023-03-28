## Problem

We are using FormData to POST a file using multipart/form-data, but payload is sent as an object instead of binary data and consequently we receive http 500 (Internal Server Error).

## Environment

- IDE Version: Visual Studio Code

## How you fix it

We need to remove Content-Type from http request headers, in our current scenario http requests are made through axios library and axios interceptor is used for all requests.

## Solution

```
const uploadImage = (payload) => {
    try {
      delete axiosInterceptor.defaults.headers['Content-Type'];

        const formData = new FormData();
        formData.append('file', payload);


      const {data} = await axiosInterceptor.post('upload/image', formData);

      return data;
    } catch (error) {
      throw new Error(error.message);
    }
};
```
