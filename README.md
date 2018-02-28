# vue-datatable
Simple vue js datatable

## Use
As a component
```javascript
Vue.component('datatable', require('datatable'));
```

```html
<datatable :data="data" @rowClicked="doSomething"></datatable>
```

The data passed down should an array with single node objects as its content. 
```json
[
	{
		'name': 'roksta',
		'age': '24',
		'location': 'Nairobi'
	},
	{
		'name': 'sam',
		'age': '23',
		'location': 'Kenya'
	},
]
```

The object keys are the table column names and the data is represented in its key-value pairings, eg, the sample data above represents a table with 3 columns - name, age and location. The values are the number of objects in the array, this time 2.

The datatable is searchable, sortable and paginated.