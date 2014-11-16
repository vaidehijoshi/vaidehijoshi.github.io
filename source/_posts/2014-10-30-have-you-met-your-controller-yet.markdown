---
layout: post
title: "Have you met your controller yet?"
date: 2014-10-30 22:28:43 -0500
comments: true
categories: "Flatiron School"
---

Controllers: we all have 'em. Sure, they put the C in MVC, but how much do we really *know* about these mysterious creatures? Probably not a lot. Don't you wish you could just look your Controller in the eye and be all, "Hey dude, what *are* you?" Well, you can. All you have to do is just poke around a bit in your code.


Let's start with what we *do* know. Given the MVC orientation of Rails, we already know that the Controller acts as a kind of mediator, intercepting messages between the client and the server, and passing information between the model and view. But ultimately, Controllers are nothing more than Ruby classes (that inherit from ActionController::Base) with class methods called " actions", which are simply meant to render a template for the user to see. But don't mistake them as simple chunks of code; there are also some pretty amazing things that Controllers can do.

<!-- more -->

##1. Controllers can have empty actions and STILL work.

Okay, time to break the ice. Let's get to know our Controllers. Below is some generic code for a Controller for a guacamole app. First off is my main ApplicationController, which becomes a parent class for my GuacController to inherit from:

```ruby

class ApplicationController < ActionController::Base
end


class GuacController < ApplicationController
  def index
    render
  end
end

```

See that `render()` method? All it does is render the default template for the current controller and action. And since I'm in the index action of the GuacController, this code would render the template `app/views/guac/index.html.erb`

But what would happen if I had an empty `index` method?

```ruby
class GuacController < ApplicationController
  def index
  end
end
```

This would also render `app/views/guac/index.html.erb`. But...why? Well, the cool thing about Controllers is that they will call `render` by default, even if the action (in this case, `index`) doesn't call the `render` method explicitly. In fact, every single Controller action already has the render method implicitly within it!

And you know what's really crazy? This code would *also* render the same index page:

```ruby
class GuacController < ApplicationController
end
```

When Rails gets a request for the `index` action and sees that there's no such action in the GuacController, it assumes that if there *were* an `index` action, it would render `app/views/guac/index.html.erb`. So that's exactly what it does, even though you haven't even given it the code to do so explicitly.

Obie Fernandez explains why exactly this works in his book, *The Rails 4 Way*:
> "The goal of the typical controller action is to render a view template—that is, to fill out the template and hand the results, usually an HTML document, back to the server for delivery to the client. Oddly—at least it might strike you as a bit odd, though not illogical—you don’t actually need to define a controller action, as long as you’ve got a template that matches the action name."


##2. Controllers can render almost anything:

Even though the `render` method is implicit and doesn't actually ever have to be written out by you, the programmer, it can actually be quite a handy little tool. You can use this method within a Controller action if you want it to render something *other* than its default template. For example, I could tell my GuacController to render any other file in my `app/views` directory by just using the `render` method.

This is useful for two different reasons:

* You can render another action's template:
⋅⋅*For example, my `create` action in my GuacController could have something like `render action:'new'`, which would render my `app/views/guac/new.html.erb`. This could be super awesome if a new instance of `@guac` hadn't been saved.

* You can render a different template entirely:
⋅⋅*If I was out of guac and I wanted my guac index page to actually render the template for my salsa index, I could explicitly render it by putting the following code in the `index` action of my GuacController: `render template:'/salsa/index.html.haml'`. Of course, this doesn't really answer the question of where all my guacamole went, but that's neither here nor there.


##3. Controllers are all for Convention over Configuration:

Because of naming conventions for Controllers, if I have a Guac Controller, Rails assumes that there is a class called GuacController saved as a file `guac_controller.rb` in my `app/controllers` directory. It will also look for corresponding view templates for my GuacController in `app/views/guac`, and it will take the output of these views and save them as `guac.html.erb`.

Basically, your Controller makes a lot of assumptions whenver it gets a single web request. But, this also means that *you* can  anticipate how your Controller might react by simply understanding what your Controller already knows and assumes about your code. 

I guess what I'm trying to say is...get to know each other.


## tl;dr?

* Your Controller does a lot of stuff for you without you even realizing it. To make the most of its magical powers, learn as much as you can about its conventions.
* Play around with the `render` method to understand when it can be used and for what purposes.
* Get to know your Controller. I'm telling you: it will be the beginning of a beautiful friendship.