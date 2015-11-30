mighty_hacks 2012-02-04-muahahahaha.md

# a simple markdown blogging approach

this is a demo for a simple markdown-based blogging approach.

this approach uses two files, stored in your own dropbox folder.

the first file is an .html "template" file, which also contains .css (to format the blog posts) and .js (javascript, to administer the posts).

the second file contains the blog posts themselves. for this demo, i borrowed the blog entries written by nikolay ??, which can be found at his github repository which presents his own software. 

> http://google.com

i combined all of nikolay's posts into a single file -- which is the approach being demonstrated here -- and that file is located here:

> http://google.com

(you'll notice that this post is found at the top of that file.)

the u.r.l. for the .html template file is summoned by someone who wants to read the blog, and the javascript in that file then (in turn) downloads the file which contains all of the posts. these posts are in markdown format.

use the left-cursor-key and right-cursor-key to switch from one post to the next.





mighty_hacks 2012-02-04-muahahahaha.md

# Muahahahaha!

I'm raising my hand into the night sky, and my sardonic laughter is rolling and bouncing among those dark, gloomy buildings. You almost can see how lighting is going to strike down into the glass ceiling above.

But, it's freaking -20C outside. Zero chances for a thunderstorm. Besides, I don't really have a glass ceiling in here.

So, maybe some other time, and at some other place...





mighty_hacks 2012-02-08-how-to-make-auto-resizable-textarea.md

# How To Make Auto-Resizable Textarea

It seems that not everyone knows how to make a textarea element which will automatically resize itself so that the text fit into it without scrolling. The usual CSS hacks don't work in case of textareas and to make this feature work, you need to do some hackery around.

In my version of the hack I'll be using [lovely.io](http://lovely.io), which is my pet project. It has mostly jquerysh syntax so it should not be a problem to reproduce the code on any other system.

## So, How It Works?

The idea is actually pretty simple. All you need is to create a `DIV` element that will have all the same styles as the textarea itself, this includes fonts, margins, paddings and borders. Then you set the same width but leave the `height: auto`. And finally move this div outside of the visible area, with `position: absolute`

After that you hook up to the `keyup` event of your textarea, and every time the user hits a key you copy text from textarea into the div, get its size and apply it to the textarea itself.

## Lovely Basics

[Lovely.IO](http://lovely.io) is kind of a port of the [RightJS](http://rightjs.org) library, which is built on modular architecture, AMD and that sort of stuff. So to make it running you'll need to make a construction kinda like that

```html
<script src="http://cdn.lovely.io/core.js"></script>
<script type="text/javascript">
  Lovely(['dom'], function($) {
    // And here is your code
  });
</script>
```

This basically gets the `lovely.io` core, and then uses asynchronous modules definition to load the DOM handling module, which is kind of jQuery looking thing.

## Show Me The Code

Now to the actual code. A simple brut force solution might look say like that

```javascript
$(function() { // dom-ready
  var textarea = $('#my-textarea')[0];
  var meter    = $('<div></div>')[0];

  // some default styles
  meter.style({
    position:   'absolute',
    top:        '-999999em',
    left:       '-999999em',
    border:     '1px solid grey',
    overflow:   'none',
    whiteSpace: 'pre',
    wordWrap:   'break-word'
  });

  meter.insertTo(textarea, 'after');

  // copying the styles
  meter.style(textarea.style(
    'font-size,font-family,width,'+
    'margin-top,margin-left,margin-right,margin-bottom,'+
    'padding-top,padding-left,padding-right,padding-bottom'
  ));

  // adding the actual resizing
  textarea.on('keyup', function() {
    meter.html(this.value() + "\n\n");
    this.style({height: meter.size().y + 'px'});
  });
});
```

Firstly, we make that `DIV` element with default styles. The crucial thing is not to forget the `overflow: none`, `white-space: pre` and `wordWrap: break-word` settings so that the `DIV` mimicked a `TEXTAREA` text behavior.

Then we copy styles from the `TEXTAREA` element (coma separated style names return a hash of the styles in lovely.io). And finally we tap into the `keyup` event of the textarea to make it automatically resize. The only tricky thing here is to add those `"\n\n"` two new line chars at the end of the text, just to eliminate possible lags on text wrapping and give the user some sense of empty space below the cursor.

## Ninja Style

That's the whole thing really. But if you need to make more than one textarea autoresizable, or say you have all sorts of textareas in our project, some of them are autoresizable, some aren't. In this case you might want to look into the custom dom-wrappers functionality of [Lovely.IO](http://lovely.io) which was migrated from the [RightJS](http://rightjs.org) project.

OO DOM-wrappers in [Lovely.IO](http://lovely.io) are pretty simple. It's just a collection of classes that wrap the original dom-elements and allows you to do all sorts of fancy things, like custom methods and inheritance.

So, in our case we could just inherit the `Input` class, which in the `dom` module handles all the input fields, and then incapsulate the auto-resizing functionality in it.

```javascript
var AutoTextarea = new Class($.Input, {
  constructor: function(raw_element, options) {
    this.$super(raw_element, options);
    this.meter = this.make_meter();
    this.on('keyup', 'auto_resize'); // RightJS style call by name
  },

  auto_resize: function() {
    this.meter.html(this.value() + "\n\n");
    this.style({height: this.meter.size().y + 'px'});
  },

  make_meter: function() {
    return $('<div></div>')[0]
      .style({....});
      .insertTo(this, 'after');
      .style(this.style('....'));
  }
});
```

As you can see, we simply overloaded the original constructor and made it to create meter `DIV` and hook it up to the `keyup` event. Once you've got this class, you can use it in all sorts of ways. For example the usual `dom:ready`

```javascript
$(function() {
  $('textarea.autoresizable').forEach(function(input) {
    if (!(input instanceof AutoTextarea)) {
       // rewrapping it in your own class
       new AutoTextarea(input._);
    }
  });
});
```

Or you could use events delegation and lazily instantiate your class when the textarea gets focused

```javascript
$(document).delegate('textarea.autoresizable', {
  focus: function() {
    if (!(this instanceof AutoTextarea)) {
      new AutoTextarea(this._);
    }
  }
});
```

I think that's the whole story. Enjoy! :)





mighty_hacks 2012-02-12-heroku-cedar-lazy-assets-compilation-fix.md

# Heroku Cedar Lazy Assets Compilation Fix

In case you're running a #Ruby-on-Rails app with #MongoID on #Heroku and all your attempts to make the lazy assets compilation work are futile, cheer up my friend, coz we've got a proper hack for you!

Basically the issue looks like that, you switch on lazy assets compilation in your `config/application.rb` file, but when you try to deploy on heroku, you see an error like that in your deployment log

```
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       rake aborted!
       Failed to connect to a master node at localhost:27017

       Tasks: TOP => environment
       (See full trace by running task with --trace)
       Precompiling assets failed, enabling runtime asset compilation
```

This issue is quite simple. Heroku just don't run their database for you when you merely try to compile your assets with the `rake assets:precompile` command. And as MongoID is trying to connect to the database when rails environment is loaded, you've got the crash.

## The Hack

I believe the fix is on the way and [Durran Jordan](https://github.com/durran) should make it work in the next gem update, meanwhile you can use the following hack. Place this into your `config/application.rb` file below the `require` commands (before your app configuration)

```ruby
if (ARGV[0] || '').slice(0,17) == 'assets:precompile'
  module ::Mongoid
    def self.load!(config_file)
      puts "Skipping connection to Mongo DB"
    end
  end
end
```

This basically stubs the database connection when you run any of the `assets:precompile` tasks.

__NOTE__ don't place it in a ruby file in the `config/initializers` directory. MongoID checks the database connection before any of your initializers are called.

That's pretty much it. After that, your deployment log shoulda look like that

```
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       Skipping connection to Mongo DB
       Skipping connection to Mongo DB
-----> Rails plugin injection
```

Enjoy!

PS: Check this [Aleksey Gureiev's blog post](http://blog.noizeramp.com/2011/10/14/rails-3-asset-pipeline-on-heroku-when-using-mongoid/) he described the issue first and deserve a dozen of kudos! Although the hack described in there doesn't work for everyone  :)


## Update

Seems like in Rails 3.2 they had added a special config option to handle the issue

```
config.assets.initialize_on_precompile = false
```

You need to add this into your `config/application.rb` file





mighty_hacks 2012-02-16-how-to-customize-file-upload-field-with-css.md

# How To Customize File Upload Field With CSS

You know what is the problem with the `input type="file"` fields. They are ugly. More of that, they are ugly differently in different browsers (yes, mozilla, I'm talking about you). But the worst part is that you can't really do much with them by using #CSS.

Some folks try to hide file fields with `display: none` and then fiddle with them by using JavaScript, but it doesn't work either, because there is a whole bunch of limitations for the `type="file"` input fields to prevent no good people from snatching your files. You can't assign the `value` property, you can't even trigger it to show the file choosing dialog without the user clicking directly on the input field.


## So, How Do We Hack It?

The solution is actually quite simple. The trick here is to create a `DIV` which will play the role of your pretty button and put the file input inside of it along with the inner text. Say kinda like that

```html
<div class="button">
  <input type="file" />
  Upload a file!
</div>
```

After that, you need to add a bit of CSS hackery and make the file input to be absolutely positioned over the main text, and then hide it with `opacity: 0`.

```css
.button {
    position: relative;
    padding: .1em 1em;
    background: #DDD;
    border: 1px solid #AAA;
    display: inline-block;
    overflow: hidden;
}

.button input[type="file"] {
    position: absolute;
    left: 0px;
    top:  0px;
    width: 100%;
    height: 100%;
    opacity: 0;
    z-index: 2;
    cursor: default;
}‚Äã
```

There are several important moments you need to keep in mind. Firstly, you have to use `opacity: 0`, not `visibility: hidden` option, because the other way it won't be accessible. Secondly, you need to add the `overflow: hidden` for the button element itself, because in Firefox you can't really change the size of a file input and it will stick out of your button, probably covering some other elements on your page. And finally, don't forget the `z-index: 2` so that when a user clicks the button, he clicked the input field, not the text below it.

That's basically the hack. You can see the [live demo](http://jsfiddle.net/6ASWA/) at [jsfiddle](http://jsfiddle.net).


## Known Issues

Also keep in mind that in Firefox, you can't really change the height of a file input. You kinda can, but weirdly enough it will change the height of the button part of the widget, the text part of it will still be relatively small. So, don't make your button too tall, otherwise there will be empty spaces above and below it and clicks might miss the hidden field.

Also in the same Firefox, you can't change the width of a file input by using CSS, so if you're planning of making some wide fake input field, you should set something like `size="99"` on your hidden input right in your HTML. Then Firefox will make a large field and it will cover your whole widget.

That's the whole thing. Enjoy!





mighty_hacks 2012-03-01-how-to-center-a-block-vertically-with-css.md

# How To Center A Block Vertically With CSS

Sometimes you need to position one #HTML block in the center of another one, and you need to do that both horizontally _and_ vertically. The closest example would be a modal dialog

```html
<div class="screen-locker">
  <div class="dialog">
    Hello world!
  </div>
</div>
```

Horizontal alignment is quite simple, you just use the old trick with `text-align` and `display: inline-block` #css properties

```css
div.screen-locker {
  text-align: center;
}
div.dialog {
  display: inline-block;
  text-align: left;
}
```

but vertical alignment is a bit tricky one. You can't just say `vertical-align: center` on your dialog element, because `vertical-align` says how to align sibling elements, not how a particular element should be aligned inside of it's parent element.

Some people try to solve the problem with JavaScript by calculating the dialog element's position from it's height and available free space. But there is actually a very simple pure css based hack for that.

## The Hack

The hack is pretty simple. All you need to do is add one more hidden element in your html code

```html
<div class="screen-locker">
  <div class="aligner"></div>
  <div class="dialog">
    Hello world!
  </div>
</div>
```

The purpose of this element is to center the dialog element vertically. The trick is that you make it have the height of `100%` and then align it vertically with the dialog element, say like that

```css
div.screen-locker {
  text-align: center;
  position: relative;
}
div.aligner {
  display: inline-block;
  width: 1px;
  height: 100%;
  vertical-align: middle;
}
div.dialog {
  display: inline-block;
  text-align: left;
  vertical-align: middle;
}
```

and because the aligner DIV will have the 100% height it will center the dialog DIV vertically as well. No need for any JavaScript voodoo, it will stay perfectly in the center and will get repositioned automatically if the browser window got stretched.

Enjoy!





mighty_hacks 2012-03-15-lightweight-redirects-with-rack-in-rails.md

# Lightweight Redirects With Rack In Rails

Sometimes, when working with #ruby-on-rails, you need to do some permanent redirecting, say you might support some legacy routes, like

```
/about.php -> /company/about
```

Or, you might want to redirect between domains, say

```
www.theosom.com -> theosom.com

http://theosom.com/auth -> https://auth.theosom.com
```

There are quite a few cases like that, and in case of rails applications, people tend to put this sort of functionality either in the routes mapping or in more complex cases in the `ApplicationController`, say like that

```ruby
class ApplicationController < ActionController::Base
  before_filter :handle_redirects

protected

  def handle_redirects
    if something
      redirect_to some_url
      return false
    end
  end
end
```

It works, but that's not the best solution. Firstly, it's relatively heavy, you boot your entire rails application just to make some simple static redirect and because your application might have all sorts of before filters and so on, it might get pretty expensive.

Secondly, it overcomplicates your application controllers logic. Rails controllers implement the [action controller pattern](http://takacsot.freeblog.hu/Files/martinfowler/actionController.html) (by [Martin Fowler](http://www.martinfowler.com/)) and have the specific purpose of handling _actions_, meaning clicks on links, buttons and things like that. Permanent redirects are not actions, they are part of what's called URL routing layer and shouldn't be in there.

# So How Do I Do It The Right Way?

Luckily for us #ruby-on-rails supports #rack applications and that's a good place to organize your permanent redirects, caching and that sorts of stuff, things that wrap your application in general.

Rack apps is pretty simple idea, it's just a collection of middleware between the web-server and your rails application. Rails itself uses quite a few of them, if run the `rake middleware` command in your rails application console, you'll see a list like that

```
¬ª rake middleware
use ActionDispatch::Static
use Rack::Lock
use Rack::Runtime
use Rack::MethodOverride
use ActionDispatch::RequestId
use Rails::Rack::Logger
use ActionDispatch::ShowExceptions
use ActionDispatch::DebugExceptions
use ActionDispatch::RemoteIp
use ActionDispatch::Reloader
use ActionDispatch::Callbacks
use ActionDispatch::Cookies
use ActionDispatch::Session::CookieStore
use ActionDispatch::Flash
use ActionDispatch::ParamsParser
use ActionDispatch::Head
use Rack::ConditionalGet
use Rack::ETag
use ActionDispatch::BestStandardsSupport
use Sass::Plugin::Rack
use Rack::Mongoid::Middleware::IdentityMap
use OmniAuth::Builder
```

Every one of rack apps is basically a wrapper class over your rails app like that

```ruby
class SomeClassName
  def initialize(app)
    @app = app  # your actual rails app instance
  end

  def call(env)
    if some_stuff_needed
      # sending a custom response
      [http_status_code, http_header, http_body]
    else
      # processing with the original rails call
      @app.call(env)
    end
  end

  def each(&block)
    # this method is used for post rails-app calls handling
    # in our case it can be empty
  end
end
```

The `#call(env)` method is called by rails _before_ the actual rails application kicks in, meaning before any controllers or routing calls, and all it receives is the raw HTTP environment hash.

This is a pretty lightweight situation, you didn't instance or process almost anything yet and you can quickly redirect your browser without bothering the actual application with those kinds of stuff. More of that your application doesn't even need to know about existence of those redirects, it can just handle the standard scenario and let your rack filters to adjust the call for it.

# The Actual Solution

Okay, enough of the theoretical, stuff. Now when you know how the guts work, the solution is pretty simple. Just write a new rack middleware class somewhere in your `config/initializers`. For example, my simple `apex -> www.` redirector

```ruby
class WwwRedirectar
  def initialize(app)
    @app = app
  end

  def call(env)
    request = Rack::Request.new(env)
    if request.get? && request.url.split('/')[2].split(':')[0].split('.').size == 2
      # making permanent redirect from apex to www. subdomain
      [301, {"Location" => request.url.sub("://", "://www.")}, self]
    else
      @app.call(env)
    end
  end

  def each(&block)
  end
end
```

And hook it up in your `config/application.rb` file like that

```ruby
config.middleware.use "WwwRedirectar"
```

__NOTE:__ you need to use the _string_ name of your class, not the class itself, coz when the config is loaded it doesn't know about your class yet.

Also, you can add your middleware in `config/environments/production.rb` if you need it in production only.

That's basically the whole solution, but you can do much more fancy stuff with rack apps. Check out for example [Ryan Bates](http://twitter.com/rbates) screencasts [1](http://railscasts.com/episodes/151-rack-middleware) [2](http://railscasts.com/episodes/203-routing-in-rails-3) [3](http://railscasts.com/episodes/222-rack-in-rails-3), that should give you some ideas





mighty_hacks 2012-03-23-imageless-icons-in-css.md

# Imageless Icons In CSS

This is not the kinda stuff you should do in production, but there is a little #CSS trick that might help you.

The problem with us developers is that it's much quicker and easier for us to write couple of lines of code than find an appropriate image and place it on a page. If that's about you, then here is a little css hack for you that will make your life easier.

Say you make all sorts of action links on your page, thins like `edit`, `delete`, `add new` and so on. Normally you would just add related css classes to them like so

```html
<a href="/stuff/new" class="add">New stuff</a>
<a href="/stuff/123/edit" class="edit">Edit stuff</a>
<a href="/stuff/123" data-method="delete" class="delete">Delete</a>
```

The basic idea is that you create some semantically correct links in your #HTML and then paint them with CSS. Often people use icons and so on

```css
a.add {
  padding-left: 20px;
  background: url('/icons/add.png') no-repeat left center;
}
```

The problem is that you will need to bother with the icon images, drag around a collection of some default pictures or something like that and it usually a bit annoying because in early stages of development it doesn't matter what you put in there, later on your boss will hire a designer who will screw everything anyways.

To save yourself a bit of time and feel more hardcore, you actually can use a simple css hack with the `:before` constructions and utf-8 symbols. Like that

```css
a.add:before {
  content: "\u271A  ";
}
a.edit {
  color: gren;
}
a.edit:before {
    content: "\u270E  ";
}
a.delete {
  color: red;
}
a.delete:before {
    content: "\u2718  ";
}
```

Those weird numbers are the UTF-8 codes of various symbols. You can find them in the Internet, or if you're under OSX, then you can open up the character viewer, find an UTF-8 icon you like and get your code from the character info. In the end it will look kinda like that

![utf-8 based imageless icons](http://zenmagiclove.com/misc/rocks/utf-8-symbols-tBVN.png)

The nice thing about this approach is that it's really quick to implement. You just get an UTF-8 code from the character viewer, paste it in your CSS and that's it.

Later on, when you have an actual design, you always can replace those things with proper images.

__NOTE__: your page have to be in UTF-8 encoding, otherwise you'll see squares in place of the icons





mighty_hacks 2012-03-29-css3-gradient-buttons-the-right-way.md

# CSS3 Gradient Buttons The Right Way

Do you like the picture below? Do you want to know how to make those things in pure #CSS the right and reusable way?

![](http://zenmagiclove.com/misc/rocks/all-images-lWD7.png)

People make CSS3 gradient buttons for a while now, but the problem is that web-designers tend to just google some css3 gradient generators, then enter some colors in there, then copy paste some giant piece of auto-generated css. Which is kinda fine until you want to change a color or add an icon, or reuse that pail of code in any way.

Luckily for us, there is always a better way to do things.

## Show Me Some Code

The trick to good CSS3 gradients is to use `rgba` colors instead of the usual `rgb` or `hex` values. You define a semi-transparent gradient layer for your buttons, say kinda like that

```sass
.button
  background-image: -webkit-linear-gradient(rgba(255,255,255,0.7) 5%, rgba(255,255,255,0) 70%, rgba(0,0,0,.05) 85%)
  background-image: -moz-linear-gradient(rgba(255,255,255,0.7) 5%, rgba(255,255,255,0) 70%, rgba(0,0,0,.05) 85%)
  background-image: -ms-linear-gradient(rgba(255,255,255,0.7) 5%, rgba(255,255,255,0) 70%, rgba(0,0,0,.05) 85%)
  background-image: -o-linear-gradient(rgba(255,255,255,0.7) 5%, rgba(255,255,255,0) 70%, rgba(0,0,0,.05) 85%)
  background-image: linear-gradient(rgba(255,255,255,0.7) 5%, rgba(255,255,255,0) 70%, rgba(0,0,0,.05) 85%)
  background-color: #eee
```

As you can see, I used three levels, a fully transparent color in the middle, white semi-transparent on top and dark semi-transparent at the bottom. This will produce a nice modern plastic-like semi-transparent gradient over your button.

![](http://zenmagiclove.com/misc/rocks/gray-buttons-kquX.png)

Btw, don't use just two colors, that will look glossy like windows vista and all designers will lough at you.

The great thing about this approach is that you can reuse this layer everywhere and you don't need to go back to a CSS gradient generator every time you want to change the background color. You can specify it directly in CSS as a normal color!

```sass
.button

  // gradients are in here

  &.red
    background-color: #ecc

  &.blue
    background-color: #cce

  &.green
    background-color: #cec
```

This will produce buttons like those

![](http://zenmagiclove.com/misc/rocks/color-buttons-X8Oj.png)

Pretty neat, huh?

You also might need to define special styles for active, disabled and hovered versions of buttons, but that's again just adjustments in the semi-transparent layer we defined in the very beginning, you won't have to change the actual `background-color` property of your button in every pseudo-class.

## Adding The Icons

And finally, lets add some icons on the buttons. I already described [in this post](http://theosom.com/p/s5cV) how you can make imageless icons in #CSS, but if you want icons exactly like on the first picture, here is what you can do

```sass
.button-add, .button-edit, .button-delete
  padding-left:  2.5em
  padding-right: .8em

  &:before
    border-right: inherit
    background-color: rgba(0,0,0,0.1)
    position: absolute
    top: 0
    left: 0
    height: 100%
    text-align: center
    line-height: 1.3em

.button-add:before
  content: "\u271A"

.button-edit:before
  content: "\u270E"

.button-delete:before
  content: "\u2716"
```

After you add both `button` and say `button-add` classes, the icon will appear on your button. It's that simple.

## What About Old Browsers?

One of the nice things about this approach is that it automatically falls back gracefully. Because you already specified the `background-color` and the `border` attributes correctly, the old browsers will just skip the gradients and show flat buttons. Say here is how it looks like in IE9

![](http://zenmagiclove.com/misc/rocks/ie9-lrZG.png)

In more older browsers there won't be the icons and round corners, but it still will be a normal gray button that will look and behave like a normal button.

If you want the full version of the css code, go and check the [lovely.io ui core project](https://github.com/MadRabbit/lovely.io/blob/master/ui/core/main.sass) [lovely.io](http://lovely.io) is an HTML5 centric project and we use those semi-transparent gradients in there as a default solution to generate the basic collection of buttons, icons and that sort of stuff.





mighty_hacks 2012-04-05-converting-numbers-to-currency-in-javascript.md

# Converting Numbers To Currency In JavaScript

Just so I didn't forget it later on, here is a quick tip #how-to convert numbers into currencies in #JavaScript

```js
function number_to_currency(value, show_cents, symbol) {
  var string = '', chunk; symbol = symbol || "$";

  if (show_cents) {
    string = "." + value.toFixed(2).split('.')[1];
    value  = Math.round(value);
  }

  while (number < 999) {
    chunk  = (number % 1000).toString();
    while (chunk.length < 3) { chunk = "0"+chunk; }
    string = "'"+ chunk + string;
    number = Math.floor(number/1000);
  }

  return symbol + number + string;
}
```

Enjoy!





mighty_hacks 2012-04-06-what-are-oo-dom-wrappers-and-why-are-they-so-awesome.md

# What Are OO DOM-Wrappers And Why Are They So Awesome

This post is more for youngsters and intermediate #JavaScript developers. To get it you'll need to know how #OOP in #JavaScript works and what [prototype-based programming](http://en.wikipedia.org/wiki/Prototype-based_programming) is.

## Sooo... What's Up?

To get to the point I need to write a bit about the history of javascript frameworks. In the good old days scripts were simpler. There was this framework [Prototype](http://prototypejs.org), it was called this way because it was simply screwing with the `HTMLElement` class prototype like so

```js
HTMLElement.prototype.addClass = function(name) {
  this.className += " "+ name:
};
```

It was fun, coz all those methods were available immediately after you get access to your dom-element, say through `document.getElementById` or something like that. Unfortunately, when there were more than one library screwing with the native class prototypes, things tend to go south really quickly.

In a while people figured that it is not cool anymore and [jQuery](http://jquery.com) raised. To be fair, [dojo](http://dojotoolkit.org) and [Ext](http://www.sencha.com/products/extjs) did it way before #jQuery, but anyhow. This is how jQuery essentially works

```js
var jQuery = function(css_rule) {
  var elements = find_elements_by(css_rule);

  for (var i=0; i < elements.length; i++) {
    this[i] = elements[i];
  }

  this.length = elements.length;
};

jQuery.prototype.addClass = function(name) {
  for (var i=0; i < this.length; i++) {
    this[i].className += " "+ name;
  }
};
```

Well, it's a bit more complicated than that, but basically it's a plain JavaScript class, which mimics an Array. It stores references to a list of elements inside itself and then loops through every dom-node in all the methods that manipulate with something.

This approach solves the problem of prototype pollution, because all the methods stored in the `jQuery` object, and it also makes it fairly simple to extend the framework with plugins.

The problem with jQuery-like frameworks is that they are great for simple cases, when all you need is to do some basic dom-manipulations or make some ajax-requests. But when you want to write some complex logic, it turns out that `jQuery` is just a flat list of functions, much like `PHP` which has the same exact problems. The code gets messy, you always shovel things into the `$` function, and plugins tend to screw with jQuery itself by adding more and more methods into jQuery's prototype.

## The OO DOM-Wrappers

The idea of object oriented dom-wrappers derives from the jQuery like frameworks, but instead of making a flat list of methods that loop through a list of elements, oo dom-wrappers wrap every single dom-element separately. In the essence it looks like that

```js
var Element = function(raw_dom_element) {
  this._ = raw_dom_element;
};

Element.prototype.addClass = function(name) {
  this._.className += " "+ name;
};
```

This might not look like a lot of a change, but in reality it changes the development paradigm completely. See, now instead of writing spaghetti code that use a flat list of _functions_, you can use proper inheritance features.

For example, you might have a separated class for handling web-forms, which will inherit the basic `Element` functionality and extend it with the form specific features

```js
var Form = new Class(Element, {
  send: function() {
    // sending the form via AJAX
  },

  values: function() {
    // collecting the form values
  }
});
```

You obviously can inherit this generic class further and say make some other, application specific forms that can validate themselves

```js
var MySmartForm = new Class(Form, {
  send: function() {
    if (this.isValid()) {
      this.$super(); // making a parent class call
    }
  },

  isValid: function() {
    return this.values().name != false; // not blank
  }
});
```

On every level of inheritance you have access to all your parent class functionality and you can extend and overload it in any way you like.

## What Does It Give You?

The crucial thing about having classes and inheritance is that now you can properly structure your code. Every complete logical block can be written in a separated, highly maintainable class with great deal of reusability.

No more writing spaghetti code. With classes you can organize your code in a proper way, have code structure, methods, use software design patterns, create proper APIs for your application and use unit testing for development.

No more screwing around with the system. When you use OO dom-wrappers, you don't pollute the system namespace, in fact you don't touch the core system at all. Every new class, widget or extension builds a new, completely separated branch of functionality. This way plugins can safely coexist and reuse each other.

## What Else Is There?

Besides the big-boy OO toys, using dom-wrappers instead of jquery-like collections processing gives you a bunch of other nice effects.

First of all, it's plainly faster. [RightJS](http://rightjs.org), which pioneered OO dom-wrappers, in various [head to head comparisons](http://rightjs.org/benchmarks) goes way faster than jQuery in almost every imaginable situation.

And there is a good reason for that. Because OO dom-wrappers don't copy lists of elements and they don't go through all of them in every method. And because in normal life you manipulate with just one or two elements, no time is wasted on looping through them.

Secondly, the result code is much smaller. Because methods in OO dom-wrappers do just what they supposed to, there is no code for constant looping through lists of elements and as the result, #RightJS, while having practically all the same functionality as #jQuery, weights almost half of it (yes, gzipped).

And thirdly, because all the subclasses use the same basic `Element` class, everything and everywhere has the same API. It doesn't matter whether you have a calendar, color picker or a lightbox they all have the same standard collection of methods and you can `hide()`, `show()` or `addClass()` on all of them without even going into the documentation. That saves a lot of time on digging around and wondering how a third party plugin called this method and that feature. All of them have standard and familiar API.

## The Conclusion

OO dom-wrappers is a very simple concept, which can give you a great deal of change in the way you develop #JavaScript applications. It doesn't make much of a difference in small and simple cases, but when you develop complex interfaces, oo dom-wrappers can give the very things that were inherently missing in JavaScript, a proper code structure and API standardization.

And that my friends is a lot.





mighty_hacks 2012-04-19-squeeze-those-baby-unicorns.md

# Squeeze Those Baby Unicorns!

One of my #ruby-on-rails apps that runs on #heroku had problems with performance. Yeah, we did all the usual mumbo-jumbo with caching and optimizations, but still had some bad days.

One of the problems was that we were running on just 2 dynos under the `thin` server, and as we have some relatively heavy operations going on, pretty often requests started dog-piling screwing everything else in the application.

So, [unicorn](http://unicorn.bogomips.org), it's this magic web-server that runs and balances several instances of itself on the same socket. To put it simple, things like `thin` or `webrick` receive a request, handle it and then write back into a socket, #unicorn on the other hand forks itself and runs several processes to handle requests coming from the same socket.

Usefulness of this feature of unicorn depends on the kind of tasks your application performs. If you handle a lot of heavy requests, then you won't win much, because it doesn't really matter how you spend your CPU cycles limit. But if you have a mixed load or a lot of lightweight stuff with a lot of concurrent users, then unicorn might be very helpful because it can let you squeeze more concurrency out of the same CPU limits.

If you wanna know more about unicorn from smarter people than I'am, then check [this post](https://github.com/blog/517-unicorn)

## How To Set It Up

Michael van Rooijen wrote [this excellent post](http://michaelvanrooijen.com/articles/2011/06/01-more-concurrency-on-a-single-heroku-dyno-with-the-new-celadon-cedar-stack/) about setting it up on heroku. I'll just summarize it in here.

Firstly replace `thin` with `unicorn` in your `Gemfile`

```ruby
gem 'rails', '3.2.3'

# gem 'thin'
gem 'unicorn'
```

Then, save this into the `config/unicorn.rb` file

``` ruby
worker_processes 3 # amount of unicorn workers to spin up
timeout 60         # restarts workers that hang for 60 seconds
```

Michael suggests to use `4` workers, but in my app it was a bit unstable, so I settled on `3`. Plus, I've changed the timeout option to `60` seconds because I have some heavy requests in there.

And finally, replace the `thin start` call in your `Procfile` with `unicorn`

```
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
```

That's basically it, but if you run #new-relic-rpm you also might wanna add this into one of your `config/initialize` files

```ruby
NewRelic::Agent.after_fork(:force_reconnect => true) if defined? Unicorn
```

That's all, now you can bundle it up and push to heroku.

## The Result

In my case, I've got a mix of heavy requests that process images and lightweight calls for cache, so one of the main problems were dog-piling calls.

![my app on heroku with two web dynos under thin servers](http://zenmagiclove.com/misc/rocks/before-Zuir.png)

After I replaced `thin` with `unicorn` that spawned 3 workers, the picture changed to this one

![same app running on two dynos under 3 unicorn workers](http://zenmagiclove.com/misc/rocks/after-A6u1.png)

I think there is not much else to add. If you have a rails app hosted on heroku and you run out of other options, give it a go, that might help you as well.





mighty_hacks 2012-04-27-pointer-events-is-awesome.md

# Pointer Events Is Awesome

While I have the urge to share this little #css trick. Lets say you have a list of stuff, and by `stuff` I mean a list of mixed content entries. Sorta like this one

![a dummy list of stuff](http://zenmagiclove.com/misc/rocks/1-y87O.png)

And if you're wondering, no, I'm not making a porno site, just thought it might get you interested in the topic :)

So, you have a list of stuff, and as with any list of stuff, at some point, you want to highlight the current item on the list. Assuming you have only the `.current` class on one of the items, how do you do that?

```css
li.current {
  background: pink;
}
```

Surely, that might work, but if you call yourself a proper #css junkie, you certainly wouldn't feel satisfied with that. Lets think bigger and fancier, how about making a little overlay with the `:after` selector? Say, like that

```css
li {
  position:   relative;
}
li.current:after {
  content:    ' ';
  position:   absolute;
  left:       0px;
  top:        0px;
  width:      100%;
  height:     100%;
  background: rgba(255,255,200,0.5);
}
```

That sounds fancy isn't it? That actually looks fancy too. Check it out

![list of stuff with css generated overlay](http://zenmagiclove.com/misc/rocks/2-HSd5.png)

You can just tint the whole thing with some fancy color, maybe even make some nice looking gradients with `rgba` colors as I described in [one of my previous posts](http://theosom.com/p/A07g). There are quite a few options open up.

One problem though, it won't work. (hey, i'm trying to make some narrative in here!) This css generated layer will block all the content underneath itself, and if some poor bastard will try to click one of the pretty lady pictures or any links on a highlighted entry, the click will land on the generated overlay and nothing will happen.

Luckily for us, there is this awesome css property called [pointer-events](https://developer.mozilla.org/en/CSS/pointer-events), which basically defines how elements should react on mouse/touchscreen events, and if you set it to `none` like so

```css
li.current:after {
  .....
  pointer-events:none;
}
```

All the mouse events (including clicks and hovers) will fall through it, just like if this element didn't exist. I've made you a little demo, [check it out](http://jsfiddle.net/G6CwN/)!

But, you know how those stories with miraculous escape work. In the end, the evil, after being defeated, crawls down in some dark hole and waits for the sequel.

Despite of all the potential awesomeness `pointer-events` [at the moment](http://caniuse.com/pointer-events) supported only by chrome, safari and firefox. I would gladly tell IE users to go fuck themselves, but `pointer-events` is not supported by any mobile browsers yet.

So, the story is to be continued...





mighty_hacks 2012-04-29-how-to-force-re-rendering-in-a-browser.md

# How To Force Re-rendering In A Browser

Normally you don't need this sort of things, browsers are pretty smart those days, but once in a blue moon, when you have a complex #html layout and go too fancy with #css, especially if you deal with a slow mobile browser, you might see some artifacts, things might get stuck here and there and so on.

And if you see the artifacts go away after you resize the browser window a bit, you might start wondering "how can I force the bloody thing to re-render stuff programmatically?"

My friend, the answer is actually pretty simple

```js
var block = document.getElementById('my-ugly-block');
var dummy = document.createElement('div');

block.appendChild(dummy);

window.setTimeout(function() {
  if (dummy.parentNode === block) {
    block.removeChild(dummy);
  }
}, 1);
```

The idea is obvious, you insert a dummy element into the block of interest and then remove it from there. But there is a couple of important moments.

Firstly, you should remove the dummy element with the `window.setTimeout` call, coz #javascript is single threaded, this will finish up the current scenario and let the browser a chance to render stuff. If you place those commands one after another, nothing will happen. The browser will change it's internal model back and fourth, but won't render the changes.

Secondly, don't forget about that `.prentNode ==` check, if you make the re-rendering calls repeatedly, things might get screwed because you have the async call in there, and those things executed by the system not exactly in the same order they were originally placed.

That's about it. Have fun!





mighty_hacks 2012-04-30-super-simple-fixed-width-layout.md

# Super Simple Fixed-Width Layout

And just for the kicks, the super-simple fixed-width layout. No extra tags required :)

```html
<html>
  <body>
    <h1>Muahahaha!</h1>

    <p>
      Fixed-width layout with no extra tags!
    </p>

    <style>
      html {
        text-align: center;
      }
      body {
        display:    inline-block;
        width:      1024px;
        text-align: left;
      }
    </style>
  </body>
</html>
```

#html, #css, #how-to





mighty_hacks 2012-05-03--webkit-transform-is-useful.md

# -webkit-transform is useful!

The `-webkit-transform` option is hardly a news for people who deal with #css on daily basis. But developers tend to think about it more like a toy "yeah, I can rotate and skew stuff, so what?". Meanwhile, there is one awesome thing in `-webkit-transorm` that can be really useful in everyday life of a front-side developer.

I'm talking about the `-webkit-transform: translate(x,y);` option. All it does is it moves stuff horizontally and vertically, and because of its simplicity people tend to completely miss it, because you can do kind of the same thing with say margins, and absolute/relative positioning.

But, as they say in #Thailand "same same, but different" (sorry i'm having too much fun in here :)), the catch is, that unlike other #css options, `translate` transformation doesn't screws with your layout.

With `-wekbit-transform: translate(x,y);` you can move any element in any position, without breaking the current layout, and it doesn't matter what kind of margins, or positioning you element has, inlined or not, it will be moved to exactly that specified amount of pixels vertically and horizontally.

This is extremely useful for all sorts of animations when elements on your page supposed to slide some way. The trick is, that now you can separate your layout from the visual effects level. You make a nice solid layout and then smoothly move stuff around by simply adding a #css class.

[Here, I've made a little demo](http://jsfiddle.net/j46yL) on jsfiddle. You've got a simple block and then move it around with a simple #keyframe animation.

Such approach with defining animations through css and separated classes gives you a great deal of flexibility. Firstly, you can simply move your animations in css classes an reuse them everywhere. Secondly, you can change your visual effects without digging into the javascript layer. And thirdly, because it's just a css class you can degrade gracefully by simply redefining those classes content in IE patches.

And finally, consider that `-webkit-transform` is hardware accelerated on mobile devices and works much faster than if you'd move stuff around with the usual `position` and `margin` options. In case of complex content with css shadows and semi-transparent layers, that might make a great difference.

That's about it. Give it a try, you're gonna love it!





mighty_hacks 2012-05-07-getting-help-with-lovelyio.md

# Getting Help With Lovely.IO

I'd like to share a bit of the [lovely.io](http://lovely.io) practical awesomeness. #LovelyIO is my pet project, on which I've been working for the last year. In simple words, it's a modern, async and #html5 centric version of the [RightJS](http://rightjs.org) library.

At the moment, lovely.io reached the point where I use it on two of my real life projects. It still a bit raw and edgy, but it's so awesome in practical applications that I should share it :)

## So, What's The Hell Up With Lovely.IO?

Lovey.IO is not exactly a #javascript framework. It's actually a service, sort of like [rubygems](http://rubygems.org) but for the front-side modules. This service is based on the [AMD](http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition) architecture, which basically looks like that.

```html
<html>
  <head>
    <script src="http://cdn.lovely.io/core.js"></script>
    <script>
      Lovely(['dom', 'ajax', 'cookie'], function($, ajax, cookie) {
        $('#my-stuff').html("Boo hoo!");
      });
    </script>
  </head>
</html>
```

Basically, you load the `core` package from the centralized lovely.io CDN host (it's located on amazon's cloudfront service). Once it's loaded (just 3k gzipped), you can _asynchroniously_ load any packages via the `Lovely` interface and then use them on the inside of the callback function.

And those micro-modules form some sort of an ecosystem, where modules can be reused by each other and live like one happy family.

## Lets Build Something Useful

I'd like to show you some real-life example. The other day I need to add a `Help` button next to some complicated form, that button supposed to open up a popup dialog with some help text.

The #HTML looks kinda like that

```html
<form ...>
  <a href="#" class="help" data-css="#the-help">Help</a>

  Form internals go in here

  <div id="the-help" style="display: none">
    The actual help text
  </div>
</form>
```

You basically have a link and a text that supposed to appear in a popup.

## Hooking Up LovelyIO

Lovey.IO already has basic [ui-core](http://lovely.io/packages/ui) and [dialog](http://lovely.io/packages/dialog) packages, so we can start making things right away

```javascript
Lovely(['dom-1.2.0', 'dialog-1.1.0'], function($, Dialog) {
    var help_dialog = new Dialog();

    // initializing the dialog object
    help_dialog.title("Help info");
    help_dialog.addClass('my-help');
    help_dialog.on('ok', 'hide');

    // catching all the `a.help` clicks
    $(document).delegate('a.help', 'click', function(event) {
        event.preventDefault();

        // updating the dialog with the content and showing it up
        help_dialog.update($(this.data('css')).html());
        help_dialog.show();
    });
});
```

As you can see, firstly we ask `Lovely` to load `dom` and `dialog` modules. `Lovely` interface automatically handles all possible internal dependencies, so you just ask which modules you need.

The `Dialog` unit is just a normal #javascript class, you can instantiate it the usual way. And, as it's inherited from the basic `dom.Element` interface, you can simply `addClass` or bind an event listener to it. For those of you who's familiar with #rightjs this should be pretty much familiar.

And finally, we delegate all the `a.help` clicks on the page to a little function that updates the dialog content and shows it up to the user.

That's all there really is. Lovely IO automatically delivers all the scripts, stylesheets, related images and so on from the centralized CDN hosting. All you need to do is to write your actual application code.

As per usual, I've made you [this little demo](http://jsfiddle.net/pKSz8). Enjoy!





mighty_hacks 2012-05-28-can-you-draw-a-triangle-in-pure-css.md

# Can You Draw A Triangle In Pure CSS?

That's a fun trick, you can draw a triangle figure with pure #css without using CSS3 magic with `transform: rotate`. More of that it will work everywhere, even in IE6. Well, sort of :)

When you get it, it's actually really simple

```css
#triangle {
  width:              0px;
  height:             0px;
  border:             100px solid transparent;
  border-left-color:  blue;
  border-right-width: 0px;
}
```

The trick is in making a box of `0/0` size, then you can make a large border, one side of which will look like a tringle.

One tricky moment though. Keep the border sizes equal, otherwise the triangle edge will look pixelated.

Here, try this little demo at [jsfiddle](http://jsfiddle.net/2RYJe)

Enjoy! :)





mighty_hacks 2012-05-29-awesomize-your-app-with-font-awesome.md

# Awesomize Your App With Font Awesome

In case you haven't heard, raster-graphic sucks, png sucks, png icons suck in particular. Yeah, I know, deep down we all have a soft sport for [glyph icons](http://glyphicons.com) because of their uberglorious black and white colors. But things have changed.

## The Problem

See, back in the good old days, when IE6 was rocking the minds of youngsters, pixels were square. If you had an image of 100x100 pixels in size, every pixel in this image would be displayed with a _single_ physical pixel on your display. But truth is, that pixels that your browser knows don't correspond to physical pixels in 1:1 ratio. Pixels in a browser is a _relative_ measure.

The simplest example is in your pocket. Retina displays of iDevices basically render every pixel in your browser with four physical pixels. So, if your image has size 100x100 pixels, on the device level it will allocate 200x200 pixels of your screen.

The problem is that on the system level those devices will approximate missing pixels and the raster graphics will and do look smudged all over the screen. It's good if you have sharp edges, then it might look okayish, but if you have any sort of gradients, it's gonna look awful.

## The Solution

The solution came from a bit unexpected direction. People figured that you don't need to invent any sort of new format for that, no need to go crazy and use SVG, instead you can make a custom font, which instead of normal letters will have a set of icons.

If it sounds dodgy, relax, it's not. Github started to use this feature a while ago and replaced all their raster icons with their own font, some other sites (including this one) did so as well. And now there is an open-source fonts available for that.

One of the most awesome, called [Font Awesome](http://fortawesome.github.com/Font-Awesome). It has decent set of icons (which grows constantly) and it has proper community support. Go check their site, they have all nice documentation and examples in there.

## So What?

In case you're thinking "fuck it, it's too complicated, I'll just make ugly squarish icons and no one will notice difference", think again. Using fonts instead of raster graphics not just solves the problem of different DPI in screens, it actually opens up a lot of options.

## Because It's Fucking Font!

Every icon is essentially a letter, so you can say change colors and backgrounds of your icons directly from your #CSS. More of that, you can apply things like text shadows, rotate icons, change colors dynamically from your application scripts, and best of all you can use css transitions to make it change smoothly!

Here's what I've got in one of my own applications, it's all done in #css + font-awesome.

![-](http://zenmagiclove.com/misc/rocks/1-Pp97.png)

That's about it. Don't hesitate, give it a try, coz all cool kids gonna start doing it pretty soon.





mighty_hacks 2012-06-07-test-faster-with-bcrypt-hacks.md

# Test Faster With BCrypt Hacks

If you're using `BCrypt` in your #ruby-on-rails application and then feel like your #rspec tests run forever, here is a quick tip on how to make your life a bit easier.

Place this into the `spec/support/bcrypt.rb` file

```ruby
#
# Making BCrypt to use the minimal cost so that the specs run faster
#
require 'bcrypt'

module BCrypt
  class Engine
    class << self
      alias :_generate_salt :generate_salt

      def generate_salt(cost = MIN_COST)
        _generate_salt(MIN_COST)
      end
    end
  end
end
```

Basically when you pick any serious size of the salt for production use, it makes things very slow when you run specs, because FactoryGirl (or whatever you use) will generate production grade salt every time you spawn a new model instance. And as it's normally the `User` model, it gets generated a lot, and slows things down.

So, this little hack, makes BCrypt always use the minimal salt size when you run tests regardless to what you set in your actual code. In case of tests it usually doesn't matter anyways.





mighty_hacks 2012-06-14-lovelyio-ui-20-whats-new.md

# Lovely.IO UI 2.0 Whats New?

I finally published the [lovely.io ui-core 2.0](http://lovely.io/packages/ui/changelog) today, time to reflect a bit on what's going on in there.

## Post RightJS

As you might now, #lovelyio was started as an #HTML5 centric fork of #rightjs, so you won't be mistaken if you thought that lovely.io UI is a continuation of the same idea. I'm trying to build a new kind of UI with marvels of the modern technology in mind.

RightJS has the [rightjs-ui](http://rightjs.org/ui) library, but didn't really have a centralized ui-core. It kinda did, but internally, and pieces of it were compiled inside of every module. And as lovely.io allows easy packages dependency handling, there was no reason not to start building one ground-level block upon which all the rest of the packages will grow.

## What's Inside?

With lovely.io ui-core i certainly didn't want to build a jquery-ui like mastodon, in reality i ended up with something close to [twitter bootstrap](http://twitter.github.com/bootstrap) but a bit simpler and with a proper #javascript API.

Essentially there is a set of scripts and styles to build standard-looking buttons, menus, spinners and so on, and a basic class for all sorts of modal dialogs/popups. It's all modular and OOP based, so you can build everything programmatically

```js
Lovely(['ui-2.0.0'], function(UI) {
  var button = new UI.Button();
  var icon   = new UI.Icon('save');

  var Lightbox = new Lovely.Class(UI.Modal, {
    constructor: function(options) {
      this.$super(options);
      this.update('some internal block code');
    }
  });
});
```

There are also several every-day use things like lockers, html-based spinners, menus and etc.

```js
Lovely(['ui-2.0.0', 'dom-1.2.0'], function(UI, $) {
  $('#some-element').on('click', function() {
    this.append(new UI.Locker());
    this.load('/some.url');
  });
});
```

As per usual, the package carries all the necessary styles, so all you'll have to do is to build your stuff and be happy.

## Font Based Icons

Lovely.io UI-core 2.0 comes with a built in set of icons, which is based on the [FontAwesome 2.0](http://fortawesome.github.com/Font-Awesome) font-based icons set.

Font-based icons were chosen, firstly because they are DPI independent and look excellent an any screen, and secondly you can do all sorts of stuff with them by using CSS magic.

Font-based icons might be a bit of a problem to setup, but, lovely.io and the ui-core package take care of everything automatically, all you get in the end is a great set of icons which you can easily use in your apps.

## Human Friendly Keybindings

Lovely.io ui-core also provides certain extensions to help you handle UI related keyboard events. Which basically means that, when you hook up the `ui` package you can specify keyboard combinations instead of the event names

```js
Lovely(['dom-1.2.0', 'ui-2.0.0'], function($, UI) {
  $(document).on('esc', function() {...});
  $('#input').on('enter', function() {....});
  $('#something').on('ctrl-s', function() { ... });
});
```

No more routine `event.keyCode == 13` checks!


## What's Not In There

Lovely.io ui core was conceived as a ground level package. Which means there is no real widgets in there, all real things are in the actual packages, see for example [dialog](http://lovely.io/packages/dialog), [zoom](http://lovely.io/packages/zoom), [tabs](http://lovely.io/packages/tabs)

Then, when I say lovely.io is HTML5 centric, I mean that. There is no `IE < 9` support and there won't be any. I know, i know, but.

1) Supporting old IE is thankless job, and no-one want to do extra work because of lazy ass sysadmins or cheap-skate office owners
2) Without old IE browsers support, we can go crazy doing super-awesome sweet CSS3 things
3) Codebase gets much cleaner and flexible
4) IE 10 is coming

LovelyIO is a project oriented on the future projects, so there won't be anything less than HTML5 and CSS3, get used to it.

## Is It Ready?

It is. I already use it on two of my latest projects in production. I don't have a gazillion of users (yet :)) but I use it every day, and because lovely.io allows me to get through the build-measure-learn cycle as quickly as possible, things get updated and fixed regularly.

lovely.io is new and unusual, but it's not raw. lovely.io STL is based on well tested RightJS codebase and ready for early adopters.

And soon we're going to start rocking the world! :)





mighty_hacks 2012-06-20-vertical-align-in-pure-css.md

# Vertical Align In Pure CSS

Okay, one more take on the old as the world problem. How to align one block inside of another one with pure #CSS?

```html
<div id="wrapper">
  <div id="content">Bla bla bla</div>
</div>
```

I already described the trick, with one extra element that aligns the inner one, but you actually can achieve the same with CSS generated elements via the `:before` call

```css
#wrapper {
  position: relative;
  text-align: center;
}

#wrapper:before {
  content: ' ';
  display: inline-block;
  height: 100%;
  width: 0px;
  vertical-align: middle;
}

#content {
  display: inline-block;
  vertical-align: middle;
}
```

Basically the trick is quite simple, you just generate an element which has `height: 100%` style and then align it with the content block by using `vertical-align: middle` property on both of them. And as the tall one will take the whole vertical space, the smaller one (with the content) will align against it in the middle.

Cheers!





mighty_hacks 2012-06-21-the-magic-clearboth-trick.md

# The Magic clear:both Trick

Okay, now raise your hands who of you in your web-developer's career had to do that?

```html
<div class="container">
  <img src="tall-image.jpg" style="float: left">
  Smaller than the image text

  <div style="clear: both"></div>
</div>
```

I'm pretty sure that 99% of you at some point had to add that dummy DIV with `style="clear: both"` so that the floating block didn't stick out of the main container.

My dear friend, did you know that you don't have to do that? You can happily have clean content like so.

```html
<div class="container">
  <img src="tall-image.jpg" style="float: left">
  Smaller than the image text
</div>
```

And then generate that dummy div with #CSS!

```css
div.container:after {
  content: '';
  display: block;
  clear:   both;
}
```

Check it out, i've made you [this little demo](http://jsfiddle.net/JfaPH)!

## Browser Support

This hack will work in IE8 and upper (http://caniuse.com/after)

If you need to support older browsers, try add `*zoom: 1;` to the container element, that should have the same effect.





silly_thoughts 2012-07-16-procrastination.md

# Procrastination

![](http://zenmagiclove.com/misc/rocks/1-zxP4.png)

Have you ever though why people procrastinate so much those days? I mean, sometimes it goes beyond ridiculous, you might spent 4 hours procrastinating over 20 minutes of work. And it's not like you're lazy or burnt out, you just can't focus.

I've got a theory that it happens because people's life doesn't depend on their immediate actions anymore.

See, at some point in the history of humanity, life started to be actually interesting. In good old days when people were running after each other with axes you could live till 30 and say "ugh, fuck it, i saw it all and it's all the same", but now, thanks to the marvels of technology, life changes all the time, human condition changes all the time and it's interesting what will happen next.

So, people started to want to see the future, people started to want to live longer, people started to value their lives more than anything else. And because of that, people started getting rid of everything that might have even a slight danger to life.

That's a good thing for sure, but it also removes the most powerful stimulus to be creative. Things people do those days are mostly far from being life threatening to anyone, so they think they have time. And because the law of minimal energy consumption applies everywhere, people don't do shit until it's getting critical.

The conclusion is simple. Wanna stop procrastinating, find something that's important, something that will change your life. That will keep you focused.





silly_thoughts 2012-08-02-be-the-batman.md

# Be The Batman

I think most of sane people realised by now that invention of the Internet is the most important and profound thing that happened to humans. (yeah i'm going this way).

No, seriously, the previous undoubtedly ‚Ññ1 event was the invention of printing press. That allowed mass spread of information and changed humanity forever. The invention of Internet is the same thing, but gazillion times more powerful and hence gazillion times more important.

All, absolutely all future human endeavours will be based on the ability of people to communicate via the Internet. If tomorrow people invent a cure for cancer, that will be because of the Internet, if they kick Putin out of the Kremlin, that will be, non the less, because of the Internet.

Now my friend, think of it. What's the biggest drag on the Internet development? What stops this, the most important to the humanity thing to grow and reach it's potential at the moment?

That's the old Internet Explorer browsers. Not lazy ass sys-admins, not cheap skate company managers, not a god forsaken post office in the middle of Siberia with only one 20 years old IBM PC.

Now, you all been to the batman's movie, and you all heard the message "tolerant society is an excellent soil for parasitic crime types to grow".

No, i'm not agitating for burning Gotham City to the ground. I'm saying that you as a developer is socially responsible for the technology you develop. It's time to realise that civilians won't fight the crime. It's time to realise that we, the developers, are the police in this Browsers City, we are the vigilante, we are the batmans. If citizens of the city tolerate the old browsers, that doesn't mean they like it, that doesn't mean that old browsers are good for the society at all.

So, stop moaning "we have 10% of IE6 users"! Stop complaining that "the customer wants IE6 support"! Go out and kick the shit out of the parasitic browsers, that is your job and your responsibility!

And if you don't have the guts to do that, maybe you shouldn't be on the force in the first place?





book_worm 2012-09-04-neuromancer.md

# Neuromancer

Just finished the [neuromancer](http://en.wikipedia.org/wiki/Neuromancer) and have to say, I didn't like it that much. Yeah, i know it's dickish of me to say thing like that about an iconic work, but let me finish.

I do love cyberpunk as a next guy, and i do recognise neuromancer as an archetypical work in the field. And i did enjoy all the little funny references to the matrix, zion and the little Johnny story. That's all good fun, but i didn't like the book as a novel.

Although there are some pretty cool characters work in there, but most of them luck sense of motivation. Yeah, Case wanted back into the cyberspace, but did he really? He just pressed a bunch of buttons, jacked out and that's it, all he did in reality he was moaning about Linda and inability to get drunk. The rest of the bunch are just emotionless characters that did their thing.

And the storyline itself was rather boring, it quickly becomes obvious that it's an epic journey type of scenario, so the main character, just like a bunch of hobbits, will get to the volcano, drop the ring, get back, marry their high-school sweethearts and drink booze happily ever after.

And above all, it doesn't read well. It's really hard to get through all the descriptions of the environments.

## The Followups

What's interesting about the book is followup thoughts that it gives you. Like for example, there was this AI, it had a Swiss citizenship, but the hardware where it was running, belonged to a private corporation, which is kind of obvious but interesting idea.

So, in a while, when all our concisenesses will be uploaded to a large computer and we ride into the sunset for all the eternity, who will own the hardware? And how much will it cost to live there? And how much it will cost to get cured of viruses in there? and so on.

Which immediately brings you to the question, to whom your hardware belongs now?





mighty_hacks 2012-09-13-lovely-160-what-up.md

# Lovely 1.6.0, What Up?

Hey folks, I've just updated the [lovely.io](http://lovely.io) NPM package to the version `1.6.0`, now some words about what's going on in there.

## Lovely Core in Node

The first major change is that #lovelyio core package is now available for use from #nodejs. That obviously doesn't mean that you can load `dom` and `ui` stuff into node environment, coz why would you? But most of the `core` package is pretty much standard and the set of its utils can be useful in node as well as on the front side.

```coffee
Lovely = require('lovely')

MyClass = new Lovely.Class,
  include: Lovely.Events,

  constructor: ->
    # bla bla bla
```

Basically, if you want it to be put in other way, that's the #rightjs NPM package + ~2 years of development.


## Standard Package Testing Utilities

But the real reason why the new release was made is that now we have standard package testing utilities out of the box.

When I started with lovely.io couple of years ago, the field of console based testing was rather undeveloped and most of the STL packages were tested with quite a bunch of home made helpers and hacks.

Nova days we have [mocha](http://visionmedia.github.com/mocha) and [zombie.js](http://zombie.labnotes.org) stable and widely accepted by public, it's time to get more serious about how we test packages.

So, now lovely.io #NPM package comes with a built in set of testing utilities and when you generate a new project with the `lovely new something` command, you'll get the `test/something_test.coffee` file generated for you. At the moment it will look kinda like that

```coffee
{Test, assert} = require('lovely')

describe "Something", ->
  Something = null

  before Test.load(module, (obj)-> Something = obj)

  it "should compile", ->
    assert.ok Something.version
```

Basically the `Test` object wraps up all the standard testing kitchen. It builds the current package, runs a little #express test server, generates a dummy HTML page, loads everything up, then pulls your package's exported object and provides the object for mangling with it in the tests.

You obviously can create your own HTML pages for testing, make macros, access the zombie browser and it's DOM environment and do all other sorts of fancy stuff with it if you need. If you need more advanced examples, you can check tests at the [lovely.io DOM package tests](https://github.com/MadRabbit/lovely.io/blob/master/stl/dom/test/unit/form_test.coffee)

The whole point of this exercise is to provide developers with a simple, standard and painless environment for automated packages testing in the terminal, rather than relay on devs clicking stuff in the browser like a bunch of school girls.





silly_thoughts 2012-09-17-you-aint-no-artist.md

# You aint no artist

I think programmers are often mislead into believing in their own artistic nature and then suffer from lack of ideal things in the world.

Yeah, software development does often need inspiration and lots of skill, but so does almost any field of work that involves creativity. And involvement of creativity doesn't mean art, coz one of the main distinct features of art is that it doesn't have any practical purpose.

See, if you would join a bunch of random jquery plugins so that they formed a face of a clueless hipster, that would be art. Coz the purpose of the construct would be to inspire and promote the idea. But if you're creating an actual app that has a real life purpose, then you're just a worker.

In most cases software development is not even an engineering, it's more like work of an electrician or a plumber. So, when a toilet doesn't flush and a plumber would go for several days on a search for an ideally shaped wrench made by NASA, what would you think of him?

Yes, my dear friend, that's you when you're looking for an ideal, bugles, google/apple approved library to put into your project while your client waits and nervously bites his nails. And same goes the other way around, if you're a client that hired a professional and then asks him to use some rarely used library that has the "enterprise" label on it, you also do the stupid thing.

I know, i know, getting from an artist to a plumber is quite a downshift, but you know what they say: "if you wanna be an artist, you gotta be a plumber too", and that's exactly what's going on in the software development. If you want to be an artist, that's cool, go to github and have fun, but if you have a real project, be a professional. Don't sit around waiting for an inspiration, just get the things done with what you've got and move on.





mighty_hacks 2012-09-28-fixing-npm.md

# Fixing NPM

[NPM](http://npmjs.org) and [Node.js](http://nodejs.com) is a mess. Everyone who was around for couple of years knows how those bastards screw everyone over every time they make a new release.

The new version of #NPM is not an exception https://npmjs.org/doc/folders.html, basically they say you either install a package locally (meaning dump it in your project's folder) and then you can `require()` it or, if you need it as a binary executable, you should install it globally, but then you won't be able to call it programmatically with `require()`

If your package does both things, like my beloved lovely.io, coffee-script and so on, you can either install it twice or go fuck yourself.

Yes, coz they're fucking Rembrandts and we're taking their brushes away by asking them to the sane thing.

## Hack It!

Well, enough bitching about it. Lets hack it and make the bloody thing work properly

Assuming you install #nodejs with `brew`

Step 1. add `global=true` into your `~/.npmrc` file
Step 2. nuke the `~/.npm` and `~/.node_modules` folders
Step 3. add `export PATH=/usr/local/share/npm/bin:$PATH` into your `~/.bash_profile` file
Step 4. add `export NODE_PATH=/usr/local/share/npm/lib/node_modules:$NODE_PATH` there too
Step 5. run `source ~/.bash_profile` in your terminal or open up a new one

Reinstall everything you need and love with the `npm install` command.

Now you can use packages both as bins and libs from a single repository.





silly_thoughts 2012-09-29-unhappiness.md

# Unhappiness

Okay, before we start a quick disclaimer, if you're getting through a rough patch or having some genuine bad stuff going on in your life, i might say some things that might sound offensive, don't take it personally. Those are just some silly thoughts that were nagging me for a while. Also, my condolences.

I believe that unhappiness is addictive. There are some legit cases for being unhappy about life, but I think 99% of unhappy people are just bitching about stuff and use it as an excuse for the shit they do.

You see, happiness requires strength and responsibility. Yeah, i know, it sounds a bit stupid, but if you think of it, the practice of being unhappy comes from childhood, if a kid smart enough and his parent care enough, the kid might realise pretty quickly that being unhappy is a good way to get attention or stuff he wants.

And that's really all there is about serial unhappiers, they grow up but still do this vicious cycle of shit: get unhappy, maybe someone will get you the stuff you want. And if you can't get what you want you always can keep being unhappy and use it as an excuse to get waisted, yell at someone or do whatever people normally do when they're unhappy.

The problem is that, this kind of shit doesn't make people happy, that's not even a point there. It's all about finding an excuse to do the easy thing. Coz happiness is hard, it requires work, achievements and discipline in what you want from life.

Yeah, i know, i talk silly, but I see it every day. Back in Russia it was like the whole god dammed country is like that: "yes we russians, we drink, coz life sucks and coz we're russians and nobody likes us". In reality that's a bunch of bullshit, if you don't wear babushka and don't eat borsch for breakfast no one give a fuck where you're from. After all we are all god's children, as the constitution says.

And it got even clearer when I got to the Thailand. You see, Buddhists they figured this stuff long before Romans invented Christianity. The rule number one is simple as that: "unhappiness is when you dumbly want stuff". And you can see it everywhere here. People at large don't wanna want stuff, they'd rather be moderate and happy.

As a rather radical example, there is a hobo lives down the street, every night he comes at the same phone booth next to a local 7/11 and sleeps there. So the other day I was walking home from that 7/11 with a pack of beers in my hands, and I was in a good mood, and I saw this dude making himself comfortable in the telephone booth. He never begged for stuff, always looked friendly and smiled, so i figured, I'll give him one of my beer cans, just to cheer up the dude a bit. Guess what, he didn't wanna take it. He smiled, bowed but didn't want to take it. Thanks god there was some security guard passing by, he translated that it was a gift not a dole.

--

Anyhow. Stop bitching, do stuff, get results, be happy.
Peace!





silly_thoughts 2012-10-04-open-source-is-a-game.md

# Open Source is a Game

Long time ago, when young Linus started hacking his first version of linux kernel, open source was like this little nerdy rebellion against corporate UNIX. And for a quite a long time it was the dominant idea, FSF meant OS, proprietary software companies fighted it then tried to lead it and everything looked exactly how it supposed to be.

Then open source stopped to be this Che Guevara fetish for computer science geeks, open source become a tool of professionals. Most of the modern frameworks, like Rails and Django were created that time. People found this simple model how to build high quality tools where everyone would benefit from their openness.

And as open source become a dominion of professionals, business immediately become interested in it, which consequentially brought attention of young developers to open source projects as a way to prove themselves  and therefore have better chances for finding jobs in future.

As the topic become hot, lots and lots of developers came to open source and now we find us in this situation when there are often too many contributors and gazillion clones of projects that do the same thing with minimal differences.

And you know what? I think open source at this point become some sort of a sport. It's not a rebellion against evil corporations anymore, more of that, it's not entirely about building something useful either, those days it's more like a competition for attention. Who gets more commuters wins the race. Simple as that.

And if you think about it, it's a good thing, revelating actually. If you get the constrains of building something useful off your shoulders, if you say forget about supporting old browsers, or stop worrying about compatibility, programming might be pretty fun thing to do.

You just need to stop pretending that you're saving the world by playing the game.





mighty_hacks 2012-10-08-caching-friendly-authentication.md

# Caching Friendly Authentication

Today's post is about authentication, giving a user access to control elements and doing so in caching efficient way. I'll use #rails and #lovelyio as examples, but the principle should be applicable to any platform/framework.

## The Problem

```erb
<div class="comment">
  <p class="text"><%= @comment.text %></p>

  <% if admin? or @comment.user == current_user %>
    <p class="controls">
      <%= link_to :edit, edit_comment_path(@comment) %>
      <%= link_to :delete, @comment, method: :delete %>
    </p>
  <% end %>
</div>
```

We all did that `if admin?` thing. Still do, right until the moment when

```erb
<p class="text">
  <%= @comment.text %>
</p>
```

Becomes, something like

```erb
<p class="text">
  <%= something_really_slow @comment.text %>
</p>
```

Then you start using the `cache` helper and make things more complicated

```erb
<div class="comment">
  <%= cache "this_slow_stuff_#{@comment.id}" %>
    <p class="text">
      <%= something_really_slow @comment.text %>
    </p>
  <% end %>

  <% if admin? or @comment.user == current_user %>
    <p class="controls">
      <%= link_to :edit, edit_comment_path(@comment) %>
      <%= link_to :delete, @comment, method: :delete %>
    </p>
  <% end %>
</div>
```

And then your boss come and starts saying: "hey my dearest developer, lets make some really slow stuff in the `comments_controller` too". And here you start pulling your hair off. You'd love to use `caches_page`, compile everything once in HTML and then serve it to the user bypassing #rails, but that bloody thing

```erb
<% if admin? or @comment.user == current_user %>
  <p class="controls">
    admin links in here
  </p>
<% end %>
```

Still requires you to run it through the rails just to make those stupid links appear and disappear for the right folks.

And here people tend to start getting a bit crazy, use memoisation of the slow processes in controllers, build crazy schemes of nested caching mechanisms, build rack apps to include those links in precompiled blocks of HTML and so on.

Have you ever wondered why people go in such troubles over such a simple thing?

All this memoization, PJAX, TurboLinks, all those things come up because of one simple thing, they're created by server-side developers. You see, serversiders, they got used to compile HTML. Since the invention of PHP, that's what they do, they write programms that compile #HTML, and when a piece of HTML needs to be changed in some way, they change the programms that compile it.

And that's so 1999... Coz

## There Is A Totally Better Way Of Doing It

Sometimes you need to let the things go. As [Morihei Ueshiba](http://en.wikipedia.org/wiki/Morihei_Ueshiba), the great master of [Aikido](http://en.wikipedia.org/wiki/Aikido) said once: "don't try to stop a train with your body, love the train instead, let it go, let it run, and use it's energy"

In our case that would be the simplest thing, just remove the whole `<% if admin?` thing from your templates for good. Just give it some love, get rid of all the ugly `if` and `elses`, make it shiny

```erb
<div class="comment">
  <p class="text"><%= @comment.text %></p>

  <p class="controls">
    <%= link_to :edit, edit_comment_path(@comment) %>
    <%= link_to :delete, @comment, method: :delete %>
  </p>
</div>
```

Then go straight into your `comments_controller`, get rid of all the momoization crap and `cache_page` the whole page as it is, with all the links

```ruby
class CommentsController
  caches_page :index

  def index
    some_slow_stuff
  end
end
```

That's what zen is all about, deconceptualization. You need to make things simple to see their true nature, and the true nature of your situation in this case is that you just wanna show some controls to the comment owners that's it. It doesn't mean that you have to add and remove them from your html, it only means that you need to `show` and `hide` them.

How about that?

```erb
<style type="text/css">
  .controls {
    display: none;
  }
</style>

<div class="comment" data-owner-id="#{@comment.owner.id}">
  ....

  <p class="controls">
    ...
  </p>
</div>

<style type="text/css">
  *[data-owner-id="#{@comment.owner.id}"] .controls {
    display: block;
  }
</style>
```

See, we just hide all the control links upfront with some #css, and then show them to the right user with a specifically crafted for him piece of CSS that shows the links to the user.


## Possible Implementation

There is a whole bunch of ways to add that last piece of CSS on the page. You could say append that piece of css to your html with a simple rack app, maybe even use a quick #nodejs proxy for that, or something like that. But normally you also need all sorts of other stuff about the current user, like his name, avatar, role and so on. So it's better if you just make a quick #ajax request to your server for that data

For example add the `show` method to your `SessionsController`

```ruby
class SessionsController < ApplicationController

  def show
    if logged_in?
      render json: {
        id:    current_user.id,
        name:  current_user.name,
        admin: current_user.admin?
      }
    else
      render text: '' # nothing
    end
  end

  ...
end
```

Yes, you do an extra request to your server, but in reality you make the same exact amount requests to #rails, coz the main page will be served with your server directly to the user (possibly through a bunch of extra locations), and this authentication request is really lightweight and can be optimised further with `cookie` flags, caching and so on.

The big win here is that you don't render you huge page all over again all the time.

And on the #javascript side it all really super simple (pardon me for using my beloved [lovely.io](http://lovely.io) in here)

```coffeescript
Lovely ['dom', 'ajax'], ($, Ajax)->
  Ajax.get '/session', complete: ->
    if user = @responseJSON # if the user session data came
      $(document.body).addClass('logged-in')
      $(document.body).addClass('admin') if user.admin

      style = new $.Style(html: """
        *[data-owner-id="#{user.id}"] {
          display: block;
        }
      """)
      style.appendTo(document.body)
```

You also can continue and adjust the userbar for the logged in user, sort of like that

```coffeescript
$('#login-form').hide()
$('#userbar').show()
$('#userbar .username').html(user.name)
```

And so on. There is practically no visible lag in the UI with such approach. You can see it in action right on this very site.

This also opens up a whole bunch of options to you, like for example you can login/logout users almost instantaneously without actually reloading the page, switch users on fly, you can even use a separated server for users authentication and so on.

That's basically the whole idea. Let me know if you have any questions.





mighty_hacks 2012-11-22-modular-framework-is-a-reality.md

# Modular Framework Is A Reality!

So, here is the thing. I'm happy with this little thing called [lovely.io](http://lovely.io) it's a fully modular #javascript framework that uses a #cloudfront based CDN as a centralised packages repository. In an essence it looks kinda like that

```html
<script src="http://cdn.lovely.io/core.js"></script>
<script type="text/javascript">
  Lovely(['dom', 'ajax', 'fx'], function($, Ajax, Fx) {
    // do stuff here
  });
</script>
```

So, you load the `core.js` synchronously, then it loads the `dom`, `ajax` and `fx` asynchronously (each as a separated file) and once everything is loaded it will run your code.

When I explain this thing to people, 99% of recipients says something like

> Oh, no! You're loading 4 files instead of one, it's gonna take forever because of the server resolving and repeated queries. And bla bla bla.

Today fellas, I'm gonna teach you a lesson

## The Test

As this is not my first penises measurement contest I'll keep the test super simple :)

```html
<html>
  <head>
    <script>var time = new Date();</script>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
    <script>console.log("jQuery CDN:      ", new Date() - time)</script>

    <script>var time = new Date();</script>
    <script src="http://cdn.rightjs.org/right-2.3.1.js"></script>
    <script>console.log("RightJS CDN:     ", new Date() - time)</script>

    <script>var time = new Date();</script>
    <script src="http://cdn.lovely.io/core-1.4.0.js"></script>
    <script src="http://cdn.lovely.io/dom-1.4.2.js"></script>
    <script src="http://cdn.lovely.io/ajax-1.1.4.js"></script>
    <script src="http://cdn.lovely.io/fx-1.1.1.js"></script>
    <script>console.log("Lovely.IO Sync:  ", new Date() - time)</script>

    <script>var time = new Date();</script>
    <script src="http://cdn.lovely.io/core-1.4.0.js"></script>
    <script>
      Lovely(['dom-1.4.2', 'ajax-1.1.4', 'fx-1.1.1'], function($) {
        console.log("Lovely.IO Async: ", new Date() - time);
      });
    </script>
  </head>
</html>
```

I'm using all #jquery, #rightjs and #lovelyio official CDN hostings. It's blunt stupid, I simply include the file and measure how long does it take to load and initialize everything.

The only difference that in case of #lovelyio you can use it in both synchronous and asynchronous modes, hence two tests

Now, you might wanna cover your kids ears.

Ready. Set. Go!

## Clean Cache Result

![](http://zenmagiclove.com/misc/rocks/11-hMBO.png)

## Cached Reload

![](http://zenmagiclove.com/misc/rocks/22-ul8w.png)

## Analysis

To be fair, both jQuery and RightJS use a Google based CDN and I'm in Bangkok, so I've got `~100ms` ping to their servers. LovelyIO on the other hand uses AWS CloudFront as it's CDN hosting, so I have only `55ms` lag with them coz those guys have edge locations just in every corner of the planet.

But even with this in mind, you should consider that #lovelyio loads _4 files_, when both jQuery and RightJS load only one.

So, here it is. Fully AMD based modular #javascript frameworks is the reality, more of that when it's done right, they are far better reality than the one you used to think is the best.





mighty_hacks 2013-01-06-a-simple-bounce-effect-in-css3.md

# A Simple Bounce Effect In CSS3

Just so I didn't loose it later. A simple bouncing effect in pure #CSS3

```css
.bounce {
  -webkit-animation: bounce 2.5s infinite;
}

@-webkit-keyframes bounce {
  0%  { -webkit-transform: translate(0px, 0px)  }
  10% { -webkit-transform: translate(0px, -50%) }
  20% { -webkit-transform: translate(0px, 0px)  }
}
```

See this thing [in action](http://jsfiddle.net/vrbSn)





mighty_hacks 2013-01-14-how-to-test-routes-with-minitest.md

# How To Test Routes With MiniTest

Here we go again. Yet another testing framework for #rails and nothing works :) Well, I hope #minitest will eventually get there, meanwhile, if you're a test junkie and wanna test routes properly in your rails application, here's how you can do that.

Firstly, you'll need to add this in your `test/minitest_helper.rb` or wherever you keep the minitest hooks

```ruby
class MiniTest::Rails::ActionDispatch::IntegrationTest
  register_spec_type(self) do |desc|
    desc.is_a?(String) && desc =~ /routing/i
  end
end
```

Secondly, create the `test/routes` folder and some tests the usual way, for example

```ruby
require 'minitest_helper'

describe "Some routing tests" do
  it "should route root to dashboard" do
    assert_routing "/", class: "dashboard", action: "show"
  end
end
```

The magic thing in here as you might figured is to put the `routing` into the top description call so that we could hook it up correctly

And finally you might wanna add the following into your `Rakefile`

```ruby
if defined?(MiniTest)
  MiniTest::Rails::Testing.default_tasks << 'routes'
end
```

That will make sure that minitest runs your `test/routes` directory with the `rake minitest` task, say on CI or something like that.

That's about it. Enjoy!





mighty_hacks 2013-01-20-fix-ruby-require.md

# Fix Ruby require

Requiring things in ruby always were kind of ugly business and you would see things like that all the time

```ruby
require File.join(File.dirname(__FILE__), "boo", "hoo")
```

And to address the issue, in Ruby 1.9 they did the silliest possible thing, they introduced the `require_relative` function.

Why is it silly? Coz you don't need it! Humanity's been using the `require "./somthing"` syntax for ages and everyone's got used to it already!

More of that, you can actually hack it on fly, right in ruby itself, right now!

```ruby
alias :__require :require

def self.require(str)
  if str[0] == '.'
    require_relative str
  else
    __require str
  end
end

require 'some/global/lib'
require './some/relative/lib'
```

Enjoy!

PS: You also can get rid of the `File.join` calls that way and split/join the strings on fly.





silly_thoughts 2013-01-21-get-your-dick-out-of-your-code.md

# Get your dick out of your code

One smart man once told me a simple trick to being awesome

> Get your ego out of the things you do

The idea is simple, just remove the self-created preconceptions about the world from your way. Years pasted and it's still one of the best advises i had in life.

You see, there is a problem in software development sphere, we lack white hat role models. The thing is that to run a successful, hight profile project you have to be a dick, it's just what it is, a prerequisite for being able to filter massive feedback. And it's fine, people spend their lives on those projects after all. But the problem is that many others are trying to be like DHH or play Steve Jobs, and sadly, they not starting by building awesome products, they start with monkeying their behaviour, aka. being dicks.

Let me spell it you. There is only one way to learn things, it's called `get in troubles and grow`, that's how it works. And whoever does it faster wins the race. And the hard truth is that no matter how good you are, there are people out there that are better than you, and you have a choice, be a dick and ignore or learn from what they have to say.

And that's all really what's there to it.





mighty_hacks 2013-01-26-tdd-for-complete-idiots.md

# TDD For Complete Idiots

As there are people who still don't know how to do it properly, here is your full, comprehensive and easy to grasp guide to #TDD

![](http://zenmagiclove.com/misc/rocks/image-Ooqe.jpg)





mighty_hacks 2013-01-30-one-of-the-ways-to-deal-with-github-tolls.md

# One Of The Ways To Deal With GitHub Tolls

Today was a successful day, I pissed off some guy on the internet well enough for him to go to #github and make a pull request with some TL;DR comment that supposed to be offensive or something.

So, I've figured: "hey, i've got 5 mins of free time, i could make something out of it".

```
cd my-project
git remote add nice-dude https://github.com/nice-dude/my-project
git fetch nice-dude
git checkout -b hohoho
git merge nice-dude/master
git revert HEAD --no-commit
git commit --allow-empty --amend -m "Hey Nikolay, I'm sorry for being a dick to you"
git checkout master
git merge hohoho
git push
```

Now when people go to the #git commits log, they can see how this dude is saying sorry for being a dick on your project.

Enjoy! :)





silly_thoughts 2013-02-04-ruby-is-an-establishment.md

# Ruby Is an Establishment

If you're young ambitious web-developer and you want to do it for long time, you should contribute to #nodejs, not to #ruby or #rails.

When you do it long enough, you can start to see the pattern, it was all the same for `C -> C++`, for `C++ -> Java`,  `Java -> Ruby` and it's all the same for `Ruby -> JavaScript`. The list of arguments is virtually the same, the only thing that changes is the name of the languages.

The old language becomes an establishment, it gets full of people who are there to protect their status, not to promote technologies. You can contribute there but you will be just wiping asses, that's all, coz they don't call it "establishment" for nothing. Everything was already established in there and people on top of it won't let you change anything.

Every time I return from a JavaScript project to a Rails one, I feel that the language is perfect, but the environment is a huge drag, complete ignorance of innovation or anything outside of ruby/rails scope. Wanna see a developer who's afraid of #javascript? Throw a stone in a crowd of rails developers and you'll hit two or three.

Zed Shaw mentioned that it will come it in his famous rant on rails, you might think that he'd blown it out of the proportions, that maybe, but the idea is kinda still correct. The ruby community is driven by a bunch of tired people and it's stagnating.

So what about #nodejs? It sucks right? But, have you seen Rails 1.0.0? It was blowing ass big time. But they will fix it and make it mind bogglingly awesome. The simple truth is that #javascript, due its simplicity  is much more flexible, abusable and fun than #ruby. It lacks certain things, and might do stuff that you wouldn't expect. But what do you want? #nodejs is a frontier and it's part of the fun.

And if you can't see the beauty in it, you probably shouldn't write code at all, coz you're a robot.





mighty_hacks 2013-02-09-lovelyio-kicks-ass.md

# LovelyIO Kicks Ass

In case you're not up to date with what's going on here. [lovely.io](http://lovely.io) is an HTML5/CSS3 centric fork of the [rightjs](http://rightjs.org) project, which is basically a #javascript library that was aiming for speed and novelty.

So, when I started with #lovelyio, one of the things that was bugging me all the time was an aim to make it faster than #RightJS. The problem as you know is that #rightjs, due its unusual dom-wrappers based architecture, was the number one for several years in terms of dom-manipulations speed.

TL;DR, finally I've made some progress in this area, and lovely.io is now consistently faster than the latest #rightjs build by ~5-10%. Not much, but it's an obvious win.

As per usual, one of the perks of making things better is that you can troll #jquery folks.

Hey jQuery robots! Did they tell you that jQuery 1.9.1 is better than jQuery 1.8.2? Well it aint :)

![](http://zenmagiclove.com/misc/rocks/1-KMAS.png)

Yes, I said "troll", so that you could chalk it up on me and don't feel bad about your framework of choice. But if you want to know what's going on, you can find the source code of the tests over here

https://github.com/MadRabbit/kick-ass

Enjoy!





book_worm 2013-03-03-cloud-atlas.md

# Cloud Atlas

So, here's a dirty secret. I love books. You know that moment when you listen to really good piece of music and it vibrates with you so well that you get goose skin? Well, I get goose skin of good literature.

Anyways. Finished "Cloud Atlas". It's soo gloomy and depressive, I almost feel sick. But it's easily the best book I read in a while.

To be fair, it's six novels in one book, and every one of them is written in different language, style and level of awesomeness, but still, very very cool.

It's interesting how modern literature on theoretical stand of point does same thing as music and visual arts. It abstracts, gets away from plot and characters and scenes. "Cloud Atlas" is all like that, all the plot and story and things that are happening are secondary, it's all in the words texture and small details and the overall concept. It's like Mondrian or Malevich if you like.

It's abstract, but it's not poetry. It's not melodic. Or how author puts it himself. It's not Mozart, it's Stravinsky.

Which consequently brings us to the question: why would you want to make a movie out it?





mighty_hacks 2013-03-12-hacking-google-i0.md

# Hacking Google I/0

I love google. Seriously, you might argue that there is a whole bunch of things that wrong with Google, but i really, really, really dig their culture.

So, go to https://developers.google.com/events/io/ and find there an easter egg. I'm not gonna spoil it for you and explain how it works, but here are the keys

```
cat      11100111
pong     10000001
bacon    10010000
simone   11010011
eightbit 01010011
synth    10001000
ascii    01111111
bowling  01110101
rocket   01000101
burger   00111001
```

PS: learn #javascript, coz knowledge is power :)





silly_thoughts 2013-04-11-the-trick-to-good-software.md

# The Trick To Good Software

The funny thing about programmers is that we spend years of our professional lives believing that our job is to tell computers what to do.


## The Reality

Reality is that computers do what they were told to do just fine. No matter whether you write `"Hello World"` or kill someone with a drone. Computers do precisely what they were told to.

Your job, your real job is to tell _programmers_, and you yourself in the first place, _what you told computers_ to do. The idea of modern software development is in structuring and cleanly describing task that a computer will perform.

The truth is that #computers don't read what you wrote in your programs, people do. Computers compile your code down to bits and bites and the only ones who see what you actually wrote are other #humans.


## Story Telling

If you start to look at your job and code you produce this way, it becomes immediately apparent that #programming is much like story telling.

Now think of it. How do you know that a guy can't tell stories for squad? It's really simple, when a story teller deviates from the story line all the time, get stuck with unimportant details, constantly jumps back and fourth between the points, etc. you immediately know that the story is gonna suck.

In the end you can understand what happened in the story, you even can retell it, but did you enjoy the story? did you get inspired to retell and maybe extend it?

Same exact thing happens in software. When you write cryptic, full of noise piece of code, no one is going to enjoy it. No one will think of it twice. And you're the first to suffer.


## The Trick

Well, you're here for a simple secret sauce don't you? So here it is

> The less noise your code has the better

Note that I'm not talking about explicit vs. implicit code, convention over configuration, meta-programming evils or any other things of this sort.

The trick to good software is to write in your code exactly what makes sense for the story your code tells. If it's good for the story to be explicit, do it. If the stuff you write has nothing to do with the story, kick it out. Kick it out for good. If it's vaguely coupled, use meta-programming and conventions.


## Examples

Some classical examples to prove the point. Say a post has an author.

```ruby
class Post < ActiveRecord::Base
  belongs_to :author, class_name: 'User', foreign_key: :authored_by
end
```

See? All this noise about class name and foreign keys. Get it out

```ruby
class Post < ActiveRecord::Base
  belongs_to :user
end
```

The second version doesn't have the nice `author` naming in it, but it is much better because it goes straight to the point and in a short sentence tells you everything you need to know.

Another example, say a class needs to set a reference to users who created/edited its records

```ruby
class Setting < ActiveRecord::Base
  belongs_to :creator
  belongs_to :editor

  attr_accessor :editing_user

  before_create :set_creator
  before_update :set_editor

private

  def set_creator
    self.creator = @editing_user
  end

  def set_editor
    self.editor = @editing_user
  end

end
```

Noise, all those callbacks and attr_acessors it's just rubbish that doesn't have much to do with what you're trying to accomplish. Much more explicit and nice way to do it would be say like so.

```ruby
class Setting < ActiveRecord::Base
  belongs_to :creator
  belongs_to :editor

  def editing_user=(user)
    if new_record?
      self.creator = user
    else
      self.editor = user
    end
  end
end
```

See how it now tells exactly what's going on? This code says, we have a creator and an editor and we _assign_ them from the _editing user_. No middle noise about callbacks. No extra noise from having _two_ methods in _private_ namespace.

One more classical example. Massaging your data in a controller

```ruby
class PostsController < ApplicationController
  def create
    if params[:post][:text].present?
      if params[:post][:text] =~ /fuck|cock|shit/
        flash[:error] = "Be nice"
        @achtung = true
      end
    end

    if !@achtung
      @post = Post.new(params[:post])

      if @post.save
        flash[:success] = "Yoo hoo!"
        redirect_to :index
      else
        render :new
      end
    else
      redirect_to :index
    end
  end
end
```

All those conditions have nothing really to do with your controller. To be precise all this logic does not belongs to the controller level at all. Yeah, you can do that and it will work, but it's not good software.

Try this.

```ruby
class PostsController < ApplicationController
  def create
    @post = Post.new(params[:post])

    if @post.save
      flash[:success] = "Yoo hoo!"
      redirect_to :index
    else
      render :new
    end
  end
end

class Post < ActiveRecord::Base
  validate :bad_language_check

private

  def bad_language_check
    if text =~ /fuck|shit|cock/
      errors.add(:text, "has some pretty bad language")
    end
  end
end
```

Now your controller clearly says what's going on. And you can easily see what happens in both cases when it can and cannot create a record. Same of the `Post` class, you can clearly see that it validates the text for bad language. And the checker itself is in the private section, just to hint you that its implementation is not that important for the post itself.


## Conclusion

It's really simple. Want to write good software? Stop writing it for the computers and start writing it for humans.

It is that simple.





mind_fucks 2013-06-22-the-mind-fucks.md

# The Mind Fucks

It's might be a bit strange hobby for a programmer, but i dig psychology, cognitive science and meta-thinking in particular. It's just professional interest, programmers are basically mental heavy lifters and understanding how my own brain works is kinda fascinating.

So, I'd like to start a new log, where i could occasionally write about all those little things software-developers often unaware of. Well, until some point in their life anyways. And I'll start with the good old one.

# The Dunning-Kruger Effect

This is really simple. The Dunning-Kruger Effect describes inability of a person to adequately estimate his own abilities/skills in tasks in which they don't have enough experience. Basically, most of the people overestimate their skills when they're moderately good at something.

In case of software developers it's always turns into the well known "4-years old syndrome". Developers are generally smart guys and it takes them ~2-3 years to grasp the basics of computer science, and 1 year after that they tend to fall into the "know it all" stage.

The problem is that learning is not a linear process as it might appear. If it takes you 2 years to get from a junior developer to mid level one, you might think that it will take you another 2 years to get from mid to senior, and then 2 more years to the elite super-hacker level.

Knowledge and learning is much like speed of light. Every next step requires more energy and takes longer, because the further you go, the heavier you get. And sadly, sheer knowledge is not the only thing that counts in our profession, in fact it's more like a prerequisite. Experience and talent for elegant solutions, that's what really matters. And gaining experience takes time.

## How Not To Fall For It?

Aside of understanding the problem, there is one really good solution for this fallacy that was known for ages. It's called

> Get your ass kicked

Start ambitious projects, compete agains other fellow developers, always ask superior folks to review your code and ask them not to be nice to you.

And as it often happens after collision with the reality, you will get hurt, you will get disappointed, but you will recognize the problem, and that will make you truly better.





mind_fucks 2013-06-24-the-90-seconds-thing.md

# The 90 Seconds Thing

In some ways, writing code is much like driving a sport car. It might look relatively simple, but if you don't know what you do you'll just make a lot of noise and will drive from border to border. Writing good software is hard, it takes lots of practice, and sometimes you need to drive into a corner sideways to make it right.

The thing is, that writing code is a skill, good programmers fall into some sort of stasis when they write code. Just like driving a car, you feel in control, you know what you do, but after the job is done, you sometimes can hardly recollect how you got from point A to point B. Programmer's mind constantly processes information at high speed and at any given moment makes gazillion decisions.

This is a well known problem in sports and cars driving in particular. In many cases, your brain is so overwhelmed with processing the data, that it can't make conscious decisions effectively. To overcome this problem, they use all sorts of meditation techniques. Basically drivers visualise the road in their minds and imagine themselves driving it, they go through that complicated corner over and over again in their minds until their subconsciousness memorises the pattern. Later, when they drive on real road, their bodies already know what to do and they can focus on other things, like competitors, weather conditions, etc.

If you ever watched really good programmers you might notice they do similar thing. When they're given a new task, they stop, freeze for a minute and then start write the code from top to bottom in basically one set. Those hackers do this premeditation thing naturally, they think the thing through, outline what they're going to build in their minds before they write the code.

In psychology it is a well known effect, it takes ~90 seconds for you brain to make a judgement, everything else is usually negotiation and rethinking on that decision.

So, next time, before you jump into madly writing code, stop for 90 seconds, let your brain to process the task first. It will do two things for you.

1. It will help you to visualise what you're trying to achieve
2. It will trigger your quick judgement reflexes before you make a mess

Trust me, 90 seconds is nothing comparing to hours of building and reworking the things a wrong way.





mind_fucks 2013-07-03-the-bystander-complex.md

# The Bystander Complex

There is a really good reason why otherwise intelligent people do really stupid things when you bring them together. There is this little thing called conformity.

Every separated person might be intelligent and independent thinker with his own opinion on a whole range of things, but when you a bring a bunch of people like that together, conformity kicks in. It is a natural, subconscious reflex that used to keep groups of people together and therefore increase their chances on survival. No matter how intelligent or opinionated, in groups, people instinctively tend to conform with the group interests. Especially in unusual and doubtful situations.

Bystander complex is a direct result of the group conformity instinct. There is a whole bunch of cases through the history when people were murdered and robbed in a broad day light with dozens of eye witnesses, and no one did anything. It's not that no one wanted to help or was too afraid, those situations happen because in unusual circumstances every person checks with the reactions of people around them, and if they don't see other people do anything, they tend to keep calm and comply with the group behaviour.

You might think that software developers are better than that, that if we want we can fix anything. Reality is that conformity and bystander complex are very common among programmers as well, especially in world of open-source. I'm sure you know a whole bunch of cases when really bad decisions were made by some really bright folks, and you might even thought

> This thing is horrendous, I'm pretty sure someone will jump in and fix it pretty soon.

The trick is, everyone thinks that. As the result, it is very often case that a few folks raise their voices but no one acts and the group starts to think something like

> Oh, no one fixed that, maybe that thing is ok. Maybe there is a reason why it's like that and we should all use it.

And the larger the group of people, the better chances that you get into this situation. (Yes #rails turbo-assets-super-line I'm looking straight at you).

So, the moral is pretty obvious. If you wanna have better things, just go in and try to fix it, don't wait for the group approval, coz that might never happen.

Also, take large and popular projects with a grain of salt. People in groups tend be polite and please each other with good words, it's just what we humans do, and often it doesn't have anything to do with the quality of the project that unites us.





mind_fucks 2013-07-24-opinions-origins.md

# Opinions Origins

Sometimes it's almost seems that our entire industry is driven solely by opinions. So, here is a little theory about opinions.

In good old times, there were two types of monkeys, one could see a sable-tooth tiger's tail sticking out of a bush and picture the rest of the beast hiding behind the leaves, and the other type of monkeys would saw raw facts (meaning just the tail) and didn't jump to conclusions.

As you might imagine for millions of years, the first type of monkeys would run for it, and the second type of monkeys would be eaten systematically. As the result, our brains evolved to think about things in a very specific way. Our brains don't think logically, our brains think associatively and most of this thinking process happens in the subconsciousness.

That's why people are often irrational. Because we don't go through the `if|else` constructions in our heads. Well, we could if we really needed, but in most cases we just get existing associations in our memory, and try to pull something out of it. And if a person had weird, conflicting experiences he will get weird, conflicting associations out if it.

More of that, when it comes to memory, we all are subjects to what's called the consistency bias. Basically when something doesn't add up, instead of facing an internal conflict, we tend to just go ahead and invent stories and memories to keep the picture consistent.

As the result, practically all our opinions are delusional one way or another, and just as in the case of tiger's tail, opinions are often provoked by insecure feelings.

So, next time you hear someone saying "in my opinion ..." you should really hear something like "here's kind of associations i've got it my head" and then think of experiences this person went through, that might just tell you more on the topic than the opinion itself.

Oh, and if you see an "opinionated framework", stop for a moment and think why is that the authors felt so insecure and put the word "opinionated" in the first sentence of the project's description. And why authors are trying to put those opinions on framework itself rather than express them personally?





mind_fucks 2013-08-19-why-stories-are-important.md

# Why Stories Are Important

There is this thing called "narrative psychology", which basically postulates that everything that humans do and think and understand are stories. Even your whole personality is a story which you tell yourself every day.

You know by now that human brains are basically huge association machines and we don't really think things through logically bite by bite, instead we make associations. The funny thing is that we don't use plain associations either, we make little stories out of them. And the story must make sense. Well at least to you personally.

Stories are the way we make sense out of all the chaos that happens around us. All the statistics and numbers live in the realm of dry science, it's us, humans, who give them meaning through narratives. In fact this idea is so popular, that it was abused for centuries in all the mystery novels and detective movies. Take any of them and there is always a sequence of seemingly random events  and it takes a genius to put things together and make sense out of it. And you know it, detective always tells a story before pointing his finger to a culprit.

You might not write a novel in your code (although you totally could), but making your code to represent a story is a straight forward thing to do if you want your code to be easily understandable and hence maintainable. It is not enough to just break your code to pieces, it is important that abstractions you use were at the same level of the story and hence make sense to the reader.

And same thing goes for the tests, if your tests are just a random pail of cryptic constructions, you do it wrong, even if you have 100% test coverage.

Good code reads as a good story, you just jump in, read it through, and get it all after one pass. Loud and clear.





mind_fucks 2013-08-24-programmers-happiness.md

# Programmers Happiness

Happiness is an elusive topic, but mechanics of it can be explained through memory. See you have two types of memory, sensorial memory, which handles things like touching, tasting, sense of spacial orientation, etc., and conscious memory which basically handles memories of events, places you've been, people you've met.

The first type of memory lasts for about a minute. After that can only partially recollect it in the long lasting memory as a series of echoes and events. The second type of memory is persistent and lasts for days, months and even years, depending on the importance of the event.

So, when it comes to happiness, it's like there are two different people inside of you, struggling for getting what they want. One is a long term planning person that say wants to loose weight, but another one kinda wants a piece of cake.

This struggle is going on and on inside of your brain every second. You can focus on one half of them, say you want to travel and save money, but then you go and work for some faceless bank. You can do that, but that will make you half happy, half miserable, which is kinda 0 in the end tally.

The real trick is not in finding a compromise, but in finding something that will make both of the types of memory satisfied. Like great creative job that's paid nicely, or tasty healthy food.

This is one of the reasons, why languages like #ruby or #javascript are so popular. They have little overhead and easy to maintain, but at the same time allow you to tackle pretty ambitious tasks.

So, as a programmer, you always need to keep those two sides of you in mind. Be creative and ambitious in your work, but keep it tidy and easy to maintain. Make implementation plans, but write scripts to automatize routine tasks as well. That's all there is really.

And the best part is that this approach comes with a bonus. Life is sooo much better without inner conflicts! And you can spend all this extra energy you usually spend in struggles, on living the life. You can walk with a doggy on a beach, give hugs to friends, see places, ride a pony. Well, you get the picture.





mighty_hacks 2013-08-25-searching-through-non-activerecord-data.md

# Searching Through Non ActiveRecord Data

So, here is a situation many young developers are struggling with when working with #rails and #activerecord. Say you have a `User` model that has a status field. And the actual data in the status filed is mapped to a hash, like so.

```ruby
class User < ActiveRecord::Base
  validates_presence_of :name
  validates_presence_of :status

  STATUS_LABELS  = {
    active:       'Active',
    suspended:    'Suspended',
    went_to_pub:  'Went To Pub',
    works_at_pub: 'Works At The Pub'
  }

  def status_text
    STATUS_LABELS[status] || status
  end
end
```

This situation appears in Rails development all the time in various ways and people do that for several reasons. Most of them is maintainability, if a manager wants to change the label, you can do so without changing the database table data. Or you want to support multiple languages. Whatever is the reason, it poses a problem.

## The Problem

When you present your data on an HTML table, you use those translated data and looks kinda like that

```text
+-------------+------------------+
| Name        | Status           |
+-------------+------------------+
| Chris       | Suspended        |
| Trung       | Went to pub      |
| Nikolay     | Works at the pub |
+-------------+------------------+
```

And when you add a search bar to that table, users will search entries by whatever they see in the table. So the question is how do you search a table by something that's not in there? Normally if you'd store those labels in a separated table, you could just join tables and make an ` ILIKE '%smth%'` query through all the fields, but you can't coz it's static data.

## The Solution

Solution is really super simple, all you want to do is to define a method that will look through your status labels and return a list of matching status values, say like so

```ruby
class User < ActiveRecord::Base

  def self.matching_statuses(word)
    STATUS_LABELS.map do |value, label|
      value if label.downcase.include?(word.downcase)
    end.compact
  end

end
```

Once you get this, you can write a normal ActiveRecord scope to search through your whole table

```ruby
class User < ActiveRecord::Base

  scope :search, ->(word) {
    where "users.name ILIKE ? OR users.status IN (?)",
      "%#{word}%", User.matching_statuses(word)
  }

end
```

See, nice and simple.

## The Conclusion

The reason why devs often struggle with such a simple thing is that SQL fries your brains and you start thinking tables instead of actual data and objects.

My dear friend, a word of advise, #ruby is awesome and #activerecord is a bloat. Ditch it. In the future, we all gonna wear colorful pajamas and use NoSQL.





mighty_hacks 2013-08-26-rails-dates-and-magic-created_on-hack.md

# Rails dates and magic created_on hack

To demonstrate the problem I'd like to start with a little rant. There is a very common and very simple task of formatting dates, which people, lets not point the fingers, tend to solve in all sorts of different ways. Say like that

```erb
&lt;h1>User Profile</h1>

<dl>
  <dd>Registered At</dd>
  <dt>&lt;%= @user.created_at.strftime('%d %b %Y %H:%M') %></dt>
</dl>
```

Or, if the date might be missing, then like that

```erb
<dt>&lt;%= @user.created_at.strftime('%d %b %Y %H:%M') if @user.created_at %></dt>
```

Which then usually "optimized" into a helper method

```ruby
module DatesHelper
  def time(time)
    time.strftime('%d %b %Y %H:%M') if time
  end
end
```

Which then, often gets bettererized in something like that

```ruby
def time(time)
  time.strftime(TIME_FORMATTING_FROM_GOD_KNOWS_WHERE) if time
end
```

And if you need to support i18n, things might take some rather disturbing turn...


## Meanwhile

Meanwhile, #rails is actually built to take care of those things for you automatically. All you had to do is to write it in your template like that and go home happily

```erb
<dt><%= @user.created_at %></dt>
```

Because as long as you set your locale correctly in the rails config, it will export your times and dates to strings automatically according to that locale.

This approach is better on many levels. 1) It's a rails convention 2) It keeps the templates clean 3) It never crashes if the date's missing and you forgot to take care of that 4) You can keep the formatting consistent through your app.

And if you feel particularly evil and want your own formatting anyways, you can always configure it like so in any initializer

```ruby
Date::DATE_FORMATS[:default] = "%d/%m/%Y"
Time::DATE_FORMATS[:default] = "%d/%m/%Y %H:%M"
```

Rails is pretty awesome like that, isn't it?


## The Hack

That was the preamble, now to the hack. There is still one valid case when you want to mangle with the times, when you have the automatic `created_at` timestamp on a record and want to present it as a plain date. Say time doesn't really matter. For example

```erb
<dd>Registration Date</dt>
<dt><%= @user.created_at.to_date %></dt>
```

If you happened to do that a lot, you might find it a bit annoying. Firstly, it still can crash if the date is missing for some reason, and secondly you're adding extra noise to your code with explicit dates conversion. If that's the case you might find the following hack useful.

```ruby
class ActiveRecord::Base
  def created_on
    created_at && created_at.to_date
  end

  def created_on=(value)
    self.created_at = value
  end

  def updated_on
    updated_at && updated_at.to_date
  end

  def updated_on=(value)
    self.updated_at = value
  end

private

  def timestamp_attributes_for_create
    [:created_at]
  end

  def timestamp_attributes_for_update
    [:updated_at]
  end
end
```

Just put it somewhere in your `config/initializers` folder and you'll get the `created_on` and `updated_on` methods for free! Now you can say in your code what you really meant

```erb
<dt><%= @user.created_on %></dt>
```

The benefits are obvious, cleaner code, no crashes, and it's built on top of rails conventions, so, no weird stuff, all crystal clear!





building_the_tele 2013-09-01-intro.md

# Intro

Long time ago, back in Siberia, I had a little workshop and used to build guitars. I came from rather technical background and always enjoyed to build stuff with my own hands, and building guitars was something rather special, something out of the reality distorting area. You start with an ugly log and end up with something that sing and roar and can rock the world.

Anyways, it was something I really loved to do, and something I really missed through my years of wandering in the world. And long story short, I decided to build a telecaster out of spare parts bought on ebay.

This project will take couple of months and has a goal to build somewhat good for blues tele with rather modern look. In any case, I just wanna document the process and write some guitar stories along the way.





building_the_tele 2013-09-02-the-body.md

# The Body

![](http://zenmagiclove.com/misc/rocks/90ad38460eeb11e39dbc22000a1f9e59_7-J7uf.jpg)

For this project I bought a nice, official fender custom shop body. As I'm building a tele for blues, I picked a medium weight swamp Ash body.

Tele bodies are made from all sorts of materials, normally it's either ash or alder, but often they're made of pine and even rosewood. The principle here is rather simple, the heavier the body the darker the sound and the other way around. Ash is somewhere in the middle and considered to be the "standard" wood for fender guitars. It also comes in different grades, from light 4lb (1.8kg) for country, up to 6+(~3kg) which go more for rock and punk music. I normally just screwing around with blues, so I picked a body that weight ~5.2lbs, which should still have enough of middle tone, but be dark enough to go nicely with light overdrive.

The body itself is three years old and very nicely built in fender's custom shop. It is a rather cheap one, but if you know where to look, you can see that the work they've done is pretty admirable. It's all in the details, how they pick pieces, how they glue them together so that the grain between pieces match, etc.

They even painted it a bit differently on the sides. They used the semi-transparent, butterscotch blond paint, which I tend to really dig, it's yellow, but it's not cheap yellow like some mexican tele and it's not completely pale like some american ones. In any case, paint gets absorbed differently on different cuts of wood and if you just airbrush it in once coat, as cheap guitars done, it looks a bit ugly on the radial cuts on the sides, so they had compensated for that and gave it a bit of  extra coating on the sides, so it looks really nice.

Overall it's not just a CNC milled piece of random wood from an express vacuum dryer. This thing went through the whole process, judging by the stamps on the body, it took them ~3 years to build it and although it's obviously not the best in the world, this thing obviously got some love and I really dig it.

Can't wait to see how furniture will look on it!





mighty_hacks 2013-11-24-the-story-of-ifnot-and-unless.md

# The Story Of IfNot And Unless

So, kids, that's the bed story time. Please, my dears, gather around and listen to the story of `if !` and `unless`

Once upon a time, there was a little programmer, just like you, and he was growing and growing, becoming smarter and smarter until one day he figured

> Hm. `if !` and `unless` kinda do the same thing.

So, he stopped typing `unless` in his code, then stopped shaving, become dull and boring, then started eat too much pizza and died of a heart attack.

And all because he assumed that because `if !` and `unless` do the same thing, they are the same.

## There is a difference

There is a reason why `unless` was added into the language. Although it has the same exact effect as `if !` it has different meaning.

`if` (and `if !`) define the standard, default flow of the logic. And `unless` is for writing down the exceptional behavior. The first one is inclusive and the other is exclusive.

Lets say you've got a class `Taxpayer` and you wanna him to vote

```ruby
class Taxpayer
  def vote!
    #...
  end
end
```

And lets say you want prevent the same taxpayer from voting twice, coz those are the main rules

```ruby
class Taxpayer
  def vote!
    if ! voted?
      place_a_vote
    end
  end
end
```

But then someone said that they don't wanna non citizen taxpayers to vote, coz who cares about all those poor bastards? Here it's not really part of the voting logic, it's an exception to prevent the second class human beings from changing your cozy life.

In this case you should use `unless`, because it's just a filter before your main logic.

```ruby
class Taxpayer
  def vote!
    fuckoff unless citizen?  # <- exclusive

    place_a_vote if ! voted? # <- inclusive
  end
end
```

And that's the whole story. Now go to bed and dream of some pretty #ruby classes

```ruby
class Unicorn
  def shit
    :rainbows
  end
end
```

Tomorrow it'll be a new day





silly_thoughts 2013-11-27-and-here-is-why-rails-is-gonna-loose.md

# And Here Is Why Rails Is Gonna Loose

TL;DR #Nodejs has more humans and iterates faster.

It's probably a stupid thing to say, but I've been around the block for a while and I saw how #java went down and was replaced by #rails.

The problem with Java was that it's like a Boing 777, it's the whole universe and needs 100 people + an airport to operate. A solid enterprise and kinda makes sense if need to travel between continents, but a huge drag if you need to do small and medium size business.

And then this danish kid appeared with Rails. And it's a smooth ride to be honest, and we built this whole map of railroads which can get you pretty much anywhere. It might not be as fast as a jet, but tickets are cheap, you've got low overhead and things go quite predictable. And the best thing, you need just a few greasy programmers to operate it.

The problem with Rails is that well, it's rails, you can't turn the freaking thing. You only can switch on between the stations and the thing is quite massive, it takes time to start and stop it.

Node. Well, node has the V8 under the hood, and if we continue with our transportation analogy, it's like a racing car.
Yeah, it doesn't have an air conditioner or radio, but it didn't meant to! It's a racing car, it's built for speed. Node doesn't have much luxury, the ride is bumpy at times and you can crash it pretty badly if you don't know what you're doing.

But on the bright side, you don't need a railroad to drive it. You can turn wherever you want, you can go from 0 to 100 in seconds and you can even take it off road and take shortcuts if you feel particularly evil.

Analogies aside, if you take a look at the number of participants and the releases history, you can see that node has more humans working on it, and it iterates a lot faster. If on rails you get a new version every ~4 years, node releases every few months.

Now divide the number of humans by the length of a release cycle and you get the development speed. There is nothing personal about it, it's just science, the faster thing will always overtake the slower one.

So here it is. You can take the train, have the good old time in the restaurant carriage, maybe shoot some buffalos on your way, but in the end the racing car is faster and will beat the train despite of the head start.





mighty_hacks 2014-01-30-how-to-make-rubygem-post-instal-hook.md

# How To Make Rubygem Post Instal Hook

So here is a little hack which not everyone knows. Sometimes you need to run some scriptery after your #rubygem was installed, like say generate some templates or so. For example in [under-os](under-os.com) i needed to put the #RubyMotion templates in place. But unfortunately in gemspecs you can only specify `post_install_message`, which doesn't let you do anything really but print a string on the screen.

But if you really really need to run some #ruby scripts right after a user installs your rubygem, you can abuse the extensions builder. To do so, put this into your `.gemspec` file

```ruby
Gem::Specification.new do |gem|
  ....
  gem.extensions = ['ext/extconf.rb']
end
```

Then create the `ext/extconf.rb` file in your gem's folder with the following content.

```ruby
GO_NUTS_HERE

# add this at the end of the file
require 'mkmf'
create_makefile('')
```

Basically the trick here is to make an empty extension definition and put your ruby code before that. Works like a charm!

PS: Use responsibly :)





mind_fucks 2014-02-09-self-imposed-rules.md

# Self Imposed Rules

There is this thing people do a lot, it's called "self imposed rules". In sports and competitive games it is a well known factor. The classical example would be when someone calls another one "cheater" and they create all those extra rules to make the game "fair".

The hard truth is that there is no concept of "fair" in life. There is a game and you're free to use any moves that are allowed by the game rules. That's how we determine the winner.

Unfortunately not everyone is a winner and some people are trying to bend the rules so that it looked like they gave it the best and hence if they lost it wasn't their fault, it was a flaw in the game.

In sports they call those people "scrubs", people who scrub the floor after the game is over.

And programmers do that all the time. All those talks about superiority of one technology over another, sql vs. nosql, the modern tendency for functional programming purity, hate for not your favorite programming language or a database. Those are all classical examples of self imposed rules.

The name of the game is "build a product", and if you're complaining about technology not being the ideal, you're not playing it to win. Just as any other scrub on the planet, you are bending the rules so it would look like you gave it the best and if things weren't built it wasn't your fault.

Reality is, it is your fault, and the only question that really matters is what you're gonna do about it?





mighty_hacks 2014-02-14-single-page-apps-access-handling-with-angularjs.md

# Single Page Apps Access Handling With AngularJS

This one is a bit tricky and googling the question didn't get me to any clear answer, so I'm writing it here.

The question is how you do the routes access handling in an #angularjs based single page app, the simplest way possible.

## Prerequisites

I assume you go with a vanilla angular app and use the `angular-route` module. Then your app file looks kinda like this

```javascript
var MyApp = angular.module('MyApp', ['ngRoute']);
```

And you define your routes kinda like this

```javascript
MyApp.config(function($routeProvider) {
  $routeProvider
    .when("/", {
      templateUrl: 'dashboard.html',
      controller:  'DashboardController'
    })
    .when("/signin", {
      templateUrl: 'signin.html',
      controller: 'SigninController'
    })

    .otherwise (redirectTo: '/');
});
```

And finally lets assume you have a `User` model that keeps the user's signin status on it, kinda like that

```javascript
function User() {
  this.signedIn = false;
}
```

## The Solution

Firstly, you can define which routes are accessible by whom in your routes config, you can specify it pretty much as you pleased, i personally prefer to name the  property `access`, which makes it easier to grasp later


```javascript
  $routeProvider
    .when("/", {
      // ...
      access: 'require_user'
    })
    .when("/signin", {
      // ....
      access: 'anonymous'
    })
    // ...
```

Once you're done with that, create a new factory that will be responsible for handling your access restrictions (again, name it as you pleased)

```javascript
MyApp.factory('accessHandler', function($rootScope, $location) {
  // create a reference to the current user
  $rootScope.currentUser = new User();

  // tap to the route-change event
  $rootScope.$on('$routeChangeStart', function(event, route) {
    // handle your route access in here
    if (route.access != 'anonymous' && ! $rootScope.currentUser.signedIn) {
      $location.path = "/signin";
    }
  });
});
```

The `route` object in the callback will have all the same properties that you specified in your routes config, so you can be really flexible with those things and write down what makes sense for your app specifically.

Once you're done with the access handler, just add it to your app `run` method

```javascript
MyApp.run(function(stuff, accessHandler) {
  // it will kick in here automatically once angular loads the factory
});
```

And that's pretty much the whole thing

## PS

In the spirit of the `whatever` in Angular, there is no really a blueprint for handling authentication, but you can achieve it pretty easily with just a simple module and  a bunch of options in the routes config.

The good news is that you can be really flexible and make the authentication work exactly the way which makes sense for your application.





silly_thoughts 2014-02-18-how-to-be-awesome.md

# How To Be Awesome

Here is your simple and easy to follow instruction:

1. Start with an awesome idea
2. Work hard
3. Remove anything that is not awesome
4. Iterate

The logic is simple. If you remove everything that is not awesome, what's left will be the pure awesomeness.





mighty_hacks 2014-02-28-movie-style-stdout-in-ruby-and-rails.md

# Movie Style STDOUT in Ruby And Rails

Ever wanted your programs output to look like a cool hacker's console output in a blockbuster movie? Add the following #ruby script into any of your #rails `config/initializers` files

```ruby
STDOUT.instance_eval do
  alias :original_write :write

  def write(text)
    mutex = Mutex.new

    @write_stack ||= "".tap do |stack|
      Thread.new do
        while true
          mutex.synchronize do
            stop = (m = @write_stack.match(/^\e\[[0-9]+m/)) ? m[0].size : 1
            STDOUT.original_write @write_stack.slice!(0, stop)
          end

          sleep 0.01
        end
      end
    end

    mutex.synchronize do
      @write_stack += text
    end
  end
end
```

Enjoy!





silly_thoughts 2014-04-05-programming-happiness-my-a55.md

# Programming Happiness My A55

If you are a worthy programmer, then somewhere down in your soul, you probably have this nagging feeling that programming is an art, and you're this sort of a hidden artist in the rough who just happened to work in a bank and not really allowed to be artsy in your day job.

Well, let me crush it on you. Programming is not an art, it's not even an art-form. Programming is a medium, an instrument. Much like a paint, a guitar or a photo-camera. And art is what you're making out it.

And if you decompose those fuzzy feelings into three parts, the art, the medium and the person, everything falls in place. See, you can take the most beautiful programming language and use the most intricate software design patterns, but if you're building a banking application with it, you're not being an artist. You are being a 100% refined, totally organic, well paid corporate employee. It's just happened that instead of a suit and a cell phone, you have a t-shirt and a macbook air.

See, art is not about happiness, it's not about syntax correctness and beautiful design patterns. Art is about feelings and self expression. Art is spontaneous, and innocent, and useless in everyday life. Art is a cry and a hope to be understood.

And if you don't have this fire in your heart, it's okay, we still love you.  Just don't tell us which programming language will make us happier, coz it's as much stupid and pointless as telling a painter which color will add more happiness to his paintings.





mighty_hacks 2014-04-07-faster-tests-under-mysql.md

# Faster Tests Under MySQL

So, here's a quick and rebellious one. If you have a large project in #ruby-on-rails that is still under #mysql and your tests are painfully slow, chances are you're using the [database_cleaner](https://github.com/bmabey/database_cleaner) gem, and I can help you to make your life easier.

## The Problem

The problem is that `database_cleaner` wipes your entire database after every test, and if your database has enough tables in it this process can be really expensive. This is especially noticeable and frustrating when you write really good, lightweight and very granular tests. The database cleaner will just drag you down.

To be fair there is a transactional mode for database_cleaner, which supposed to run a whole test in one transaction, unfortunately you cannot use it with #mysql, because mysql, unlike #postgresql doesn't support nested transactions. So, if your code uses any sort of SQL transactions, the database_cleaner transaction will fail.

## The Solution

Meanwhile there is a pretty simple and powerful solution. You can extract the list of tables where new rows were created, and then clean only those updated tables. Put this into any file under your `spec/support` folder (assuming you're under #rspec)

```ruby
module OsomCleaner
  def self.clean
    tables_to_delete.each do |table|
      execute "DELETE FROM #{table}"
    end
  end

  def self.tables_to_delete
    result = execute("SELECT table_name FROM information_schema.tables WHERE table_schema = '#{db_name}' AND table_rows > 0")
    result.map{ |row| row[0] } - tables_to_skip
  end

  def self.tables_to_skip
    @tables_to_skip ||= %w{ schema_migrations sessions }
  end

  def self.db_name
    @db_name ||= connection.instance_variable_get('@config')[:database]
  end

  def self.execute(query)
    connection.execute query
  end

  def self.connection
    ActiveRecord::Base.connection
  end
end
```

And then add the following instead of your database cleaner setup

```ruby
RSpec.configure do |config|
  config.use_transactional_fixtures = false

  config.before(:suite) do
    OsomCleaner.clean
  end

  config.after(:each) do
    OsomCleaner.clean
  end
end
```

## Results

The results are obviously contextual and will depend on the number of sql tables you have in your application. In my case, this simple hack dropped the overall tests run time from `~15min` to `~1.5min` (yes, `~8-10x` boost) . The difference even greater on really clean granular tests that don't create too much scaffolding around tested units.

You can optimize it even further by patching the #activerecord connection to execute multiple SQL statements in one query, but it was proven not to be reliable enough and created dead locks in the #mysql connection.

## Caveats

Although this is really neat and pleasant hack it has one small caveat. Deleting things from a table doesn't reset the auto-increment counter on it. Even if you truncate your tables in the cleaner there are still chances that your code deleted everything from the table, and then the cleaner will just skip it because there won't be any records.

But, as I see it, it's fine, your tests shouldn't rely on known record IDs anyways. Besides you can always reset the increment in a `before { }` block manually whenever you're really need it.

And that's the whole hack, Enjoy!





silly_thoughts 2014-04-18-programmer-happiness-take-two.md

# Programmer Happiness, Take Two

But seriously, despite my recent rant about programmer's happiness, i really believe that happiness is not a pill, but a life choice. And programmer's happiness is not different. I'm convinced that you can be happy and productive with any, most horrendous language, or a framework, or an environment. The other way around is also true.

It is a life choice, it is how you decide with yourself how you going to perceive things. It is the same way as you pick a partner in life, none of the rational and non-subjective metrics really matter, it's all about what you want and what makes you happy.

It might be hard at the beginning, and people can be real dicks about your choices sometimes. But hey, some people always will be dicks when you try to stand for something. That's how life lets you know that you're doing something right.

In any ways, I'm trying to say, that to be happy as a programmer, you shouldn't run after whatever is optimized for happiness, but work on how you approach things on everyday basis. Shit will happen regardless of your platform choice, and in the end it's all about how good you are at dealing with it.





mind_fucks 2014-04-29-mental-capacity.md

# Mental Capacity

We programmers are the smart people, right? It is fundamental to our believes that in the virtual world, with enough hardware and coffee, everything is possible. And while i wholeheartedly believe in it, i also believe that it is crucial for a developer to take the idea of mental capacity seriously.

You need to realize that you not only limited by the number of hours in a day, but also in the number of logical operations your brain can perform per a quantum of time. When you realize that, the immediate thought that comes after it is "how do i trim the waste?".

There are plenty of ways you can go after it, but today i'm sharing a simple trick that i was taught years ago and which i personally find really helpful. It goes like that

> 1) Don't think about what you don't like
> 2) Focus on what you're after

Sounds dumb simple isn't it? Well, why don't you do it then? Just cut down hatery, stop complaining about other languages, buggy libraries and lack of tests in your current project. Reuse all those wasted brain cycles to create something and change your world for better. Well, because you can!

And it has a hidden bonus. It will make you a more cheerful and huggable person as well.





mighty_hacks 2014-08-31-angularjs-html5-routes-in-mixed-web-site.md

# AngularJS HTML5 Routes In Mixed Web-Site

I've got a simple, but interesting case today, which seems to give quite a bit of headache to some front-end developers who happened to work with #angularjs.

## The Problem

Imagine you decided to rebuild an existing server-side rendered web-site with angular, and you want to deliver it piece by piece. The problem starts when you decide to use the #html5 push state router. Say something like that

```js
angular.module('my-app').config(function($routeProvider, $locationProvider) {

  $locationProvider.html5Mode(true);

  $routeProvider
    .when("/", {controller: "HomeController", templateUrl: "home.html"})
    .when("/blog", {controller: "BlogController", templateUrl: "blog.html"})

    .otherwise({redirectTo: "/"});

});
```

This will nicely handle your landing page and the `/blog` resource, but, imagine you also have a legacy route `/about` which is not covered by your angular application yet and served as a static page directly from the server.

In this case routing won't work correctly. When you go directly to the `/about` page from the address bar of the browser, it will work fine, but, if you click say a menu option on a page handled by angular, the click will be caught by the angular's HTML5 router and will redirect you to the home page instead of going to the separate page.

## Options

This is a pretty common issue for legacy applications trying to switch to a modern API based design. Part of the routing is in angular and part of it is still on the server side.

Generally there are two solutions to the problem:

1. Disable the `HTML5` router and fall back to the anchor based router for the angular app. That is a quite disappointing and mediocre solution for building a _modern_ web app. It also might not be an option if you have to keep the original routing intact.

2. There is another solution, you can keep the html5 router, but add the `target="_self"` attribute to every link that is supposed to lead to an external web-page. This is a good solution if you have just a hand full of links and they all scoped nicely. Unfortunately it is not ideal because you will have to drag the `target` attribute around and keep them up to date as your angular application grows and takes over the legacy web-pages.


## Better Solution

Fortunately for us, there is a better solution, which shows the strengths of angular. What you can do is to define a `href` directive in your application and add the `target="_self"` attributes dynamically if the route is not matched against your routing system.

```js
angular.module("my-app").directive("href", function($route) {
  return {
    link: function(scope, element, atts) {
      if (!$route.routes[attrs.href] && attrs.href[0] !== '#') {
        element.attr("target", "_self");
      }
    }
  };
});
```

This solution lives in the veins of the AngluarJS philosophy, "build a framework that suits your app needs", it lets you keep the html5 routing and doesn't require you to do any extra work on your templates. In fact templates won't even know about the existence of the router, you can keep them clean and to the point.

And the best part, once you're done transferring all you web-site to angular, you can just delete this directive and move on with your day.





silly_thoughts 2015-02-19-the-year-of-the-horse.md

# The Year Of The Horse

It's probably a bit strange to write a year retrospective on a lunar new year, but I kinda missed the opportunity on the ISO new year, so I'll just pretend being fancy and do it now.


## Marriage

Well, there is no way around it (and there shouldn't be), the most important event of this year for me was my marriage. Long story short, I got married to a beautiful women, we love each other and so far it felt nothing but right.

It still feels a bit to early to contemplate on it in any meaningful way, so I'll just say, hooray to us!

![](http://zenmagiclove.com/misc/rocks/10724045_658527917577803_2013669504_n-9NO6.jpg)


## People Are What People Do

On the other hand this year was full of all sorts of realizations for me. There are good ones and bad ones, but one of the seemingly small and obvious, but, somehow very important, was that at some point I really felt it that people are what people do.

It doesn't really matter what you think about yourself from the inside of your skull in this regard, the things you do will affect who you are. People often neglect things they do (or don't do), or they often entertain the idea of being this and that, but don't really do anything.

Don't be an awesome person, do awesome things instead! You are what you do, so, shine!


## The Saddest Thing

A rather painful realisation was that one of the saddest things you can do to yourself is to let your limitations to define who you are. There are obviously things that you can't change, but surrendering to them without a fight to death is just... well, sad really.


## Shut Up And Learn

Another thing on the personal level that was hard to swallow, was a realisation that I grew too much into the development stack I normally use, and way too often I started to judge other tech from that stand point.

Well, it's easy to be a smug in your little domain, it's hard to look to things from a different perspective and try to really understand and get it. So, I started to punch myself in the face with books and throwing my brain into unfamiliar environments.

I learned a lot about business and management, I learned several new programming languages (including exotic ones like Elm and MiniKanren), I've spent some sweet time with Lua and Elexir, but the most notable was going with the 100% Swift and Xcode environment.

I spitted blood for a few weeks, but in the end, I learned to appreciate the other side of the world. Heck, I think i really like Swift and wouldn't mind if it was my primary language.

So, that was good experience, I hope I'll keep on this one in the next year.


## Travels and Journeys

It doesn't feel at times that this year was particularly full of travelings, but in reality there was a good measure of it.

Firstly, we went to New Zealand with my wife, that was awesome. A ~2000km roadtrip around the north island, with Hobbiton and the Tongariro national park. The place is amazing, would gladly go back! And certainly looking forward to go around the south island as well.

![](http://zenmagiclove.com/misc/rocks/10268983_635523719866103_633152073_n-NY0s.jpg)

Secondly, my job send me on a couple of trips to the US and San Francisco. That was interesting. I think I enjoyed it a fair bit, loved the people as well. But was glad to be back to Sydney. I've got mixed feelings about US, so I'll just let it settle down.

![](http://zenmagiclove.com/misc/rocks/10471865_1448633555388220_1439488756_n-VIz9.jpg)

Thirdly, we went to Fiji with my wife for our honeymoon. Interesting place actually, well, in its own way. I think at some point I want to see more of tropical islands in my future travelings.

[ ![undefined](http://zenmagiclove.com/misc/rocks/915610_715494591859476_451494740_n-P41K-small.jpg) ](http://zenmagiclove.com/misc/rocks/915610_715494591859476_451494740_n-P41K-large.jpg)


## Looking Forward

Well, it was already quite a lengthly post, so I'll wrap it up here.

I have a few not so secret wishes for this year, but I don't wanna spoil them, so I'll sound them out in the next year summary I think.

Otherwise, we're looking good and the next year seems really promising, so I'll just try to work my ass off and see what will happen.





mighty_hacks 2015-03-19-more-rainbows-or-why-you-should-switch-to-atom.md

# More Rainbows! Or Why You Should Switch To Atom

I've been in love with the [atom editor](https://atom.io) for a while now. So, I kinda feel like I have to write something about it, but instead thought I'll "show" something  :)

Open up your atom editor, then go to `Atom -> Open Your Stylesheet`, then copy paste thing below in that file and hit `Save`

```css
atom-text-editor::shadow .cursors {
  .cursor {
    transition: background-position 0.3s linear;
    opacity:       0.5;
    background:    gray;
    border:        none;
    border-radius: 1px;
    background-image: linear-gradient(-45deg,
      rgb(255, 0, 0)   0%,
      rgb(255, 255, 0) 16%,
      rgb(0, 255, 0)   32%,
      rgb(0, 255, 255) 50%,
      rgb(0, 0, 255)   66%,
      rgb(255, 0, 255) 82%,
      rgb(255, 0, 0)   100%
    );
    background-size: 5em 100%;
    background-position: 0% 0px;
  }

  &.blink-off .cursor {
    visibility: visible;
    background-position: 100% 0px;
    transition: none;
  }
}
```

Enjoy! (in case you still resist the future, [i got ya covered](https://twitter.com/nemshilov/status/578362578784514049)!)

# But Seriously

One of the biggest sale point for me is that atom is a browser, which means it's all #css and #javascript though and through. And I kinda do css and javascript all day, every day, so I can do whatever I want with the thing. Hell, I even can annoy Putin and friends with it (what a nerdy way of demonstrating your support and disobedience!).

Bet you can't do that with Vim!

# PS: Extra Weirdness!

One more. I didn't make it myself, but found on the Internetz. Put this into your editor when you feel down.

```css
atom-text-editor {
	-webkit-animation: rainbow 3s linear infinite;
}

@-webkit-keyframes rainbow {
	0%   { -webkit-filter: hue-rotate(0deg);   }
	100% { -webkit-filter: hue-rotate(360deg); }
}
```

That will remind you of the good old days you had in uni/college (I know you did) and maybe will trigger your creativity :)





book_worm 2015-03-20-slaughterhouse-five.md

# Slaughterhouse Five

I had a friend once who used to say that _"reading fiction does things to your soul"_.
I like people who didn't loose the sense of their souls. That makes them human.

Sometimes I say to people that _I like humans_. And it's often awkward. Like if I
would say that I like spiders or snakes. You know, like it is some weird fetish
to me. A way to not conform to the norm.

But, I do like humans. I find us beautiful. In all the things we do: social protocols,
arts, science. Even in the shit we are taking each other through for whatever reason.
Human behavior is so complex, but animal at the same time.

And it means a lot to me when I see a person, who in all the mess of human conditions,
finds some sense of dignity. This tells me that they didn't give up on themselves.
This tells me that they still have a soul.

Vonnegut does things to your soul. Not pretty, nor easy ones. But, somehow, it leaves
you with the sense that you are understood. That you are not alone.





mighty_hacks 2015-03-26-how-to-convert-cron-schedules-between-time-zones.md

# How To Convert Cron Schedules Between Time Zones

So, here is a bit tricky one. Say you have a service that performs some tasks for users on a schedule, and lets say you allow your users to add those tasks by using the [cron schedule format](http://en.wikipedia.org/wiki/Cron).

## The Problem

While it's a rock solid business idea, it has a tricky tech problem. Your server, the one that handles the tasks, is in one time zone, but the actual users are all over the globe. So, when a user in Moscow says `0 1 * * *` (every day at 1:00pm) your server in the  Silicon Valley will run it 10 hours later at `11:00pm` Moscow time, which, might not be what's expected.

So, you need to convert the cron schedule from a user's time zone, to the server's timezone. How do you even?...

## The Solution

Although it seems a bit tricky task, it has a rather simple solution. I've done it in #ruby and #rails, but it can be replicated in any language using the same principle. In a short, the idea is that you convert the schedule into a clean timestamp, then convert that timestamp into the target time zone, and then you convert the timestamp back into a schedule.

```ruby
# breaking it apart
min, hour, dom, month, dow = cron.split
dow = "7" if dow == "0" # in cron both 0 and 7 mean Sunday
```

That was easy, now we need to convert all those `min`, `hour` and stuff into a time stamp

```ruby
# converting the cron data into a timestamp
Time.zone = current_user_timezone # wherever it lives
t = Time.zone.now.beginning_of_month.end_of_week + 14.days + 1.second

t = t + min.to_i.minutes if min  != "*"
t = t + hour.to_i.hours  if hour != "*"
t = t + dow.to_i.days    if dow  != "*"
t = Time.zone.local(t.year, t.month, dom.to_i, t.hour, t.min, t.sec) if dom != "*"
```

There are two tricky moments here. Firstly, you need to make sure that you set `Time.zone` to the current user's timezone and then use `Time.zone.now` to get the current time sample. Secondly, you need to get a clean time stamp at a beginning of the second week of the month. The reason for that so we could move and transform the time without hitting the ends of the month and making things jump back and fourth.

So, this should build you a clean time stamp in the current user's timezone. All the important attributes of the original cron schedule should also be transferred onto this timestamp.

```ruby
# converting the timestamp back into a cron line
t = t.in_time_zone(your_server_timezone)

min  = t.min    if min  != "*"
hour = t.hour   if hour != "*"
dom  = t.day    if dom  != "*"
dow  = t.wday-1 if dow  != "*"

cron = [min, hour, dom, month, dow].compact.join(" ")
```

This is a pretty much routine part. We just convert the schedule timestamp into the server's timezone and then extract all the necessary data from that new timestamp. After that we just rebuild the cron line back into a string.

## Conclusion

It's a bit of a messy thing to do, but cron schedule lines can be converted between timezones, which yields in a much better end user experience than nagging users with mediocre solutions like say asking them to use UTC or something like that.

Also, this solution doesn't obviously handle the `*/num` cron formatting, but that's another story.





mind_fucks 2015-05-21-error-avoidance.md

# Error Avoidance

The fun thing about humans is that, in pretty much any situation, you can characterize their behavior based on two simple instincts: they will avoid pain and they will seek intense pleasure. Well, the lengths at which a person will follow those instincts may vary, which brings us to the today's topic.

Error avoidance is a result of a completely natural reflex of pain evasion. I'm sure you saw it yourself many times "oh no, i've made a mistake, i'm a disgrace to my family!", "oh, my thing segfolted in production, i probably should quit programming". People generally don't like making mistakes, the consequences can be painful. So, the natural response is to avoid pain and make as few errors as possible.

Now, while there are, in fact, cases in life when you need to be extremely cautious and make as little mistakes as possible. Say, you are performing a brain surgery, or transferring some one's life savings to another bank account, or programming a self-destruct sequence on a nuclear war-head. Yeah, you need to be damn sure you didn't make any errors. But, in the overwhelming share of cases, all your mistakes will lead to is embarrassing. Which exists pretty much in your head only.

The reality is, errors are necessary for growth. Every error is an opportunity for you to understand your shortcomings and improve your skills. There is no substitute for it. Errors are vitamins and micro-elements of your personal growth nutrition system. Remove those from you diet and you will rob yourself of reaching your true potential.

PS: I really want a t-shirt with Clint Eastwood saying to Sister Sara: "everybody's got a right to be a sucker once"





silly_thoughts 2015-05-28-do-not-work-with-insecure-people.md

# Do Not Work With Insecure People

Most of us, at some point, read an article named something like "don't work with a-holes" or "don't hire jerks". And although, intuitively, most of the subjects those article cover make total sense, I personally find them quite unsciency.

My problem with them is that words like "a-hole" and "jerk" are quite vogue in their meaning. Also, they are often misused. It almost seems that every time someone doesn't particularly like someone else's behavior they call them an a-hole. Because, you know the rule: the one who calls someone an a-hole first has the right to take the moral high ground in the rest of the conversation.

When you operate with such vague empirical maters, it's hard to grasp the actual underlaying processes. So, I thought, maybe I could try to explain such group dynamics through a more established terminology. And maybe, that will shade some light onto the underlaying principles and help someone to identify troublesome individuals more precisely.

In my opinion, the core of the problem is actually in what is called "insecurity". And when we are talking about insecurity it is crucial to understand the difference between insecurity and low self-esteem.

See, at any point in time, there are always people who are more confident and less confident than you. Some people think pretty low of themselves, and some others, well, have pretty high view of their own image. The important thing to understand is that confidence on its own does not define a person's negative behavior. An individual with a low self-esteem can demonstrate an assertive behavior and ask for help from those who they deem more confident and experienced. And the other way around is also true, a highly confident person, can always offer help to those who, in their view, are struggling.

The problem starts when you add the sense of insecurity into the mixture. Insecure people with low self-esteem will guilt-trap those who are above them on every corner. They won't try to grow and become better at what they do. Instead, they will try to drag more successful people down. And the same way, a high self-esteem person with a great dial of insecurity will try to do his best to not let anyone below them to raise. They will put them down on every step of the way, demoralize and do everything in their power to prevent others from evolving. That what you call a classical "a-hole" model.

As you can see from this demonstration, the issue is actually more systemic than it's usually portrayed in mass media. Problem is that insecure people see others as rivals and competitors in life. And that include you too my friend. Technically it doesn't matter where an insecure person is on the confidence scale, they will sabotage whatever it is you're trying to accomplish in life. You can build and infinitely awesome piece of machinery and they will just casually toss a wrench in it. That is the cornerstone of their behavior.

See, most of normal human beings know that there is enough good for everyone. We know that it is okay and very important to help each other; that it is the basis of a sustainable society. Unfortunately, insecure individuals don't see the world this way. They are socking in fear of any competition. And that what turns them into "a-holes" and "jerks" and "boner killers" when they can't get their way or feel threatened by others.

So, my advise, instead of watching out for vague "a-holes" and "jerks", try to identify insecure people. Remember that you are a human being, just like everyone else. You have feelings and you can get mad at someone for whatever reason. You might even go as far as calling them an a-hole, but that doesn't mean that this person can't be a valuable contributor to your causes. Insecure people, on the other hand, even if they won't show directly their jerkiness (passive-agressive characters for example), will, most definitely sabotage your work and leave you frustrated. Because that what they do.

So, don't work with insecure people.





mighty_hacks 2015-06-15-marshalized-jobs.md

# How To Marshalize ActiveJob Arguments

As we all know #rails 4.2 have finally given birth to the `ActiveJob` component, which,
allegedly, will congregate all the other background job queues under one unified API.
While that is a totally awesome and long awaited feature, there is one gray area
issue that you might run into. The arguments that an active job instance might take.

## The Problem

The problem is that an `ActiveJob` instance only can take simple strings and numbers
as arguments (or a combination of those in arrays and hashes).

Basically it is done as a safeguard to protect you against some side effects that
passing an `ActiveRecord` instance into a background job might cause. Say, for example
you passed an object to a queue and then deleted it from a database. God knows what
might happen; so they force you to use record ids as attributes.

## The Options

But, some of us actually want to lead a dangerous life and pass complex objects
into background jobs. DANGER, DANGER!

<iframe width="420" height="315" src="https://www.youtube.com/embed/2a4gyJsY0mc?rel=0&amp;controls=0&amp;showinfo=0&amp;t=92" frameborder="0" allowfullscreen></iframe>

For example, I'm quite a fond of value objects when it comes to a complex business
logic. They a simple throw away instances that incapsulate data and probably some
smarts around it. Unfortunately, the data in them is not persisted and not really
meant to.

## The Solution

So, in any case, if you find yourself in the same situation and want to pass value
objects into a background job, here is how you can do that with `ActiveJob` filters

```ruby
class MarshalizedJob < ActiveJob::Base
  before_filter do |job|
    job.arguments.map! do |arg|
      Base64.encode64(Marshal.dump(arg))
    end
  end

  before_perform do |job|
    job.arguments.map! do |arg|
      Marshal.load(Base64.decode64(arg))
    end
  end
end
```

Once you have that, just use the new `MarshalizedJob` class as the parent for your
specific jobs that will take value objects as parameters.

__NOTE__: you need to have both `Marshal` and `Base64` encoders, because #ruby marshalizer
will spit out binary `ASCII-8bit` strings, which left alone, will cause encoding problems
down in the pipes, as `ActiveJob` encodes everything into `JSON` later on when it passes
data to sidekiq/redis.

## Final Notes

I just wanted to add couple of personal notes. Firstly, I'm really pleased with the flexibility
of `ActiveJob`; hail to rails core team! They listened to the things the community was building
for years and they didn't disappoint.

Secondly, although the reasoning behind the limitations on the argument types is totally
understandable, we need to acknowledge that there are developers who are spoiled by other
languages and tech. So, it feels like there should be a config option out of the box in
#ActiveJob that will allow one to serialize value objects transparently without us needing
to monkeypatch it with callbacks every time.





mighty_hacks 2015-06-16-building-streetfighter.md

# Building Street Fighter II in Ruby

A few weeks ago, I decided to stretch my #gamedev chops a bit and
built some basics of the Street Fighter II game in #Ruby. That turned
to be more fun than I expected, so, I also recorded a short video
that goes through the main steps of the process.

<iframe width="420" height="315" src="https://www.youtube.com/embed/_whiCEywodw?rel=0&amp;showinfo=0" frameborder="0" allowfullscreen></iframe>

The game is not fully finished at the moment, I had just put in some basic
implementation of the main game scene, controls, animations etc. I pushed
it all to GitHub [MadRabbit/ruby-fighter](https://github.com/MadRabbit/ruby-fighter),
so if you feel like playing a gamedev as well and give it a go, it's all there!





might_hacks 2015-06-19-responsive-sidebar.md

# Responsive Sidebar Out Of Nothing

If you're looking at this page in a desktop browser, try to shrink the
browser's window to a narrow, mobile like width. You should see that
the sidebar folds and becomes this sandwich icon in the header. So,
today I'm explaining how to do that with a minimal markup and some
basic #css voodoo.

## The Problem

I, personally, like my markup as simple as I can get away with. There are
couple of reasons for that. Firstly, obviously, when you don't have much
of a clutter of css classes and strange seemingly purposeless elements,
it is much easier to reason about the system in general. When you see a
bunch of simple, semantically marked elements, you can quickly build the
app structure in your head, and then move things around with a good dose
of confidence.

Secondly, keeping markup simple kind of forces you to think more about
the context of those elements; and good cascading styles are all about
context. It does, on average, make you write more #CSS, but it makes
you write better, more flexible CSS. In the end, it really pays off in
cases like #responsive design or graceful degradation in older browsers.

Let me demonstrate. If you look at this page via an inspector, you'll
see a structure kind of like that:

```html
<body>
  <header>...</header>
  <main>
    <div class="paddings">
      <article>
        Main article text
      </article>
      <aside>
        The sidebar stuff
      </aside>
    </div>
  </main>
  <footer>...</fotter>
</body>
```

Then this structure is transformed with some simple #flexbox css rules.

```css
main .paddings {
  display: flex;

  article {
    flex: 1;
  }

  aside {
    width: 300px;
  }
}
```

So, basically it's a very simple two columns deal where the sidebar has
a fixed width and the `ARTICLE` element takes the rest of the available
space in the container.

## Make It Responsive

As I mentioned before, when it comes to minimalistic markup, you need
to think about the context, and in this case the context will be a different
width of the browser window. I personally prefer to show the sidebar on
an iPad in the landscape mode, and then fold it in the portrait orientation.
That makes it nicely stashed away on all the mobile phones as well.

So, in this case I'm using a media query with `max-width: 768px`

```css
@media (max-width: 768px) {
  main aside {
    position:   absolute;
    top:        0;
    right:      0;
    min-height: 100%;
  }
}
```

This will break the flexbox structure and will make the sidebar to appear
as a tall column on the right, which will hover over the main content. Kind
of like the open state sidebar.

Now we need to add a toggler element that will flip the state of the
sidebar from open to hidden. I would normally just put an `A` element
right on the inside of the `ASIDE` block.

```html
<aside>
  <a href="" class="toggler"></a>

  <section class="about"> ... </section>
  <section class="contacts"> ... </section>
  <section class="categories"> ... </section>

  ...
</aside>
```

You will need to make that `.toggler` element hidden on desktops and only
show the element when the sidebar folds. That is simple

```css
main aside {
  a.toggler {
    display: none;
  }
}

@media (max-width: 768px) {
  main aside {
    a.toggler {
      display:  block;
      width:    2em;
      height:   2em;
      position: absolute;
      left:    -4em;
      top:      1em;
      /* the rest of the styles */
    }
  }
}
```

This way the toggler will only appear when it's needed. Once you have that,
you will need to connect the button to some piece of javascript that will
toggle the `open` class on the `ASIDE` element.

```css
@media (max-width: 768px) {
  main aside {
    position:   absolute;
    top:        0;
    right:      0;
    min-height: 100%;
    transform:  translateX(300px);

    &.open {
      transform: translateX(0px);
    }
  }
}
```

As you can see I used the `translateX` property to move the sidebar right
and hide it beneath the right edge of the screen. And then, when the `open`
class is added, I move it back into the view.

## Lets Make It Smooth!

It is possible to do the same thing by using the `right: -300px` property
on the element and then reset it to `0px` with the `open` class, but I
prefer to use `translate` because, well, one way or another you'll boss
will want the sidebar animated, and then, `trasnform` will save your skin.

See, the problem is that when you use geometry attributes like `right`, `left`,
`width`, etc. The browser will have to recalculate the page geometry on every
step an animation. That makes the movement often look quite jerky.

But, when you use `transform: translate` properties, the browser starts cheating.
It basically takes a picture of the element and then moves it across the screen.
So, visually it might look like they do the same thing, but in reality `transform`
will use much less CPU resources and hence animate it smoothly.

```css
@media (max-width: 768px) {
  main aside {
    // ....
    transform:  translateX(300px);
    transition: transform .4s ease-out;

    &.open {
      transform: translateX(0px);
    }
  }
}
```

Now the sidebar should move smoothly back and fourth!

## The Locker Screen

One more thing you will need for you awesome responsive sidebar is a an
overlay that will lock and shade out the the main content while the
sidebar is visible.

That can be easily done with a css generated element.

```css
@media (max-width: 768px) {
  main aside {
    // ....
    &.open {
      // ...

      &:before {
        content:    " ";
        display:    block;
        background: rgba(0,0,0,0.4);
        position:   absolute;
        height:     100%;
        width:      100vw;
        top:        0;
        right:      100%;
      }
    }
  }
}
```

As you can see, it is just a plain `DIV` element with a semi-transparent background.
The only tricky part is that I used the CSS3 `100vw` value, which makes the block
to have the same width as the window. And then I moved it completely out of the `ASIDE`
element with the `right:100%` setting.

## Conclusions

That is pretty much all the basics of the implementation. After that, you can add
some bells and whistles, like a rotating toggler icon, and so on, but the main mechanics
will remain the same.

The cool thing about this approach is that it is highly flexible and has a great deal
of separation of the concerns. Your HTML and JavaScript are only concerned with providing
the controls and keeping the state; all the visual styling is done completely in the #CSS
layer.

And the best thing is, that we kept the CSS itself highly contextualized and declarative.
Every state is clearly described and you can extend them with more fine grain details
without chances to ruin the main functionality or a need to change the DOM structure.





mighty_hacks 2015-06-24-responsive-fixed-ratio.md

# Responsive Elements With a Fixed Aspect Ratio

This problem have a tendency to hit me from the blind side now and then.
How do you make a #responsive element with a flexible size, which keeps its
aspect ratio when stretched with the design?

For example, you have an avatar image, like the one in the sidebar, and you
want to keep it square by using pure #CSS. You can make its width flexible
by setting the `width: 100%` style on it. But how do you specify the height?
The size might differ from a context to context.

Well, as it is with many things in life, the trick is that you have to stop
asking the question directly, in this case _"how do you i specify the height?"_,
and look for other options.

## Use The Paddings Luke!

Turned out, you can specify the `padding-bottom` property on an element
in percents. If there is a `width` was set on the element, that will make
the browser to calculate the _bottom_ padding relatively to the element's
width.

For example the block below will always keep the aspect ratio of `1/10`.
Try to stretch the browser window back and fourth to see it in action.

<div style="width: 100%; padding-bottom: 10%; background: orange;">

</div>

The style for it looks kinda like that

```css
div.one-tenth {
  width: 100%;
  padding-bottom: 10%;
  background: orange;
}
```

## Special Cases

So, for a square element, like an avatar you might want to specify the
`padding-bottom` property to `100%`. Naturally. But one another case
that you will probably run into is a `16:9` video. Like this one.

<iframe width="560" height="315" src="https://www.youtube.com/embed/tHwntRpLobU?rel=0&amp;showinfo=0" frameborder="0" allowfullscreen></iframe>

To keep it perfectly sized in all the situations wrap the `IFRAME` in a
`DIV` and style them like so

```css
div.video {
  position:       relative;
  padding-bottom: 56.25%; /* 16:9 */
  padding-top:    25px;
  height:         0;

  iframe {
    position: absolute;
    top:      0;
    left:     0;
    width:    100%;
    height:   100%;
  }
}
```

That is all there is to this trick really. Enjoy!





silly_thoughts 2015-06-28-ielts-accepting-the-defeat.md

# IELTS: Accepting The Defeat

In the past 5 months I did a strange thing, I attempted to pass
the [IELTS](https://en.wikipedia.org/wiki/International_English_Language_Testing_System)
exam with a minimal score of `8` (out of `9`) in every sub-task. I had
_7_ attempts and the best results look like that:

 * Reading   - 9
 * Listening - 9
 * Writing   - 7.5
 * Speaking  - 7.5

And, although it might look like the results are within a margin of
an error, I eventually decided to stop my attempts and let it go. And
this is basically the analysis (a.k.a "i learned a lot") post about
the experience I had with IELTS in Australia.

But, firstly, you might wonder why one would subject himself to a
such unusual ordeal. The answer in my case is: _"I wanna be free!"_.
I want to end the macroeconomic slavery period of my life and get a
permanent residency in AU. Unfortunately, due to some complications
with my background, I need to demonstrate a _superior_ level of the
English language skills.

Roughly 5 years ago, I took the IELTS exam in Russia. I did it almost
without any serious preparation, just for the fun of it, and I scored
7.5 on average. I figured that in those last years I have improved
rather dramatically, so, scoring 8, shouldn't be a problem. I expected
that I might need a few attempts, but, generally, it should be achievable.

Unfortunately, it turned to be much harder than I expected. I consistently
scored 8.5-9 in the reading and listening sections. But, when it went to
the writing and speaking blocks, the score was around 7-7.5.

It didn't make any sense to me. I know I have a 100% clear understanding
of whatever people say around me. I read compulsively. I actually
read faster than an average native speaker. I also present regularly
at meetups and conferences, and generally known for talking too much.
And, I write for living. So, why such a drastic difference between those
two groups?

Well, it turned out that the reading and listening sections have strict,
easily quantifiable ways to measure the results. They are actually checked
by a computer. Writing and speaking on the other hand are measured by the
"trained professionals". And, apparently, those professionals don't really
want us in here.

See, the only real reason why one would want to get a score of `8` in
IELTS here in Australia is that they want to immigrate. So, there is
a whole industry formed around it. There are a half a dozen of testing
centers only in Sydney. I've been to a half of them and they are packed!
This is ridiculous for a 5 million people city.

People are just thrown into this vicious cycle. In the last 6 months I
have heard countless stories about people who would score 8.5 in their own country,
but then barely scratch 7.5 here in Australia. Especially if you are an Asian
or an Arab. People spend literally years trying to get the score here in Australia.
Many Asian kids, they just fly back home to Vietnam or Hong Kong, to take
the test, because it is almost impossible for them to pass the bar here.

Well, I've heard the message. It's a clear "nope". And I don't really want to
play this game anymore. So, I'm sidestepping. I'll try to give my freedom
units to the American TOEFL test. Will see how that will turn out.





book_worm 2015-07-02-assertiveness-workbook.md

# Assertiveness Workbook

Do you often have a feeling that everyone around you is an idiot
and it is really hard to get anything done because of that? Or
maybe you have a feeling that you work too much and people around
you are just a-holes who abuse your kindness? Well, guess what?
It's your own fault.

It is unbelievable how much crap we humans have in our heads.
Crap that makes us suffer and crap that makes everyone around us
suffer as well. We just label it something like _"human condition"_
or _"that's who i am"_ and keep doing the same thing over and over.
So frustrating.

Some of us had a fair chance in life, some of us had great role
models when we were growing. Some of us even were so lucky that
we were explained how it all works and how unhealthy behavioral
patterns can limit our options in life. But, some of us weren't
so lucky. Some of us require some help to get better at life.

This is not really a book in a normal sense of a book. This is
not kind of thing that one could just read through and miraculously
change their life (if there are any books like that at all).
"Assertiveness workbook" is just what it says; it is a workbook
that have lessons and exercises that can help a person to overcome
their unhealthy communication habits.

Although it sounds a bit dull, it's actually a great thing to read.
It explains in great details different communication styles, like
passive, aggressive, passive-agressive and dynamics of their interactions.
A lot of those things intuitively make sense, but the great thing
about this book is that it gives you a model and a language. That
helps you understand and reason about people's behavior. It will
help you to realize the roots of the problems you might have with
other human beings.

This book obviously won't cure you. It won't change who you are.
But what it will do, it will give you a fighting chance. And
sometimes that's all you really need.





mighty_hacks 2015-07-03-connect-body-patch.md

# How To Patch Response Body in Connect Server

This thing took quite a bit of tinkering to figure out. And apparently
some other folks also struggle with the ins and outs of the problem as well.
So, today's topic is _"how do you patch response body in #nodejs connect
server middleware?"_

## The Problem

I quite like [connect](https://github.com/senchalabs/connect) server.
It's just a very simple thing that allows you to bolt middleware on
top of the stock `http` server in #nodejs. It's super lightweight,
fast and easily extendible.

But, the problem is that it's an one direction pipeline. If some
piece of middleware piped some data into the response and called
`res.end()`, your own middleware won't have an easy way to modify that
response.

For example, `nikolay.rocks` is built with [react](http://reactjs.com).
And I'm kind of being naughty and rendering the whole app directly into
the `document.body`. So, when [gulp-connect](https://github.com/AveVlad/gulp-connect)
injects the livereload script, it embeds it on the inside of the `BODY`.
But then the react app completely wipes the `BODY` content and and breaks everything.
Hence I needed to post-process the response body and move the livereload
script outside of `BODY`.

## The Solution

To solve the problem you'll need to inject a middleware into the
connect's pipes.

```js
connect.server({
  root: "./",
  // other prams
  middleare: function() {
    return [
      super_patch // <- that's our middleware
    ]
  }
});
```

The `super_patch`, as any other connect middleware will take three params
`req`, `res`, `next`. The trick here is to re-wrap the `req.end` function
with what we need. It looks kind of like that:

```js
function super_patch(req, res, next) {
  var real_end = res.end;

  res.end = function(string, encoding) {
    if (string) { // can be undefined on 302, etc. requests
      string = string.replace(/mighty hacks/, 'MIGHTY HACKS');
      res.setHeader('content-length', Buffer.byteLength(string, encoding));
    }

    real_end.call(res, string, encoding);
  };

  next();
}
```

That's pretty much the whole solution. But there are two important things
to keep in mind thought. Firstly, the `string` can be `undefined` in some
cases; for example a redirect or a `HEAD` request. Secondly, you need to
set the `content-length` header with the new content size, because it's
set to the original by default. A mismatch might make the browser to hang
waiting for more data from the server.

Needless to say that it's quite a naughty thing to do and it might break
some content for you. So, in a real life situation it might be a very good
idea to scope the hack by checking the `req.url` or response content type
before applying the patches.





mind_fucks 2015-07-05-prototype-matching.md

# Prototype Matching

Prototype matching (don't mistake with prototype modeling) is one of
the techniques in the decision making theory. Basically, it is a bit of a
mind hack that helps to make a decision in a situation when differences
between options are not so clear or incomparable directly to each other.

The principle goes something like that. When faced with options that
are unclear or hard to compare, instead of trying to make lists of
pros and cons, and compare apples to oranges, it can be more efficient
to think of archetypical people who would pick each of the option.

For example, lets say you're thinking _"should i learn ruby or javascript?"_,
you might imagine two dudes: one is bearded and knowledgeable, but dislikes
everything non-ruby; the other one might be young, a bit sloppy and naive,
but have an open mind. You obviously can think of a female developer if you
want, the point here is to imagine archetypes that make sense to to you
personally.

Then, once you have those archetypes, instead of focusing on comparing
ruby to javascript directly, try to think to which of those two archetypes
you relate more. The one you feel more connected to, will most likely be
associated with the better option.

The science of this technique is basically in cognitive dissonance
minimization. It helps you pick the option about which you care more
deeply. In a long run that might be just the only thing that matters.

So, the next time you choose a language or ask yourself whether you
should work for a company `A` or a company `B`, try to think of some
associated archetypes. After all the company won't become like you,
you will become like them; so, try to pick what is connected to your
sense of yourself better.





mighty_hacks 2015-07-07-load-private-images-via-ajax.md

# How To Load Private Images Via Ajax

So, here is a fun problem. Lets say you have a website where
users can upload some images and then access them privately
when signed in. For example you might be using a simple #rails
controller kind of like this one:

```ruby
class ImagesController < ApplicationController
  before_action :require_user

  def show
    image = current_user.images.find(params[:id])
    send_data image.blob, type: :jpg, disposition: :inline
  end
end
```

This will work perfectly in a classical rails app with a
session based #authentication system. The browser will send
cookies along with the image request and the `current_user`
will magically pop up in the `ImagesController` just as expected.

The problem is that you might be living in the early 21st century
and you're might be building an awesome JSON API based app,
say with #angularjs. If you have done everything right, you
will be authenticating your users with an auth-token in the HTTP
headers. Say something like that:

```ruby
class ApplicationController < ActionController::Base
protected

  def current_user
    @current_user ||= User.authenticate(auth_token)
  end

  def auth_token
    request.headers["AuthToken"]
  end
end
```

In this case, there will be no cookies and when the browser sends
a request for the image, it won't send any headers either. So,
you'll request will be completely anonymous and the server will
rightfully throw a `404`.

## Brut Force Solution

The solution to the problem is obviously to send somehow an auth-token
along with the image request. And the first thing that might spring
to mind is to just add the auth-token to the image url:

```html
<img ng-src="/images/123.jpg?auth_token={{auth_token}}" />
```

That might work. But, unfortunately you will have to drag that
`?auth_token=` everywhere in your front end. Then you will have to
support two authentication types on the server side as well. Plus
make sure that your angularjs `$http` header configurations don't
get misaligned with the `?auth_token=` hacks.

That will be a mess and quite an error prone one.

## Better Solution

A better solution would be to load the image via your front end
ajax system. In case of angular with the `$http` module. It will
most likely already have all the necessary headers and configurations
for your JSON API access, so your image requests will fit right in.

A nice way to implement the solution would be to build a simple
directive, something that would look like that:

```html
<img private-src="/images/123.jpg" />
```

Then make some #javascript code like so

```js
App.directive("privateSrc", ["$http", function(http) {
  return {
    restrict: "A",
    scope: { src: "@privateSrc" },
    link: function(scope, element) {
      http.get(scope.src, {responseType: "blob"})
        .success(function(blob) {
          var converter  = window.URL || window.webkitURL;
          element[0].src = converter.createObjectURL(blob);
        });
    }
  }
}]);
```

It is pretty straight forward, but there are few things worth
mentioning. Firstly, you need to specify `responseType: "blob"`
so that the browser treated the XHR response as a binary data.
Secondly, you will need to convert the blob into a source object
for the image with the `window.URL` service. (this feature is
called [blob urls](http://caniuse.com/bloburls) in case you want
to dig deeper into the topic).

As the result, we have an easily reusable and highly maintainable
solution that fits right into your existing system.

## Extra Awesomeness!

One cool side effect of this approach is that you can visualize
the image loading process to your users and make your UI appear
more dynamic. For example, you can add the `loading` class to the
image while it's being loaded:

```js
link: function(scope, element) {
  element.addClass("loading");

  http.get(scope.src, {responseType: "blob"})
    .success(function(blob) { /* dealing with the blob */ })
    .finally(function() {
      element.removeClass("loading");
    });
}
```

Once you have that, just add some #css sprinkles on top of
the cake:

```css
img.loading {
  content:    " ";
  background: rgb(0,0,0,0.5) url("spinner.gif") no-repeat center center;
}
```

Don't forget to specify that `content: " "` thing, it will
remove the weird border around the image while it doesn't
have any source in it yet.

__PROTIP:__ you can use `base64` animated gifs with `background-image`
properties. Saves you one tiny http request :)





silly_thoughts 2015-07-10-ielts-vs-toefl.md

# IELTS vs. TOEFL, Things You Need To Know

As I [mentioned before](/2015-06-28-ielts-accepting-the-defeat), in
the last few months I took the [IELTS](https://en.wikipedia.org/wiki/International_English_Language_Testing_System)
test several times in order to score 8 (out of 9) in every section
of the test. Then, for a comparison, I took the American
[TOEFL](https://en.wikipedia.org/wiki/Test_of_English_as_a_Foreign_Language)
exam, where I scored 109 (out of 120). And now I just want to write a
bit to compare those two and maybe give some suggestions which one you might want to take.

Generally speaking, both IELTS and TOEFL are English language exams
and measure relatively the same thing. You can even find some
spreadsheets on the Internet that compare scores in both tests
and show how they correlate to each other.

In reality they are actually two rather different things. IELTS is
a British old school exam, which is done with a paper and a pencil. And
TOEFL is an American test, which you take in front of a PC (yeah, I touched
windows, ewww). Furthermore, IELTS is usually taken as a _general_
grade test and TOEFL is more an _academic_ thing that you might need
to go to a uni.

But, lets do a piece by piece comparison to get a sense of it.

## The Reading Section

Generally, reading in TOEFL is a bit more challenging than IELTS.
If in IELTS you would normally read some ads and articles from the
times when paper news were cool, then in TOEFL you will see more
academic scrolls. You don't need to have any specific prior knowledge
in both cases to understand those texts. But, TOEFL texts have more
academic grade words and phrases in them, when IELTS is pretty much
what one could see in a news paper article or a casual blog post.

Where those two really differ is how they test the level of your
understanding of the materials. IELTS is really easy, you just need
to skim the text and find some matching words and then write them
down on a piece of paper. If you read regularly, it is hard to
screw it up. The only challenging part is when they ask `true/false/not given`
options. They often present it that there can be two right answers,
but they will accept only one of them as the actually correct one.
You don't always get to be right in IELTS, even if you know what
you're doing.

In TOEFL, on the other hand, questions are much harder and they
really test your detailed understanding of the text. More of that
they have sections of questions where they ask you to pick a closest
in meaning word from a list. The differences between the options
can be blurry at times. So you really need to know the precise
meaning of words to answer everything correctly.

## Listening Section

Generally speaking, IELTS listening section is easier. You just
listen to some conversations and write down the answers in a paper
as you hear them. But, the problem is that they always try to
trick you with some weird accents or a poor sound quality. Sometimes
a person can unexpectedly fire up a mobile phone number
or a 10-digits bank account, and you supposed to write it down.
I think that is a very specific skill that doesn't have much to
do with your understanding of spoken English.

In TOEFL, you listen to a set of lectures and conversations,
and then you answer some questions about them _afterwards_. This
requires you to have some good note taking skills (a pencil and
some paper are provided). But, I think it is a much more fair
and consistent way to test your ability to fully understand
audio materials.

Not to mention that in TOEFL you listen to those pieces
through headphones, not via some crappy speakers on the other
end of a class room.

## Speaking Test

The speaking sections are actually completely different in IELTS
and TOEFL. In IELTS it is done in a form of a casual interview.
You just chat to a real person on every-day topics. Then you
are given 2 minutes to speak on some specific subject. Then you
answer some related questions about the subject. And that's it;
overall ~10minutes.

In TOEFL you speak into a mic. They make you read some pieces of
text or listen to a conversation and then ask you to summarize
the details of those pieces in a short ~45 seconds burst. You have
several sections like that, but generally the principle is the same.

I find that both tests have their ups and downs here. In IELTS
you are get measured in real time, which makes it highly subjective.
Maybe the examiner didn't have a stellar day, maybe they didn't
like your haircut, or maybe they got beaten up by Russian immigrant
kids when they were in a high school. You will never know, all you
have at the end is a "thank you" and a poker face.

In TOEFL on the other hand you don't have a space to kidna build
the case and present your thoughts in a logical and concise manner.
Because the window is so small, you need to fire up right away
and hope that you will be on time to finish up your thought. But
then there is no human factor, you are measured anonymously and
all you have to do is to speak clearly and make sense.

## Writing Section

That is where IELTS fails completely in my mind. In IELTS you need
to write (on actual paper) a _formal_ letter and a _formal_ essay.
The first one is ~150 words long and the second one is ~250.

There are several problems with that. Firstly, in 21st century
we don't kill trees to communicate anymore. The last time I wrote
something on a piece of paper before the test was probably ~15 years
ago. It is a horrendously annoying thing to do now. Secondly, no one
writes formal letters anymore. The expected format of the text is
utterly obsolete. No one writes letters, sprinkles them with some
perfume and sends it with a post man anymore.

The only thing it tests really is your ability to pick up some dead
imperial traditions of the past. But, the real trick here is that,
the examiner is allowed to judge you and cut your score if they
feel like you memorized some of the phrases. Start to use large
fancy words and exemplary sentence structures and they will cut
you down. It is really hard to nail it down to they way an
examiner will like it. And, unfortunately, it has very little to
do with your real life ability to express yourself in a written format.

TOEFL is quite a bit more civilized in this regard. Firstly, you
use this awesome human invention, we call it "a keyboard". Secondly,
you write it on a computer screen, so you can edit and fine polish
the text without messing it up. Thirdly, you are actually granted
extra points if you write more text! (in IELTS no one cares)

You will still have to follow a formal essay structure in TOEFL
though. But that is a good, proper skill anyways.


## Overall

Overall, I personally liked TOEFL much more. Where IELTS mostly
tries to tick you in some weird circumstances and just penalizes
you for that, TOEFL actually tests your ability to process information
and express yourself in English.

IELTS is generally easier but it just scrupulously watches an every
tiny detail. TOEFL hits you with more complex information, but it tests
your actual English language skills, like: note taking and detailed
knowledge of some more academic words.

So, which one should you take? Well, as I see it, there is a trick
to it. If you are allowed to take either of them, then you should
look at which score you need to produce.

If your target score is ~6-7 in IELTS terms, then go with IELTS.
It is actually an easier test and you will have plenty of headroom
for mistakes in the test.

But, if your goal is a high score, like 8 and above, then definitely
go with TOEFL. Unlike IELTS, TOEFL allows a certain degree of irregular
mistakes and, instead of being obnoxious about tiny irrelevant details,
it measures your real life English language skills and then grades
your high band performance more fairly.

Besides, when it comes to the high scores, in my experience, IELTS showed
very little correlation between the effort and the end result. So, I'd
definitely go with TOEFL if I needed to score a high band.





rocket_shed 2015-07-11-welcome-to-the-shed.md

# Welcome To The Shed

I'm starting a new section here called "the rocket shed". This
will be a section for posts related to my work on some of my
personal projects and probably some business related blabing.

There are few thoughts and stuff that I want to record as I go
through the projects. They are most likely will be rather technical,
but it is not for the tricks of the trade kind of posts that
I write in [Mighty Hacks](/categories/mighty+hacks).

The [Rocket Shed](/categories/rocket+shed) is where one steps
beyond mere technical abilities and tries to build an actual
rocket and see how high it will fly.





rocket_shed 2015-07-13-meet-the-meowtv-app.md

# Meet The MeowTV App

A few months ago, my wife and I, we adopted a cat. Say "hi" to Muffin!

![](http://zenmagiclove.com/misc/rocks/muffin-11.jpg)

He is an awesome little dude. Being a digitally native cat, he just loves
playing with iPad. Initially he would spend literally hours playing with
some apps made for cats. But, as cats usually are, he soon would get tired
of playing the same game over and over. He needs some variety. And here
is where the interesting part starts.

Turned out that the market for apps for cats is really tiny. There is just
a hand full of apps that are worth checking in there. In a month he run
through them all and kinda got bored. Started to ask for more.

We have tried this and that, and accidentally stumbled on some videos
on youtube which are made specially for cats. The usual stuff, birds,
fish, squirrels. The problem is though, the YouTube player is made for
humans. Muffin would just get too agitated and start touching the screen
messing up the playback.

And here is where it occurred to me that cats need a special type of video
player designed specifically for their style of watching the movies.

Meet the [MeowTV App](http://meowtvapp.com)!

The concept is really simple. It is just as any other online video player,
you can search for some related videos, hit "play" and enjoy the movie.
But, the difference is that it has a transparent overlay that will lock
the video playback controls away from a cat.

The result is actually pretty awesome. The app automatically finds some
new videos for your cat to watch. There are literally dozens of them
online, so that provides a good variety. Then you can put the video on
a loop and lock the screen.

Works actually like a normal TV for humans. A cat gets glued to the
screen and can enjoy the movies for quite a long time. Well, that's pretty
much it. If you have a cat and an iPad/iPhone, give it a try, it's free
and awesome!





book_worm 2015-07-14-startupland.md

# Startupland

Firstly, a bit of a word of disclaimer. I generally don't
like auto-biographies that much. I find them rather melodramatic
and full of half-truths. Given that, my views on this sort
of books tend to be skewed by this perceptional filter. So,
please take whatever I write in here with an extra grain of salt.

First and foremost of all, there is nothing really new in this book.
Those guys, they did most of the things right for a startup:
the started low, loved their clients from the very beginning,
focused on growth in the right moment, and finally, went for
an IPO. So, if you are looking for some revelations, you
probably might wanna look somewhere else.

But, other than that, it is a pretty good read. I for one,
certainly miss the awesome startup vibe of San Francisco.
There is truly nothing like it in the rest of the world.
It is one of those Lorenz attractors things that make everything
else revolve around them. And in this regard I enjoyed the
story a fair bit.

The real question that bugs me after the book though is:
"do you really need to do that?". What is the "new generation
of enterprise software"? Do you absolutely need to grow beyond
the limits, dominate the market and try to push everyone out?

I was left with an impression that Zendesk didn't start with
the goal for the world domination. But, it was good and ended
up being sucked into the Californian vortex. So, the question
is whether it is an inherited property of the market field, or
is it the inner desire of founders that lead them onto the
path of "enterprise software".

Is it even theoretically possible to build a best in the
world software product, but keep it handmade and organic?
If it will be a truly good product, you will owe it to the
users to eventually scale it, right?

I feel like I need to sidestep and start looking at the things
from a different perspective.

And a footnote. Uprooting your family and move them half way
across the globe to let you to pursue your dreams. Quite
a thing to do.





mighty_hacks 2015-07-18-easy-peasy-vector-icons.md

# Easy Peasy Vector Icons

There are a few techniques we used through the history of
web development in order to add icons to the UI. Individual GIF
files with IMG tags, PNG icons and backgrounds in #css, font icons
and `content` properties. But, there is one another, quite simple
and lean technique that is not that well known. It uses #css with
embedded #svg content. I've been using it quite extensively lately
on my projects, so I thought I could share the idea.

## The Problem

Although font icons are fantastic, in many cases they might
be an overkill and unnecessary maintenance overhead. Firstly,
you have a big ass web-font blob to carry around and secondly,
a substantial chunk of css mappings for the font glyphs that
also need to be loaded up one way or another.

It might be fine for a large web app, like a CMS or something
like that, where you use literally dozens of icons all over
the place. In this case it makes sense to have a whole set
of icons available at your disposal at any time.

But, for a small project, like say this blog for example, you
might need just a hand full of icons. So, you might want to
consider some simpler options.

## The Option

There is a nice alternative to the font based icons that will
provide you with smooth, low maintenance vector icons. You
can embed #SVG based icons directly into a piece of #CSS.

Firstly, you will need a SVG icon. I have found an awesome
source of those, it's called [iconfinder.com](https://www.iconfinder.com).
It has plenty of icons in various formats, both free and commercial
(for a reasonable price). Just go in there, find any vector icon
that has a plain SVG option, and download the `.svg` file.

Now, let's presume you have some #html element, like a link for
example, where you want to add the icon. Say a `delete`
link like this one:

```html
<a href="javascript:deleteStuff();" class="delete">
  Delete!
</a>
```

Now you can embed that SVG icon directly into the link with a
simple piece of CSS like so:

```css
a.delete {
  white-space: nowrap;
  display:     inline-block;

  &:before {
    content:             " ";
    display:             inline-block;
    width:               1em;
    height:              1em;
    background-image:    url('data:image/svg+xml;utf8,<svg ....>...</svg>');
    background-repeat:   no-repeat;
    background-position: center center;
    background-size:     contain;
  }
}
```

The trick here is the `background-image: url('...')` section. You
will need to open up that `.svg` file you downloaded form the iconfinder
and copy the part of it's content that has the `<svg>...</svg>` tags
in it. Sometimes that's all it will have, but sometimes it might have things
like `<!DOCTYPE` or `<?xml version="1.0" ?>` at the beginning of the file,
cut it out.

If you have done everything right, you will see something like this:

<p class="step-1">
  <a href="javascript:alert('deleting!')" class="delete">Delete!</a>
  <a href="javascript:alert('deleting!')" class="delete" style="font-size: 2em">Delete!</a>
  <a href="javascript:alert('deleting!')" class="delete" style="font-size: 3em">Delete!</a>
</p>
<style type="text/css">
  .step-1 a.delete {
    white-space: nowrap;
    display:     inline-block;
    margin-right: 1rem;
  }
  .step-1 a.delete:before {
    display:             inline-block;
    width:               1em;
    height:              1em;
    vertical-align:      middle;
    content:             " ";
    background-image:    url('data:image/svg+xml;utf8,<svg enable-background="new 0 0 500 500" height="500px" id="Layer_1" version="1.1" viewBox="0 0 500 500" width="500px" xml:space="preserve" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><path clip-rule="evenodd" d="M418.081,122.802h-59.057V68.29  c0-20.077-16.262-36.34-36.341-36.34H177.316c-20.078,0-36.342,16.264-36.342,36.34v54.513H81.918  c-12.536,0-22.713,10.177-22.713,22.715c0,12.536,10.177,22.713,22.713,22.713h13.629v263.48c0,20.078,16.262,36.34,36.341,36.34  h236.224c20.078,0,36.341-16.262,36.341-36.34v-263.48h13.629c12.535,0,22.715-10.177,22.715-22.713  C440.796,132.979,430.616,122.802,418.081,122.802z M313.598,122.802H186.4V97.367c0-11.083,8.909-19.991,19.991-19.991h87.216  c11.084,0,19.99,8.909,19.99,19.991V122.802z M186.4,186.401v218.051c0,9.992-8.181,18.172-18.17,18.172s-18.17-8.18-18.17-18.172  V186.401c0-9.989,8.18-18.17,18.17-18.17S186.4,176.412,186.4,186.401z M268.172,186.401v218.051  c0,9.992-8.181,18.172-18.172,18.172c-9.99,0-18.17-8.18-18.17-18.172V186.401c0-9.989,8.181-18.17,18.17-18.17  C259.991,168.231,268.172,176.412,268.172,186.401z M349.938,186.401v218.051c0,9.992-8.181,18.172-18.169,18.172  c-9.99,0-18.172-8.18-18.172-18.172V186.401c0-9.989,8.182-18.17,18.172-18.17C341.758,168.231,349.938,176.412,349.938,186.401z" fill-rule="evenodd"/></svg>');
    background-repeat:   no-repeat;
    background-position: center center;
    background-size:     contain;
    margin-right:        0.2em;
    margin-top:         -0.3em;
  }
</style>

As you can see, it scales perfectly when you change the `font-size`
property; because we have specified the size of the `:before` block
in `em` and made the `background-size: contain`.

If you're using #LESS preprocessor or such, it worth adding a simple
icons constructor like this:

```css
.add-icon(@data) {
  ...
  &:before {
    ...
    background-image: url(@data);
  }
}
```

And then embed your icons in links and buttons sort of like that

```css
@trash-icon: 'data:image/svg+xml;utf8,<svg ....>...</svg>';

a.delete {
  .add-icon(@trash-icon);
}
```

This way you can have the icons somewhere in a config and them quickly
reuse and embed them all over the place.

## Pros & Cons

As you can imagine, this approach has it's own merits and falls. Firstly,
you will have to handpick the icons yourself, which might be challenging
if you a design blind dev. Secondly, this approach is not that maintainable
in pure CSS; it's hard to judge from the code what's on the inside of the
image.

But the cool thing is that this is a very lean, extremely low overhead
solution where you need just a hand full icons. The SVG data will be
compressed really well by your web-server's `gzip` pipe. And as all the
data will be embedded right into CSS, there will be no dependent asset
files that you will have to drag around. All your icons will be available
immediately after the styles are loaded.

You can also hand pick all the icons you need and you won't be limited
to a particular vendor. You can mix them as you're pleased and pick the
best ones that fit your design. You also have an option to mix svg and
raster graphics if you feel like it. The `content` property will take
andy `base64` encoded PNG files as well if you need them.

And a final word of caution. Some resources might offer you a base64 encoded
SVG. Don't do it. SVG is a textual format (basically XML) and in it's
raw form will compress much better than the base64 encoded version. So,
there is really no point in using base64 encoding with SVG.





mighty_hacks 2015-07-20-fancy-checkboxes-out-of-nothing.md

# Fancy Checkboxes Out Of Nothing

We are continuing our series of posts about minimalistic markup
and #css black voodoo. And today I'm showing, how to convert
this:

```html
<input type="checkbox" />
<input type="checkbox" checked="checked" />
```

Into this (click, click, click):

<p class="full-example">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .full-example input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
    border-radius: 2em;
    transition: background .2s linear;
    cursor: pointer;
    background-color: white;
    background-repeat: no-repeat;
    background-image:
      radial-gradient(circle, rgba(255,255,255,1) 50%, rgba(255,255,255,0) 54%),
      radial-gradient(circle, rgba(0,0,0,.3) 54%, rgba(0,0,0,0) 56%);
    background-size: 100% 100%, 100%, 100%;
    background-position: -1em -0.0em, -.95em 0.1em;
    transform: translateX(0);
    -webkit-tap-highlight-color: rgba(0,0,0,0);
  }
  .full-example input[type="checkbox"]:checked {
    background-color: rgba(0,255,0, .5);
    background-position: 0.95em -0.0em, 1em 0.1em;
  }
  .full-example input[type="checkbox"]:checked:before {
    display: none;
  }
</style>

Without any extra elements, or labels, or anything else. Just
pure #CSS and a single #HTML element.

__NOTE__: The point of this article is to show the basic principle, you might
wanna use an [autoprefixer](https://github.com/postcss/autoprefixer) in order
to make those snippets work properly in a real life situation.

## The Starting Point

The magic sauce to the whole thing is really the `appearance` css
property, by assigning it to `none` you can make a checkbox element
to look like a plain DIV. For example:

```css
input[type="checkbox"] {
  appearance: none;
  width:      6em;
  height:     4em;
  border:    .15em solid rgba(0,0,0,.1);
}
input[type="checkbox"]:checked {
  background: green;
}
```

Will make the browser to render this (click it!):

<p class="step-1">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .step-1 input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
  }
  .step-1 input[type="checkbox"]:checked {
    background: green;
  }
  .step-1 input[type="checkbox"]:checked:before {
    display: none;
  }
</style>

Lets also add some `border-radius` and animations to make it look
nicer:

```css
input[type="checkbox"] {
  appearance: none;
  width:      6em;
  height:     4em;
  border:    .15em solid rgba(0,0,0,.1);
  border-radius: 2em;
  transition: background .2s linear;
}
input[type="checkbox"]:checked {
  background-color: rgba(0,255,0, .5);
}
```

<p class="step-2">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .step-2 input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
    border-radius: 2em;
    transition: background .2s linear;
  }
  .step-2 input[type="checkbox"]:checked {
    background-color: rgba(0,255,0, .5);
  }
  .step-2 input[type="checkbox"]:checked:before {
    display: none;
  }
</style>

## The Knobs

Now, lets add the round knobs. In a situation like this, the
first instinct people usually have is to use a `:before` element
and make it round as a normal div. But, the problem is that
browsers won't animate position changes on those auto-generated
elements. And the end experience won't be that slick and awe inducing
as anticipated.

To combat the problem, we will use the `background-image` property
in conjunction with a `radial-gradient` image. The big difference
in this case is that, when done right `transition: background`
_will animate_ the changes in the `:checked` state! Lets see how
it works in principle:

```css
input[type="checkbox"] {
  ...
  background-repeat: no-repeat;
  background-image:
    radial-gradient(circle, yellow 50%, transparent 54%);
  background-size: 100% 100%;
  background-position: -1em 0;
}
input[type="checkbox"]:checked {
  ...
  background-position: 1em;
}
```

<p class="step-3">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .step-3 input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
    border-radius: 2em;
    transition: background .2s linear;
    background-repeat: no-repeat;
    background-image:
      radial-gradient(circle, yellow 50%, transparent 54%);
    background-size: 100% 100%;
    background-position: -1em 0;
  }
  .step-3 input[type="checkbox"]:checked {
    background-color: rgba(0,255,0, .5);
    background-position: 1em;
  }
  .step-3 input[type="checkbox"]:checked:before {
    display: none;
  }
</style>

Try to click them, you should see how they move nice and slow from
end to end to flip the state. And also, notice how the background
color also changes progressively.

Now, lets make it a bit more complicated. Lets add the knob shadow,
so it would look like the real deal. To do that we will basically
create two `radial-gradient` circles, one under another. It's pretty
much the same thing, just a bit more complex:

```css
input[type="checkbox"] {
  ...
  background-image:
    radial-gradient(circle, white 50%, transparent 54%),
    radial-gradient(circle, grey 54%, transparent 56%);
  background-size: 100% 100%, 100% 100%;
  background-position: -1em 0, -0.95em 0.1em;
}
input[type="checkbox"]:checked {
  ...
  background-position: 1em 0, 0.95em 0.1em;
}
```

<p class="step-4">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .step-4 input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
    border-radius: 2em;
    transition: background .2s linear;
    background-repeat: no-repeat;
    background-image:
      radial-gradient(circle, white 50%, transparent 54%),
      radial-gradient(circle, grey 54%, transparent 56%);
    background-size: 100% 100%, 100% 100%;
    background-position: -1em 0, -0.95em 0.1em;
  }
  .step-4 input[type="checkbox"]:checked {
    background-color: rgba(0,255,0, .5);
    background-position: 0.95em -0.0em, 1em 0.1em;
  }
  .step-4 input[type="checkbox"]:checked:before {
    display: none;
  }
</style>

## The Black Voodoo

The example above looks kinda good, but we can improve on that with
some final touches. Firstly, you might notice that the edges of the
circles look a bit pixelated. The problem there is that most browsers
are not so good at calculating gradients from named colors like
`white` to `transparent`. You can largely help the case by specifying
the colors in `rgba` values, like so

```css
input[type="checkbox"] {
  background-image:
    radial-gradient(circle, rgba(255,255,255,1) 50%, rgba(255,255,255,0) 54%),
    radial-gradient(circle, rgba(0,0,0,.3) 54%, rgba(0,0,0,0) 56%);
}
```

<p class="step-5">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .step-5 input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
    border-radius: 2em;
    transition: background .2s linear;
    background-repeat: no-repeat;
    background-image:
      radial-gradient(circle, rgba(255,255,255,1) 50%, rgba(255,255,255,0) 54%),
      radial-gradient(circle, rgba(0,0,0,.3) 54%, rgba(0,0,0,0) 56%);
    background-size: 100% 100%, 100% 100%;
    background-position: -1em 0, -0.95em 0.1em;
  }
  .step-5 input[type="checkbox"]:checked {
    background-color: rgba(0,255,0, .5);
    background-position: 0.95em -0.0em, 1em 0.1em;
  }
  .step-5 input[type="checkbox"]:checked:before {
    display: none;
  }
</style>

And for the final touch add this to your css

```
input[type="checkbox"] {
  -webkit-tap-highlight-color: rgba(0,0,0,0);
}
```

<p class="step-6">
  <input type="checkbox" />
  <input type="checkbox" checked="checked" />
</p>

<style class="text/css">
  .step-6 input[type="checkbox"] {
    appearance: none;
    outline: none;
    width:  6em;
    height: 4em;
    border: .15em solid rgba(0,0,0,.1);
    border-radius: 2em;
    transition: background .2s linear;
    background-repeat: no-repeat;
    background-image:
      radial-gradient(circle, rgba(255,255,255,1) 50%, rgba(255,255,255,0) 54%),
      radial-gradient(circle, rgba(0,0,0,.3) 54%, rgba(0,0,0,0) 56%);
    background-size: 100% 100%, 100% 100%;
    background-position: -1em 0, -0.95em 0.1em;
    -webkit-tap-highlight-color: rgba(0,0,0,0);
  }
  .step-6 input[type="checkbox"]:checked {
    background-color: rgba(0,255,0, .5);
    background-position: 0.95em -0.0em, 1em 0.1em;
  }
  .step-6 input[type="checkbox"]:checked:before {
    display: none;
  }
</style>


This will remove a weir flickering on mobile devices when a user
taps the switch box. Normally a browser will try to highlight the
tap, this hack will disable it and the user will see a smooth,
uninterrupted css transition.

## The Conclusion

Well, here you go. We live in such blessed times for the front
end development. With `appareance:none`, multiple backgrounds and
a bit of imagination, we can redraw pretty much any built in #html
element into a thing of beauty and keep the markup pristine!





rocket_shed 2015-07-24-how-i-blog-now.md

# How I Blog Now

I'm open-sourcing the guts of [nikolay.rocks](https://github.com/MadRabbit/nikolay.rocks)
today. And, as it is quite a bit unconventional way to blog, I thought
I should write about it a bit.

## Prehistory

I've been blogging for a while now. I cannot say I'm particularly good
at it ‚Äî it's more of an habit to me ‚Äî but it's been roughly 15 years
since I had put my first post online. So, you can imagine that I switched
quite a few platforms and engines on my way here.

I hacked together a bunch of PHP pages, I used livejournal, blogspot,
wordpress, jekyll and a few others. I also built my own engine in rails
at some point. But, although there are good parts to each one of them,
I wasn't quite satisfied with any of them.

## What Is My Problem?

Well, it's more of _problems_ than _a problem_. Firstly, I'm a programmer.
Like many others, I have a certain preferred way to deal with stuff I write;
meaning plain text files and a trusty code editor. I spend 10-12 hours every
day writing code and documentation, and I have spent quite a bit of time
optimizing the process for a fast delivery. This is an environment where
i'm most focused.

But then, most engines are web based. And when I'm stuck in a small tiny
TEXTAREA element in a flimsy browser environment, that just distracts me.
There is no code highlighting for the snippets, all fonts are usually wrong.
And being a web-developer, you just don't trust this stuff that much. I
always have at the back of my mind this idea that the web page can reload
or something, and I'm going to loose my stuff. You also can't just drop
your writing halfway through and switch to something else. There is a
session in the browser, and you need to save the draft somewhere. And things
like that. There are solutions to all the problems, but it's just distracting
to me, alot.

The second problem is that I'm a programmer. I build awesome web-stuff
for living, and I can push the envelope quite hard. So, when I use a third party
engine, there are always some substandard things in them. I understand they
don't make the product for me personally, and they need to run a business.
On the other hand, I know I could fix most of the issues easily. But, I don't
have access to the engine guts. All those unresolved urges distract me from
the actual writing.

The third problem, well you guessed it! I'm a programmer. I like to look
back sometimes at my code from a year or two behind and see at my progress.
And I like to do the same thing with my writings. Writing is some sort of a
snapshot of me myself in a certain period of time. I like to look back and
see how well (or badly) I'm progressing as a human being over the time.
But that means I need to keep my data gnostic from platforms and engines.

## The Dream

So, I thought about all those issues for a while and I compiled this list
of important things for a dream engine I'd love to have, and I ended up
with something like this.

1. All content should be 100% in plain text files, so I could work with
   them as I work with my code
2. The presentation layer should be as shallow as possible and don't
   impose rules on the writing format
3. The engine should be gnostic to any platform and easily migrateable
   without a loss of data or functionality
4. The system should be distributed and let me keep my stuff on github
   along with my code
5. The content should be accessible even in case of a catastrophic failure
   in the engine/delivery system.

## The Engine

After a bit of tinkering with different ideas, I decided to build the
entire thing in #javascript and front-end. So, the end result looks
kind of like that. I have the following structure of flat static files
in the project

```
images/
  .....
pages/
  ....
index.html
application.css
application.js
atom.xml
```

All posts and images live in the `pages/` and `images/` folders as simple
static files (markdown, jpeg, etc). Then, as I work on the texts, a small
[gulp](http://gulpjs.com) script reads through all the files and builds
me a small index of the posts in the `atom.xml` file.

Then the `application.js` file has a simple [ReactJS](http://reactjs.com/)
app and it builds the entire UI you see at the moment. All together it works
kind of like that

```
Browser
  <- index.html
    <- application.css
    <- application.js
      <- atom.xml
      <- **/this-page.md

      -> UI
```

There is a small #nodejs server that basically does mode-rewrite and sends
`index.html` file for any HTTP request. Then the file loads up the `application.js`
which loads up the pages index in `atom.xml`. Then, from the index the
application finds the location of the article `*.md` file, loads it up via
an #ajax call and finally builds the entire UI ‚Äî along with the Markdown
transformations and all the post processing ‚Äî directly in a reader's browser.

## Pros & Cons

The biggest win for me in this arrangement is a complete lack of infrastructure
in this engine. There is no database, no server side framework, no dependence
to any server side programming language or anything like that. It can run
on any web server that has an option for some sort of a mod-rewrite that will
serve `index.html` to all HTTP requests. In case of `nikolay.rocks` I just
wrote a simple NodeJS + Connect web server and pushed it to Heroku. But, I can
migrate the entire site easily to any host, no matter which tech will be in
fashion tomorrow.

The second biggest win is the fact that the front end app and the content,
they live next to each other. This way I can work on my content and engine
improvements in the same workflow, and then deliver them with the same
delivery mechanism. As the result I fix small UI/UX issues really quickly,
and I can deliver any new wacky ideas fast as well. There is no extra friction
for server side deployment.

And then it's all just text. I work with all the content and code in a normal
programming environment, I can use git branches to stash some work and run
experiments ‚Äî both content and UI/UX wise. I can share all my work on
GitHub, which will serve as a backup for all my data, as well as an opportunity
for people to contribute. Anyone can submit a pull request to my work, or
create a feature request in [GitHub issues](https://github.com/MadRabbit/nikolay.rocks/issues).

I think I should write some cons here as well. This way of blogging might
not be everyone's cup of tea, for sure. The workflow is programmer oriented.
And the entire thing requires javascript enabled browser to run. But, to
me personally those issues are quite minor. The overwhelming majority of
users have javascript enabled. Even google bots analyze and index the
content perfectly. So, overall  I'm pretty happy with this arrangement at
the moment. And so can you!

Either way, if you are curious and maybe want to build something like that of
your own, or maybe you want to participate in the new era of collaborative
#blogging (totally made up); please proceed to [this github repo](https://github.com/MadRabbit/nikolay.rocks)!
