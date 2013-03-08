Building A Relationship Between CSS & JavaScript

jQuery, Prototype, Node.js, Backbone.js, Mustache and thousands of JavaScript
microlibraries all combine into a single undeniable fact: **JavaScript is
popular**. It’s so popular, in fact, that we often find ourselves using it in
places where another solution might be better in the long run.

Even though we keep JavaScript, CSS and HTML in different files, the concepts
behind progressive enhancement are getting all knotted up with every jQuery
plugin we use and with every weird technique that crops up. Because JavaScript
is so powerful, there are a lot of overlaps in capability between JavaScript and
HTML (building document structure) and JavaScript and CSS (injecting style
information). I’m not here to pick on any JavaScript library, bootstrap or
boilerplate; I’m just here to offer a little perspective as to where we are and
how we can realign our goals.

## Keeping CSS Out Of Your JavaScript

CSS can hook into HTML with a variety of different selectors; this isn’t
anything new. By using IDs, classes or any attribute you can think of (even
custom attributes), you have easy access to style an element. You can also do
this with a slew of JavaScript methods, and honestly, it’s the same basic
process with a different syntax (one of my JavaScript ah-ha moments). Being able
to natively access HTML from JavaScript and from CSS is one of the reasons
progressive enhancement has been such a successful development model. It allows
a point of reference to guide us and to serve as a reminder as we develop a
project, so we don’t “[cross the streams][1]”.

But, as you move forward with JavaScript and build applications with highly
interactive elements, it gets harder to not only keep HTML out of your
JavaScript, but also to catch yourself before injecting style information into a
document. Of course, the case for not injecting style with JavaScript certainly
isn’t a binary one (yes/no, true/false, 0/1); there are plenty of cases where
you might need to apply styles progressively, for example, in a drag and drop
interface where positioning information needs to be constantly updated based on
cursor (or finger) position.

But generally speaking, **you can safely house all the style information you 
need within CSS** and reference styles as reusable classes. This is a much more
flexible model than sprinkling CSS throughout a JavaScript file, and it very
closely compares to the model of adding style information into your HTML. We
follow this model when it’s only HTML and CSS, but for some reason it has a
tendency to fall apart once JavaScript gets added into the mix. It’s certainly
something we need to keep an eye on.

A lot of front-end developers take real pride in having clean HTML. It’s easy to
work with, and to certain super-geeks it can even be artful. It’s great to have
clean, static HTML, but what good is that if your generated HTML is riddled with
injected style and non-semantic markup? By “generated HTML,” I’m referencing how
the HTML looks after it’s been consumed and barfed back up after being passed
around all those plugins and extra JavaScript. If step one to having clean HTML
and separated progressive enhancement layers is to not use a `style` attribute,
I’d have to say that step two is to avoid writing JavaScript that injects a
`style` attribute for you. 

## Cleaning Up Your HTML

We can probably all agree that blindly using a technology is a terrible idea,
and I think we’re at a point with jQuery where we are, indeed, blindly using a
lot of the features without fully understanding what’s going on under the hood.
The example I lean on pretty heavily for keeping CSS out of my JavaScript is the
behavior of jQuery’s `hide()` method. Based on the principles of progressive
enhancement, you wouldn’t code something with inline CSS like this:

    <div class="content-area" style="display:none;"></div>

We don’t do that because a screen reader won’t pick up an element if the style
is set to `display:none`, and it also muddies up the HTML with unnecessary
presentational information. When you use a jQuery method like `hide()`, that’s
exactly what it does: it will set a `style` attribute on the target area and add
a display property of `none`. It’s **very easy to implement, but not very good
for accessibility**. It also violates the principles of progressive enhancement
when you inject style into the document like that (we’re all sorts of messed up,
huh?). It’s not uncommon for this method to be used within a tabbing interface
to hide content. The result is that the content is nonexistent to a screen
reader. Once we realize that adding style from JavaScript isn’t ideal in most
cases, we can move it into the CSS and reference it as a class:

**CSS**

    .hide {
       display: none;
    }

**jQuery**

    $('.content-area').addClass('hide');

We still have to address the accessibility problem of hiding content with
`display:none`, but since we’re not using a built-in jQuery method anymore, we
can control exactly how content gets hidden (whichever accessible method you
prefer is probably fine). For example we could do something like:

**CSS**

    .hide {
       position: absolute;
       top: -9999px;
       left: -9999px;
    }

    .remove {
       display: none;
    }

In the above example, you can see that even though both classes result in
content being removed from view, they function very differently from an
accessibility standpoint. Looking at the code like this makes it clear that we
really are dealing with style information that belongs in a CSS file. Using
utility classes in this way can not only help your JavaScript slim down, but
also have double usage in an Object Oriented CSS (OOCSS) development model. This
is truly **a way to not repeat yourself** (Don’t Repeat Yourself, or DRY) within
CSS, but also across a whole project, creating a more holistic approach to
front-end development. Personally, I see a lot of benefit in controlling your
behaviors this way, but some people have also called me a control-freak in the
past. 

## Web and Team Environments

This is a way we can start opening up lines of communication between CSS and
JavaScript and lean on the strengths of each language without overdoing it.
Creating a **developmental balance on the front end is very important**, because
the environment is so fragile and we can’t control it like we can on the back
end with a server. If a user’s browser is old and slow, most of the time you
can’t sit down and upgrade it (aside: I do have my grandmother using Chrome);
all you can do is embrace the environmental chaos, build for the best and plan
for the worst.

Some people have argued with me in the past that this style of development,
where you’re referencing CSS classes in JavaScript, doesn’t work well in team
development environments because the CSS is usually built by the time you’re
diving into the JavaScript, which can cause these classes to get lost in the mix
and create a lot of inconsistency in the code (the opposite of DRY). To those
people I say: poke your head over the cube wall, open AIM, GTalk or Skype, and
communicate to the rest of the team that these classes exist specifically to be
used with JavaScript. I know the concept of developers communicating outside of
GIT commit messages seems like madness, but it’ll be okay, I promise. 

## Using Behavioral CSS With JavaScript Fallbacks

Using these CSS objects as hooks for JavaScript can go far beyond simple hiding
and showing of content into an area of behavioral CSS, transitions, animations
and transforms that are often done with JavaScript animations. With that in
mind, lets take a look at a common interaction model of fading out a `div` on
click, and see how it would be set up with this development model, while
providing the proper fallbacks for browsers that might not support the CSS
transition we’re going to use.

For this example we’ll be using:

* [jQuery][2]
* [Modernizr][3]

First, let’s set up our `body` element:

    <body>
        <button type="button">Run Transition</button>
        <div id="cube"></div><!--/#cube-->
    </body>

From there we’ll need to set up the CSS:

    #cube {
       height: 200px;
       width: 200px;
       background: orange;
       -webkit-transition: opacity linear .5s;
          -moz-transition: opacity linear .5s;
            -o-transition: opacity linear .5s;
               transition: opacity linear .5s;
    }

    .fade-out {
       opacity: 0;
    }

Before we add on the JavaScript layer, **lets take a moment and talk about the
flow of what’s going to happen**:

1. Use Modernizr to check for CSS Transition support
2. If Yes
2.1. Set up a click event on the button to add a “fade-out” class to `#cube`
2.2. Add another event listener to catch when the transition is finished so we 
can time the execution of a function that will remove `#cube` from the DOM.
3. If No
3.1. Set up a click even on the button to use jQuery’s `animate()` method to 
manually fade `#cube` out.
3.2. Execute a callback function to remove `#cube` from the DOM.

This process will introduce a new event called `transitionend`, which will
execute at the end of a CSS transition. It’s amazing, FYI. There is also a
companion event called `animationend`, which will execute at the end of a CSS
animation for more complex interactions.

First thing we need to do is set up our variables in the JavaScript:

    (function () {

       // set up your variables
       var elem = document.getElementById('cube'),
           button = document.getElementById('do-it'),
           transitionTimingFunction = 'linear',
           transitionDuration = 500,
           transitionend;

       // set up the syntax of the transitionend event with proper vendor prefixes
       if ($.browser.webkit) {
           transitionend = 'webkitTransitionEnd'; // safari & chrome
       } else if ($.browser.mozilla) {
           transitionend = 'transitionend'; // firefox
       } else if ($.browser.opera) {
           transitionend = 'oTransitionEnd'; // opera
       } else {
           transitionend = 'transitionend'; // best guess at the default?
       }

       //... rest of the code goes here.

    })(); // end wrapping function

You might notice that our new `transitionend` event needs a vendor prefix; we’re
doing a little browser detection to take care of that. Normally you might detect
for the vendor prefix and add it onto the event name, but in this instance the
cases for the syntaxes are a little different, so we need to get the whole name
of the event for each prefix.

**In the next step we’ll use Modernizr to detect support**, and add our event
listeners to each case (all of this stuff gets added inside the wrapping
function):

    // detect for css transition support with Modernizr
    if(Modernizr.csstransitions) {

        // add our class on click
        $(button).on('click', function () {
           $(elem).addClass('fade-out');
        });
    
        // simulate a callback function with an event listener
        elem.addEventListener(transitionend, function () {
           theCallbackFunction(elem);
        }, false);
       
    } else {

       // set up a normal click/animate listener for unsupported browsers
       $(button).on('click', function () {

           $(elem).animate({
               'opacity' : '0'
           }, transitionDuration, transitionTimingFunction, function () {
               theCallbackFunction(elem);
           });

       }); // end click event

    } // end support check

**Finally, we need to define a shared function between the two actions** (DRY)
which executes after the transition (or animation) is complete. For the sake of
this demonstration we can just call it `theCallbackFunction()` (even though it’s
not technically a callback function). It will remove an element from the DOM and
spit out a message in the console letting us know that it worked.

    // define your callback function, what happens after the transition/animation
    function theCallbackFunction (elem) {

       'use strict';

       // remove the element from the DOM
       $(elem).remove();

       // log out that the transition is done
       console.log('the transition is complete');

    }

In the browser, this should work the same way in IE 7 (on the low end) as it
does in mobile Safari or Chrome for Mobile (on the high end). The only
difference is under the hood; the experience never changes for the user. This is
a way you can use cutting-edge techniques without sacrificing the degraded user
experience. It also keeps CSS out of your JavaScript, which was really our goal
the whole time. 

## The Moral Of The Story

You might be asking yourself why we should even bother going through all this
work. We wrote about 60 lines of JavaScript to accomplish the same design
aesthetic that could be created with eight lines of jQuery. Well, no one ever
said that keeping clean code and sticking to progressive enhancement was the
easiest thing to do. In fact, it’s a lot easier to ignore it entirely. But as
responsible developers, it’s our duty to build applications in a way that is
accessible and easily scales to the future. If you want to go that extra mile
and create a seamless user experience as I do, then it’s well worth the extra
time it takes to dot all the i’s and cross all the t’s in a project to create an
overall experience that will gracefully degrade and progressively enhance.

Using this model also lets us lean heavily on CSS for its strengths, like
responsive design and using breakpoints to redefine your interaction at the
various screen sizes. It also helps if you’re specifically targeting a device
with a constrained bandwidth, because, as we all know, CSS is much lighter than
JavaScript in both download and execution time. **Being able to offload some of
the weight JavaScript carries onto CSS is a great benefit**.

In production, we are currently using CSS animations and transitions for micro
interactions like hover effects and maybe a spinning graphic or a pulsating
knot. We’ve come to a point where CSS is a pretty powerful language that
performs very well in the browser and it’s okay to use it more heavily for those
macro interactions that are typically built using JavaScript. If you’re looking
for a lightweight and consistent experience that’s relatively easy to maintain
while allowing you to use the latest and greatest browser features — it’s
probably time to start mending fences and build strength back into the
relationship between CSS and JavaScript. As a great man once said, “The key to
writing great JavaScript is knowing when to use CSS instead.” (It was me… I said
that.)

[1]: http://www.youtube.com/watch?v=jyaLZHiJJnE
[2]: http://jquery.com/
[3]: http://modernizr.com/