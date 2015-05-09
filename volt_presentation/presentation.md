Intro to Volt
======
### Lunch n' Learn
#### May 8th 2015
###### :-) Last updated like a few minutes ago

!

### Who Am I?

- Wizard Engineer @Wizard Development
- GitHub: armandoamador
- Twitter: mando_amador

!

### Presentation Objectives

- What is Volt
- What can it be used for
- Some of it's inner workings
- Getting Started
- Example Projects
- Resources

!

### What is Volt ?

- Client and Server Web Framework
- Uses Opal
- Same code runs on the client and the server!
- Uses flow/reactive programming

!

### What are some of it's usage?

- Automatic Data Syncing
- Reactive Data Binding
- Build apps as nested components

!

### Usage (cont'd)

- Client code + Assets + Server Code
- Can be packaged as gems
- Can provide "tags" (quick ways to reuse code)

!

### Example Components

- Field error messages
- Login/Signup
- File upload
- Pagination

!

### MVVM

- Model
- Controller/ViewModel
- View

!

### Let's Create a Ingredient organizer Demo App

- Ingredients must have a name and a description
- Ability to add and destroy ingredients

!

### Getting Started

- `gem install volt`
- `volt new demo-app`
- `cd demo-app`
- `mongod`
- `volt s`

!

### Creating an ingredient page

```
#app/main/views/main/ingredients.html
<:Title>
  Ingredients

<:Body>
   <h1>Ingredients</h1>
```

!

### Adding ingredient to the nav bar

```
#app/main/views/main/main.html

  <:nav href=“/ingredients”>Ingredients<:nav>

```

### Adding ingredients route

```
#app/main/config/routes.rb

client ‘/ingredients’, action: ‘ingredients’

```

!

### Create an Ingredient form

```
#app/main/views/main/ingredients.html

<div class="row">
  <div class="col-md-12">
    <form>
      <div class="form-group">
        <label>Name</label>
        <input type="text" class="form-control">
      </div>
      <div class="form-group">
        <label>Description</label>
        <textarea class="form-control" rows="3">
        </textarea>
      </div>
      <button type="submit" class="btn btn-default">
        Add New Ingredient
      </button>
    </form>
  </div>
</div>

```

!

### Make form dynamic and work

```
#app/main/views/main/ingredients.html

<div class="row">
  <div class="col-md-12">
    <form e-submit="add_ingredient" role="form">
      <div class="form-group">
        <label>Name</label>
        <input type="text" class="form-control"
          value="{{page._new_ingredient!._name}}">
      </div>
      <div class="form-group">
        <label>Description</label>
        <textarea class="form-control" rows="3"
          value="{{page._new_ingredient!._description}}">
        </textarea>
      </div>
      <button type="submit" class="btn btn-default">
        Add New Ingredient
      </button>
    </form>
  </div>
</div>

```

!

### Make form dynamic and work cont'd

```
#app/main/controllers/main_controller.rb

def add_ingredient
  page._ingredients << {
    name: page._new_ingredient._name,
    description: page._new_ingredient._description
  }
  page._new_ingredient._name = ''
  page._new_ingredient._description = ''
end

```

!

### Showing ingredients

- Let's create a table of how we want it to look

```
#app/main/views/main/ingredients.html

<table class="table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Almond Flour</td>
      <td>Stuff</td>
    </tr>
  </tbody>
</table>

```

!

### Showing Ingredients cont'd

- Let's make the table have dynamic data

```
#app/main/views/main/ingredients.html

<table class="table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    {{ page._ingredients.each do |ingredient| }}
      <tr>
        <td>{{ ingredient._name}}</td>
        <td>{{ ingredient._description}}</td>
      </tr>
    {{ end }}
  </tbody>
</table>

```

!

### Deleting Ingredients

```
#app/main/views/main/ingredients.html

<table class="table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
      <th>Action</th>
    </tr>
  </thead>
  <tbody>
    {{ page._ingredients.each do |ingredient| }}
      <tr>
        <td>{{ ingredient._name}}</td>
        <td>{{ ingredient._description}}</td>
        <td><button e-click="ingredient.destroy">X</button></td>
      </tr>
    {{ end }}
  </tbody>
</table>

```

!

### Ability to select an ingredient and add extra info

- Quick styling for this feature
```
# app/main/assets/css/app.css.scss

tr {
  &.selected td {
    background-color: #428bca;
    color: #FFFFFF;

    button {
      color: #000000;
    }
  }

  td {
    padding: 5px;
    border-top: 1px solid #EEEEEE;
  }
}

```

!

### Bonus Feature cont'd

```

#app/main/views/main/ingredients.html

<div class="row">
  <div class="col-md-12">
    <table class="table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Description</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        {{ page._ingredients.each_with_index do |ingredient, index| }}
        <tr e-click="params._index = index"
          class="
          {{ if (params._index || 0).to_i == index }}selected{{end}}">
          <td>{{ ingredient._name}}</td>
          <td>{{ ingredient._description}}</td>
          <td><button e-click="ingredient.destroy">X</button></td>
        </tr>
        {{ end }}
      </tbody>
    </table>
  </div>
</div>

```

!

### Bonus Feature cont'd

```

#app/main/views/main/ingredients.html

<div class="row">
  <div class="col-md-12">
    {{ if current_ingredient }}
    <h2>{{ current_ingredient._name }}</h2>

    <textarea>{{ current_ingredient._price }}</textarea>
    {{ end }}
  </div>
</div>

```

!

### Bonus Feature cont'd

```
#app/main/controllers/main_controller.rb

def current_ingredient
  page._ingredients[(params._index || 0).to_i]
end

```

!

### How can we save data?

```
#app/main/controllers/main_controller.rb

model :store

def add_ingredient
  _ingredients << {
    name: page._new_ingredient._name,
    description: page._new_ingredient._description
  }
  page._new_ingredient._name = ''
  page._new_ingredient._description = ''
end

def current_ingredient
  _ingredients[(params._index || 0).to_i]
end


#app/main/views/main/ingredients.html

{{ _ingredients.each_with_index do |ingredient, index| }}

```

!

### Demo Apps

- TodoMvc
- Blog
- [Typing Calculator](https://typing-calculator.herokuapp.com/)

!

### Recap

- Auto Sync
- Less HTTP/REST(web sockets)
- One Language
- Components (Isolate)
- Single Framework
- Centralized State
- Active Community

!

### Resources

- [Volt Framework](http://voltframework.com)
- [Building Todo app in 10 minutes](https://www.youtube.com/watch?v=KbFtIt7-ge8)
- [Real-Time Web Apps with Volt in Ruby](http://www.sitepoint.com/real-time-web-apps-volt-ruby/)
- [Isomorphic App Development - RubyConf 2014](https://www.youtube.com/watch?v=7i6AL7Walc4)

!

# Thank You
