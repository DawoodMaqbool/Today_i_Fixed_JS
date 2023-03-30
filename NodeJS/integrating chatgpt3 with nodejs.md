
## Problem
How to integrate ChatGPT3 with nodejs application

## Environment
- IDE Version: Visual Studio Code

## How you fix it
To solve this, I created my api key from https://platform.openai.com/, read the documentation from https://platform.openai.com/docs/api-reference and then integrated chatgpt api with nodejs. Have used openai npm package for communicating with chatgpt. Moreover I have used completions service of chatgpt for this task. 

## Solution

```
const express = require("express");
const app = express();
const bodyParser = require('body-parser');
require('dotenv').config();

const {OpenAIApi, Configuration} = require('openai');
console.log(process.env.API_KEY);
const configuration = new Configuration({
  apiKey: process.env.API_KEY,
});

app.use(bodyParser.json());

app.post('/text-generator',(req, res) => {
  const {model, prompt, max_tokens} = req.body;
  console.log(req.body);
  const openai = new OpenAIApi(configuration);
  openai.createCompletion({
    // the model you want to use for your query
    model:model, 
    // the query you want to send
    prompt:prompt, 
    // size of the response  
    max_tokens: max_tokens, 
    // value up-to which regenerated response will be mixed. If set to 0 the response will remain same 
    temperature: 0.5 
  }).then((response) => {
    console.log("Response:", response.data.choices[0].text);
    return res.status(200).json({"message": response.data});
  }).catch(err => {
    return res.status(400).json({message: err});
  });
});

app.listen(3001, () => {
  console.log('Server running on http://localhost:3001/');
});

//You can test this by sending a request via postman with following json as body 

{
  "model": "text-davinci-003",
  "prompt": "what is nodejs",
  "max_tokens": 850
}
```

