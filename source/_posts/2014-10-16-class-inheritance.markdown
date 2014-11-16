---
layout: post
title: "Class inheritance"
date: 2014-10-16 20:00:34 -0500
comments: true
categories: "Flatiron School"
---

Confession time: I'm kind of in love with object-oriented programming. 

I should probably clarify that I don't always understand OOP, and I sometimes even confuse myself for no good reason whenever the topic of "self" comes up. But hey, every relationship has its ups and downs, and I've only really been committed to this one for the past week or so.

OOP is easy to understand mostly because of its strong connection to real life -- everything is an object, all objects have properties/traits/characteristics, so on and so forth. But dealing with multiple *types* of objects across different types of *classes* gets real tricky, *real* fast. This especially holds true when it comes to class inheritance.

<!-- more -->


## WTF is inheritence?

Inheritence in object-oriented programming is pretty much exactly what it sounds like: when one class **inherits** the properties of another class, just like how you inherit traits from your parents (even if you might wish otherwise).

Okay, maybe an example here will help:

Imagine that you have a class called `BakedStuff`, which includes all the baked goods your heart could ever desire. Every instance (every *object*) of this `BakedStuff` class has a `name`, baking `time`, and `baking_temperature`, which we'll set to default at 375 degrees.

And for good measure, let's also throw in a little method in there called `bake_for`, which is just going to return a string that will tell us how much time to bake our treats for, and at what temperature.


```ruby
class BakedStuff

  attr_reader :name, :time, :baking_temperature

  def initialize(name, time, baking_temperature=375)
    @name = name
    @time = time
    @baking_temperature = baking_temperature
  end

  def bake_for
    "To make #{name}, bake for #{time} at #{baking_temperature} degrees fahrenheit."
  end

end
```

Nothing *too* complicated about it, right?

Now I can create an actual object and pass in the correct parameters. I can even ask the cake how long I should bake it for, which would look something like this:

```ruby
cake = BakedStuff.new("cake", "30 min")
puts cake.bake_for
```
would output: `To make cake, bake for 30 min at 375 degrees fahrenheit.`

Awesome. I love cake. But you know what I like even more than cake? Cookies! Just like in my first class, I want to make a new `Cookie` class, which will also have a name, time, baking temperature, and `bake_for` method. Now, I could theoretically rewrite all of my code from the `BakedStuff` class, but then I'd have less time to bake and I'm also pretty sure that goes against the whole "DRY" concept in Ruby.

What I *could* do is have my new `Cookie` class *inherit* everything from my `BakedStuff` class, since I want all my new instances of `Cookie` to share the same methods as my `BakedStuff` class.

Let me try it:

```ruby

class Cookie < BakedStuff
  attr_reader :cookies_made

  def initialize(name, cookies_made, baking_temperature=375)
    super
    @time = "20 min"
    @cookies_made = cookies_made
  end

  def bake_for
    super + " This recipe will yield #{cookies_made} cookies."
  end

end
```

Whoa! New code! 

- That `<` sign is what we use to dennote inheritence. All it means is that my `Cookie` class is now *inheriting* all the methods from my `BakedStuff` class.
- Notice that this class has some additional features, including a new instance method which sets the `cookies_made`.
- But wait, what's that `super` thing doing in there??


## WHUT is `super`?

The `super` keyword in Ruby is, well, pretty super. Okay, okay, what it *really* does is allow you to call methods up the "inheritence heirarchy", which means that you can use methods that were perviously defined in classes that this class now inherits from.

In fact, `super` is what allows me to pull down everything from my `initialize` and `bake_for` methods in my parent class, which in this case is my `BakedStuff` class. You'll also notice that I've reassigned my `@time` variable to equal 20 minutes, and that I've added a string interpolation into my `bake_for` method. And I didn't have to retype any of the code from my parent class in the process!

Let's look at what an object of the Cookie class could look like:
```ruby
cookies = Cookie.new("cookies", 15, 400)
puts cookies.bake_for
```

would output: `To make cookies, bake for 20 min at 400 degrees fahrenheit. This recipe will yield 15 cookies.`

Okay, one last example. Let's say that my favorite kind of cookie is a Snickerdoodle. I want it to inherit from its parent class, `Cookie`, but I also want to jazz the `Snickerdoodle` class up a little bit. I'm going to change its `bake_for` method and make a little counter that will tell me exactly how many snickerdoodles I've got left.

This would look something like:

```ruby
class Snickerdoodle < Cookie
  def initialize(name, baking_temperature=375)
    super
    @time = "25 min"
  end

  def bake_for
    while cookies_made > 0
    puts "There's only #{@cookies_made} snickerdoodles left!"
    @cookies_made = cookies_made.to_i - 1
    end
  end

end
```

Now let me actually create a batch of snickerdoodles:
```ruby
snickerdoodle = Snickerdoodle.new("snickerdoodles", 10)
puts snickerdoodle.bake_for
```

which outputs:
```
There's only 10 snickerdoodles left!
There's only 9 snickerdoodles left!
There's only 8 snickerdoodles left!
There's only 7 snickerdoodles left!
There's only 6 snickerdoodles left!
There's only 5 snickerdoodles left!
There's only 4 snickerdoodles left!
There's only 3 snickerdoodles left!
There's only 2 snickerdoodles left!
There's only 1 snickerdoodles left!
```

Now for the tricky part. What do you think will happen if I create a new batch of cookies in my `Cookie` class? Do you think I'll get a cookie counter? Do you think my `Cookie` class will be affected by my `Snickerdoodle` class? Let's try it and see:
```
chocolate_chip_cookie = Cookie.new("chocolate chip cookies", 24)
puts chocolate_chip_cookie.bake_for
```

which outputs: `To make chocolate chip cookies, bake for 20 min at 375 degrees fahrenheit. This recipe will yield 24 cookies.`

And what about if I create a new pie?
```
pie = BakedStuff.new("pie", "45 min", 450)
puts pie.bake_for
```
outputs: `To make pie, bake for 45 min at 450 degrees fahrenheit.`

Interesting. It doesn't seem to matter at all that I changed my `Cookie` class -- `BakedStuff` stayed the same. And all the edits I made to my `Snickerdoodle` class didn't fall up to my `Cookie` class, either. That's because inheritance only works in one direction, from the parent class, down to its children.

I should also mention that `super` didn't change my parent classes either; all it did was bring the already-created methods from my parent classes down to its children. It's actually passing all the arguments to the parent class' method.

Finally, it's crucial to remember that a class can only inherit from one class at a time. Something like `Snickerdoodle < Cookie < BakedStuff` would have raised an error. Also, remember that instance variables have nothing to do with class inheritance, since they are not defined by the class itself!


## tl;dr?

* Class inheritance is an incredibly powerful tool. Use it.
* The `super` keyword can save you a ton of time. Remember that it only works with methods of a class, and *not* instance variables. Read more about it [here](http://rubylearning.com/satishtalim/ruby_inheritance.html) and [here](http://rubylearning.com/satishtalim/ruby_overriding_methods.html)
* Cookies just make everything better (just in case it wasn't obvious already).