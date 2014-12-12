![image](https://raw.githubusercontent.com/GesJeremie/Gotham-documentation/master/background.jpg)

# GothamJS

GothamJS is a simple and tiny framework built for **Directories Project**.

## Summary
- [Installation](#installation)
- [Application Flow Chart](#application-flow-chart)
- [Routes](#routes)

## Installation

- Go to `wanker/app_front` folder with your terminal
- Install bower dependencies `$ bower install`
- Install npm dependencies `$ npm install`
	- **Note:** Use `$ sudo npm install` if it's not work
- Run brunch `$ brunch watch`



## Application Flow Chart

![image](https://raw.githubusercontent.com/GesJeremie/Gotham-documentation/master/schema.jpg)

## Routes
Each routes of your application must be added in the `routes.coffee` file. 

Imagine this file `routes.coffee` : 

```

module.exports = (route) ->
  
  route.match 'zombie/users', 'zombie#index'

```

> In this example when the current URL request will be : `http://localhost:3000/zombie/users`, Gotham will execute the file `controllers/zombie/index.coffee`

Of course you can add variables into your routes to be more flexible.

```

module.exports = (route) ->
  
  route.match 'zombie/:id/edit', 'zombie#edit'

```

> In this example, an url like `http://localhost:3000/zombie/243/edit` or `http://localhost:3000/zombie/frank/edit` will execute the file `controllers/zombie/edit.coffee`




