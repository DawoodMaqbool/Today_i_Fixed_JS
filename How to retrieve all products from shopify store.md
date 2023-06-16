## Problem
...
If you want to fetch all the products from shopify using Admin API in NodeJS.

## Environment
... Visual Studio Code, NodeJS ...

## How you fix it
...  In the .env file we place the Access Token and the Store Name. We then use it in our code inside the URL to make the request.

For executing the code. simple use "npm i" to install the dependencies and then "node *filename*" to start fetching details of product from the Shopify store and would start writing in the file *productsListing* in the JSON format.

## Solution
```
const fs = require('fs');
const axios = require('axios');
require('dotenv').config();

async function retrieveProductsFromCollection(pageInfo = '', products = [],cycle=0) {
  console.log(" page info = = = = >> ", cycle)
  if (!pageInfo && cycle) {
    return products;
  }

  const url = `https://${process.env.ACCESS_TOKEN}@${process.env.STORE_NAME}/admin/api/2022-10/products.json?limit=250&fields=id,images,title,body_html,vendor,product_type,status&page_info=${pageInfo}`;

  const response = await axios.get(url);

  const { data } = response;
  products = products.concat(data.products);

  const nextPageInfo = getNextPageInfo(response.headers);
  const jsonData = JSON.stringify(products, null, 2);
    fs.writeFile('productsListing.json', jsonData, 'utf8', err => {
      if (err) {
        console.error('Error writing to JSON file:', err);
      } else {
        console.log('Products data written to products.json');
      }
    });

    if (!nextPageInfo) {
    return retrieveProductsFromCollection('', products, ++cycle);
  }

  return retrieveProductsFromCollection(nextPageInfo, products, ++cycle);
}

function getNextPageInfo(headers) {
  const linkHeader = headers['link'];
  if (linkHeader) {
    const nextPageInfo = extractLinkHeaderInfo(linkHeader);
    return nextPageInfo;
  }
  return null;
}

function extractLinkHeaderInfo(linkHeader) {
      const urlParams = new URLSearchParams(linkHeader);

       const nextPageInfo = urlParams.getAll('page_info');
  const links = nextPageInfo[nextPageInfo.length-1].split('>; ');
  if(links[1].includes('previous')){
    return null
  }
  return links[0];
}

retrieveProductsFromCollection('', []).catch(err => console.log(err));

```
