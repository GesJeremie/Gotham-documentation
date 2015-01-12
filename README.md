![image](https://raw.githubusercontent.com/GesJeremie/Gotham-documentation/master/background.jpg)

# GothamJS

GothamJS is a simple and tiny framework built for **Directories Project**.

## Summary
- [Installation](#installation)
- [Application Flow Chart](#application-flow-chart)
- [Routes](#routes)
 - [Variables](#--variables)
 - [Constraints](#--constraints)
- [Controllers](#controllers)
- [Libraries](#libraries)
 - [View](#view)
 - [Syphon](#syphon)
    - [Get datas](#--get-datas)
    - [Exclude datas](#--exclude-datas)
 - [Validator](#validator)
    - [Validate datas](#--validate-datas)
    - [Get errors](#--get-errors)
    - [Nice names for attributes](#--nice-names-for-attributes)
    - [Create validation rule](#--create-validation-rule) 

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

#### - Variables
Of course you can add variables into your routes to be more flexible.

```coffeescript
module.exports = (route) ->
  
  route.match 'zombie/:id/edit', 'zombie#edit'
```

#### - Constraints

Sometimes you need to attach a constraint to a route. You can do that easily :

```coffeescript
route.match 'zombie/:town', 'zombie#town', (params) ->
  
  town = params.town

  if town is 'new-york'
    return true
  else
    return false

```

In this case, the route will match only if the town passed is `new-york`, it's a little bit useless here, but we can imagine everything. The only thing to keep in mind the constraint function must return `true` or `false`.

Another example : 

```coffeescript
route.match '', 'zombie#index', ->
  host = window.location.host

  # Split host
  segments = host.split '.'

  # Fetch sub domain
  sub_domain = segments[0]

  if sub_domain is 'south-dakota'

    return true

  return false
```

In this example Gotham will execute the file `controllers/zombie/index` only if the URL is `http://south-dakota.domain.com/`


> In this example, an url like `http://localhost:3000/zombie/243/edit` or `http://localhost:3000/zombie/frank/edit` will execute the file `controllers/zombie/edit.coffee`

## Controllers 

As we saw in the **routes** part, a controller is associated to one or more routes. The structure of a basic controller is like this : 

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

#### Syphon
Syphon is a tiny library to fetch datas from a form.

#####- Get datas
Imagine the form : 
```html
<form method="get" action="" class="form">
    <input type="text" name="user[name]"> <br/>
    <input type="text" name="lvl"> <br/>
    <input type="text" name="email"> <br/>
    <button class="btn btn-primary" type="submit">
        Add
    </button>
</form>
```
You can fetch datas from the form like this : 
```coffeescript
# Create a new syphon instance
syphon = new Gotham.Syphon()

# Fetch datas examples

# By the tag "form"
datas = syphon.get 'form'

# By the class ".form"
datas = syphon.get '.form'
```
**Note : You can use any jquery selectors**

#####- Exclude datas

Syphon provide a simple and elegant method to exclude some datas.

Imagine this form :
```html
<form method="get" action="" class="form">
    <input type="text" name="zombie[name]"> <br/>
    <input type="text" name="lvl"> <br/>
    <input type="text" name="email"> <br/>
    <button class="btn btn-primary" type="submit">
        Add
    </button>
</form>
```

We want fetch the datas of the form without the "email" field :

```coffeescript
# Create a new syphon instance
syphon = new Gotham.Syphon()

# Exclude email
datas = syphon.exclude('email').get 'form'

# Other examples : 

# Exclude email and lvl
other = syphon.exclude('email', 'lvl').get 'form'

# Exclude email and zombie[name] with array style
zombie = syphon.exclude(['email', 'zombie[name]']).get 'form'

```

#### Validator

Validator provide a solution to validate datas.

##### - Validate datas

```coffeescript
    # New validator instance
    validation = new Gotham.Validator()

    # Your datas
    datas = 
      'name': 'Dead man'
      'email': 'zombies.forever@dude.com'
      'lvl': 99

    # Your rules
    rules =
      'name': 'required'
      'email': 'required|email'
      'lvl': 'required|max:100'

    # Run validation
    validation.make datas, rules
    
    if validation.passes()
      @log 'Success !' # @log is a shortcut for console.log()
    else
      @log 'Error !'
```

##### - Get errors

**All**

```coffeescript
# Fetch all errors found
@log validation.errors 'all'
```

**First**
```coffeescript
# Fetch the first error for the data lvl
@log validation.errors 'first', 'lvl'
```

**Last**
```coffeescript
# Fetch the last error for the data lvl
@log validation.errors 'last', 'lvl'
```

**Get**
```coffeescript
# Get all errors for the data lvl
@log validation.errors 'get', 'lvl'
```
##### - Nice names for attributes

Validator use the attribute name of a data to render the error message. Sometimes you need a different name, for this you need to go to `app/validators.coffee` and update `Validator::attributes`. 

Example : 

```coffeescript
##
# Attributes
##
Validator::attributes
  'user_name': 'Username'
  'password_confirmation': 'Password confirmation'
  'user[lastname]': 'User last name'

```
##### - Create validation rule

Validator provide right now only few rules, you can create yours easily.
The right place to create your rules is in `app/validators.coffee`, but you can do this in a controller by example.

In this example we will create an `equal` validation rule.

```coffeescript
# We are in app/validators.coffee
# but it's possible to put this code in a controller's
# method by example

##
# Equal
#
# Check if a field is equal to a value
##
Validator::rule 'equal', (attribute, value, params) ->
  
  if value is params[0]
    return true

  return false


# We add an error message for this rule
Validator::error 'equal', 'The value of the field :attribute must equal to :value'

```

Now we can use this rule like that : 
  
```coffeescript

# Validation 
validation = new Gotham.Validator()

# Your datas
datas = 
  'name': 'walker'

# Your rules
rules =
  'name': 'equal:walker'

# Run validation
validation.make datas, rules

# Will return true
@log validation.passes()
```


