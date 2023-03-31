## Problem
I have a list of items, when user types text filter out items from list and highlight the search term in each result.

## Environment

- IDE Version: Visual Studio Code

## How you fix it

We create a function which takes two parameters, array and search text. then apply filter method to extract all items containing search text. Each return item comes with it's original text but search text / term wrapped in a span and add class on the span to highlight it.

## Solution

```
export const highlightSearchTerm = (list = [], value = '') => {

  /*** mutate list / array so that original array does't update ***/
  let _list = extractMutatedList(list);

  if (!value) return _list;

  _list = _list.filter((item) => {
    const _value = value.toLowerCase();
    const _label = item.label.toLowerCase();

    if (_label.includes(_value)) {
      const { label } = item;
      const index = _label.indexOf(_value);
      const highlight = label.substring(index, index + value.length);

      const result =
        label.substring(0, index) +
        `<span class='highlight'>${highlight}</span>` +
        label.substring(index + highlight.length, label.length);

      item.text = result;

      return item;
    }
  });

  return _list;
};

/*** mutate array so that original array does't update
***/
const extractMutatedList = (list) => {
  let _list = [...list];
  _list = _list.map((item) => ({ ...item }));

  return _list;
};
```

### Demo Link
https://stackblitz.com/edit/react-ts-amqaws?file=App.tsx
