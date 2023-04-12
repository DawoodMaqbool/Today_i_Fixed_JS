## Problem
By default, fetch api doesn't provide request and response interception. 

## Environment

- IDE Version: Visual Studio Code

## How you fix it

One way to create an interceptor for fetch api is to monkey patch it. Monkey patching is an approach to override the original functionality with your version of the function. In the following code snippet, I'm intercepting the requests by altering the url and then intercepting the response by changing the title property. 

## Solution

```
window.fetch = async (...args) => {
  let [resource, config] = args;
  // request interceptor starts

  resource = 'https://jsonplaceholder.typicode.com/todos/2';

  // request interceptor ends

  let response = await originalFetch(resource, config);

  // response interceptor
  const json = () =>
    response
      .clone()
      .json()
      .then((data) => ({ ...data, title: `Intercepted: ${data.title}` }));

  response.json = json;
  return response;
};
```


