# Making your views stupid

Hi! I'm Matt Button. I'm a core developer on the kohana framework project and
I'm studying computer Science at the University of York.

Today I'm going to be talking about making your views stupid, or rather

# Making your views stupidly simple

Simple code is good code.  it's easy to write, easy to debug and easy to maintain.

What do I mean by simple? I mean keeping your templates as isolated as possible from 
your PHP code.

# PHP is a programming language

The more you mix markup with logic the harder it is to adapt your system down the line.

Anyone who's watched a couple of PHP projects over the past decade will realise this.

# ASAP ASAP ASAP

In the beginning PHP (aka Personal Homepage Tools) was a tool for creating dynamic websites
as quickly as possible. As we all know, the more you rush things, the more mistakes you make.

After a while people started realising that trying to do business logic in the middle
of a template isn't the greatest of ideas, especially when you need to maintain the project!

# Smarty

After a while templating languages come along promising to make life easier by keeping the
two domains separate... except that it doesn't!

You end up refactoring some of the logic out of your templates but because Smarty's syntax
is basically a funky version of PHP's you still get logic creeping into the templates.

Hell, you can even embed PHP in Smarty templates, thus eliminating any possible benefit!

#MVC

Finally, MVC makes its way into the PHP ecosphere. People are told to take business
logic out of their views and controllers and put it in their models.

However there are lots of little bits of logic that creep into templates. Things
such as working out how many news items to display in a sidebar or which parser
should be used to render the user's post all make it harder to re-use templates and
keep them simple for maintaining down the line.

This kind of logic doesn't really belong in your model (because it's related to
how the app looks, not how it works) and doesn't belong in your controller (because
the controller should only be concerned about receiving the request and passing
the requested data to the view). So how do we solve this problem?

# Mustache slide

This is where Mustache comes in. Mustache is a templating language whose syntax only
allows looping, basic if/else conditionals, including partial templates and echoing
data. No assignments, macro/function definitions, no calling methods with parameters.

So where does all the logic go? Well, it's all stored in an object called a View Model,
which sits between the View and the Controller.

# AWESOME SLIDE SHOWING THIS

would sit between the View and the Controller/Model.

It's based on a Design Pattern by Microsoft called the "Model View ViewModel" pattern.
If you want to find out more, wikipedia is your friend!

# SOME SLIDES ON MUSTACHE SYNTAX

Now imagine that you're working on a site for... beer lovers(!) who can register
on the site and add their favourite beers.

You've been tasked with writing the user profile view which needs to display
the user plus their favourite beers.

Using Mustache your controller could look like this:

# Controller slide

All we have to do is create a new instance of the view model, pass the requested
user to it, and then call render. Nice. simple. code.

As you can see here, I'm creating a new Mustache object to which I pass my view object.
Now this is obviously a bit cumbersome and I usually use a library called Kostache
which keeps the Mustache invocation within the view class itself, so you can just do:

Now imagine our template looks like this

# Template Slide

All we're doing here is echoing out the user's name and looping over their favourite beers.

I know it's terrible, but we can get away with it because we're developers - 
the designers can worry about making it pretty!

The Mustache syntax is pretty simple, just looking over it you can probably guess
what things are doing. A nice thing I'd like to mention is that Mustache
automatically escapes all output, very handy for avoiding XSS exploits.

The view model itself looks something like this:

# View Slide

Any methods or public variables defined in the View are available to the template.

As I can only fit about 10 lines on a slide I've used public variables for 
simplicity, but if you want type hinting you could easily assign your own
accessor methods.

Here the beers method gets a set of all the beers the user likes and returns it
to the template.

# Ways we can improve this example

As you probably noticed, the template has direct access to the model, which depending on
how your models work could mean your templates can call state changing methods such as delete,
update etc.

Also the status method is in the wrong place. By defining it here it's only available
to templates that use that view class. In order to use this method in different areas
we need to copy & paste it everywhere, or refactor it into a different class.

So to solve these problems we need to create some kind of barrier between the model
and the template, which is essentially what the Decorator pattern does.

We can then refactor the view class like so:

# View class refactored

The status method has been replaced with a `user` method which returns an `User_Decorator`
instance wrapped around the model. Now you may have noticed that the new method and the
public variable `$user` user share the same name.  When there is a choice between a 
member variable and a method then Mustache will always call the method over the variable.

Now the `User_Decorator` looks like this:

# User Decorator slide

The constructor accepts an instance of `User` and the `__get` / `__isset` magic methods
are used to proxy requests for variables back to the model. These methods should really
be defined in a parent class to make the implementation more re-usable, but I've defined
them here to make it clear what's going on.

The status method works the same as before, except that it uses the `$_model` member
variable instead of the `$_user` one.

All we need to do now is adapt the template to use the model decorator:

# Refactored template

All we've had to do is tell Mustache to call status within the scope of the user object.

# Advantages of using Mustache

Parent language agnostic - If you write mustache templates for your site they'll
work in PHP, Javascript, Ruby, Python and so on.

Output is automatically escaped - Makes it extremely easy to prevent XSS attacks

Because you've de-coupled your logic from the presentation layer you can unit test
it to make sure your templates are receiving the data they expect!

And if you're one of those agile types you can also use view classes to generate
documentation on what variables are available to your templates!

# In conclusion

## Notes

http://nicksda.apotomo.de/2011/10/rails-misapprehensions-helpers-are-shit/
http://techportal.ibuildings.com/2010/11/02/creative-mvc-meet-the-viewmodel-pattern/
