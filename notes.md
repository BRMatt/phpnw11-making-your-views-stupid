# Making your views stupid

Hi! I'm Matt Button. I'm a core developer on the kohana framework project and
I'm studying computer Science at the University of York.

Today I'm going to be talking about making your views stupid, or rather

# Making your views stupidly simple

Simple code is good code.  it's easy to write, easy to debug and easy to maintain.

Nowadays a lot of projects follow the MVC design pattern to try and keep things as DRY
as possible, but back in the day, everything was thrown together in a huge mess.

After a while people start realising that trying to do business logic in the middle
of their template isn't the greatest of ideas, especially when the client wants the
site to be redesigned.

So when template languages such as Smarty come along promising to end all those problems
people naturally jump on the bandwagon.

Unfortunately Smarty doesn't really solve the problem as it's essentially PHP
with a funky syntax. Sure, the business logic is /partially/ separated from presentation
logic, but there's still a lot of stuff left in there.  Hell, you can even embed
php within the smarty templates, thus removing any theoretical benefit of using
Smarty.

Finally, MVC makes its way into the PHP ecosphere. People are told to take business
logic out of their views and controllers and put it in their models.

However there are lots of little bits of logic that creep into templates. Things
such as working out how many news items to display in a sidebar or which parser
should be used to render the user's post all make it harder to re-use templates and
keep them simple for modification down the line.

This kind of logic doesn't really belong in your model (because it's related to
how the app looks, not how it works) and doesn't belong in your controller (because
the controller should only be concerned about receiving the request and passing
the requested data to the view). So how do we solve this problem?

# Mustache slide

This is where Mustache comes in. Mustache is a templating language whose syntax only
allows looping, basic if/else conditionals, including partial templates and echoing
data. No assignments, macro/function definitions, no calling methods with parameters.

So where does all the logic go? Well, it's all stored in an object called a View Model,
which if you were to look at a diagram of the MVC pattern,

# AWESOME SLIDE SHOWING THIS

would sit between the View and the Controller/Model.

It's based on a Design Pattern by Microsoft called the "Model View ViewModel" pattern.
If you want to find out more, wikipedia is your friend!

Now imagine that you're working on a site for... beer lovers(!) who can register
on the site and add their favourite beers.

You've been tasked with writing the user profile view which needs to display
the user plus their favourite beers.

Using Mustache your controller could look like this:

# Controller slide

All we have to do is create a new instance of the view model, pass the requested
user to it, and then call render. Nice. simple. code.

Now imagine our template looks like this

# Template Slide

All we're doing here is echoing out the user's name and their favourite beers.

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

Here the `favourite_beers` method uses the assigned model to fetch the user's
favourite beers.

PROBABLY RUNNING OUT OF TIME ABOUT NOW.

## Notes

http://nicksda.apotomo.de/2011/10/rails-misapprehensions-helpers-are-shit/
