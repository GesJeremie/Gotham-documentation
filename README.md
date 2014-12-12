![image](https://raw.githubusercontent.com/GesJeremie/Gotham-documentation/master/background.jpg)

# GothamJS

GothamJS is a simple and tiny framework built for **Directories Project**.

## Summary
- [Installation](#installation)
- [Application Flow Chart](#application-flow-chart)
- [Routes](#routes)
- [Controllers](#controllers)
- [Libraries](#libraries)
 - [View](#view)

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

```coffeescript
module.exports = (route) ->
  
  route.match 'zombie/users', 'zombie#index'
```

> In this example when the current URL request will be : `http://localhost:3000/zombie/users`, Gotham will execute the file `controllers/zombie/index.coffee`

Of course you can add variables into your routes to be more flexible.

```coffeescript
module.exports = (route) ->
  
  route.match 'zombie/:id/edit', 'zombie#edit'
```

> In this example, an url like `http://localhost:3000/zombie/243/edit` or `http://localhost:3000/zombie/frank/edit` will execute the file `controllers/zombie/edit.coffee`

## Controllers 

As we you see in the **routes** part, a controller is associated to one or more routes. The structure of a basic controller is like this : 

```coffeescript
# require gotham
Gotham = require 'gotham/gotham'

class Zombie_Edit extends Gotham.Controller
  

  ##
  # Before
  #
  # Executed before the run action. You can use
  # @stop() in this method to stop the execution
  # of the controller
  #
  ##
  before: (params) ->


  ##
  # Run 
  #
  # The main entry of the controller.
  # Your code start here
  #
  ##
  run: (params) ->

   # Your code
   console.log 'Zombie edit controller loaded'



module.exports = Zombie_Edit
```

As you see in the **application flow chart** the `before()` method is always executed before the `run()` method. You can stop the execution of the method `run()` via the method `stop()`. 

`before()` is the right place to put some checks. 

Gotham send to `before()` and `run()` methods the params of the router. Imagine this `routes.coffee` file : 

```coffeescript
module.exports = (route) ->
  
  route.match 'zombie/:id/edit', 'zombie#edit'
```

In the controller `controllers/zombie/edit.coffee`, you can fetch the value of the variable `:id` via the `params` variable sent to `before()` and `run()`

## Libraries 

Gotham have some libraries to help you when you code. 

#### View
Sometimes you need to build some html block in your javascript application like this : 

```coffeescript

# Zombie datas
zombie = 
    name: 'antoine'
    lvl: 25

# Template
html = '<h1 class="zombie-name">' + zombie.name + '</h1>' + '<br/><span class="zombie-lvl">' + zombie.lvl + '</span>'

# Render in the html
$('#zombie-block').html(html)
```

It's ok for simple html blocks, but often you need to build big html blocks, that's why Gotham have a simple View library who use Handlebars.

Gotham have already out of the box a method in the controller called `view()` to load a template and compile this one with some datas. 

Create an Handlebars template in `views/zombie.hbs` : 

```handlebars
<h1 class="zombie-name">{{ name }}</h1>
<br/>
<span class="zombie-lvl">{{ lvl  }}</span>
```

Now you can load your template in your controller like this : 

```coffeescript
Gotham = require 'gotham/gotham'

class Zombie_Edit extends Gotham.Controller
  
  before: ->

  run: ->
    # Our zombie
    zombie = 
      name: 'pierre'
      lvl: 25
      
    # Load the template zombie.hbs with the zombie datas
    # and display the result in the console log
    console.log @view 'zombie', zombie

module.exports = Zombie_Edit
```

It's simple and pretty to read. Don't hesitate to read the documentation of [Handlebars](http://handlebarsjs.com/).




