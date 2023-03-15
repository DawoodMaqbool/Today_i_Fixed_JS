## Problem
...  
If you want to redirect a user to different destination when a specific URL is hit then you can use the following code.  ...

## Environment
... Visual Studio Code, Next.JS ...

## How you fix it
...  In the next.config.js file we use the redirects key. redirects is an async function that expects an array to be returned holding objects with source, destination, and permanent properties: ...

... 
- source is the incoming request path pattern.
- destination is the path you want to route to.
- permanent true or false - if true will use the 308 status code which instructs clients/search engines to cache the redirect forever, if false will use the 307 status code which is temporary and is not cached.
 ...

## Solution
```module.exports = {
  async redirects() {
    return [
      {
        source: '/blog',
        destination: '/news',
        permanent: true,
      },
    ]
  },
}```
