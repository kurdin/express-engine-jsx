# express-engine-jsx

Example of `users.jsx` view file
```jsx harmony
var Layout = require('./layout');

<Layout>
  <ul class="users">
    {users.map(user => (
    	<li key={user}>{user.name}</li>
    ))}
  </ul>
</Layout>
```

Example of `layout.jsx` view file
```jsx harmony
<html>
<head>
  <meta charset="UTF-8"/>
</head>
<body>{children}</body>
</html>
```

Example of router
```javascript
app.get('/users', function (req, res) {
	res.render('users', {
		users: [
			{name: 'Max'},
			{name: 'Bob'}
		]
	});
});
```

Output html
```html
<!DOCTYPE html>
<html>
<head><meta charset="UTF-8"/></head>
<body><ul class="users"><li>Max</li><li>Bob</li></ul></body>
</html>
```

## How it works

When you render some view, this engine takes `jsx` file like this
```jsx harmony
var Layout = require('./layout');

<Layout>
  <ul class="users">
    {users.map(user => (
    	<li key={user}>{user.name}</li>
    ))}
  </ul>
</Layout>
```

and compiles it to `js` file like this
```javascript
var React = require('react');
var requireJSX = require('express-engine-jsx/require');

module.exports = function (props) {
  var __components__ = [];
  with (props) {
  	var Layout = requireJSX('./layout');
  	
  	__components__.push(
      React.createElement(
      	Layout, 
      	null,
      	React.createElement(
      	  'ul',
      	  {className: 'users'},
      	  users.map(user => (
            React.createElement(
              'li',
              {key: user},
              user.name
            )
          ))
      	)
      )
    );
  }
  return __components__;
};
```

and now this component can be rendered to html with `ReactDOM.renderToStaticMarkup()`.

As you can see, each jsx view file returns array of components
```jsx harmony
<div id="first"></div>
<div id="second"></div>
```

```javascript
//...
__components__.push(React.createElement('div', {id: 'first'}));
__components__.push(React.createElement('div', {id: 'second'}));
//...

return __components__;
```

## Usage

```javascript
var express = require('express');
var app = express();

require('express-engine-jsx').attachTo(app, {
  cache: __dirname + '/cache', // required and should be absolute path to cache dir of compiled js files
  views: __dirname + '/views', // required and should be absolute path to views dir with jsx files
  doctype: '<!DOCTYPE html>'   // optional and this is default value
});
```

That's it, you no need to do `app.set('views', 'views')` and so on, `attachTo` will do that for you

## License

MIT, see `LICENSE` file