#MDUTILS.selectBox

- selectBoxName = the name of the select box
- data = the JSON object holding the data
- onClick = the function to run when a user makes a selection
- options = JSON object with all other params

The first 3 are required.  That's why they are not just part of "options".

Options may contain nothing.  But, it "can" contain...

- sql = a sql statement that will build the dataArray.  If sql is used, dataArray will be empty or undefined.
- keyField = the name of the keyField
    - if this is omitted, the first field in the dataArray will be used
- displayField = the name of the field that will be displayed in the dropdown
    - if this is omitted, the second field in the dataArray will be used
- onChange = similar to onClick.  the function to run when the onChange event fires
- style = any "inline" styling to apply to the select box
- class = much preferred to inline style.  We probably need a metronic based default here, but used this if it is supplied as part of the options

Also, having an "options" param makes this function future proof.  If we need to add something later, it can be added without breaking any code that calls this function.
```js
MDUTILS.selectBox = function( selectBoxName, data, onClick, options ) {
    ...all the code goes here
}
```
