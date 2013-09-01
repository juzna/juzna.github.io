---
layout: post
title: PHP Enhancer - Enhance PHP syntax now
original_link: https://gist.github.com/3441603
---


# PHP Enhancer

*Enhance PHP syntax now.*


## Motivation
Is there something you don't like about PHP's syntax? Would you like to upgrade it a little bit, if it wouldn't be that difficult? PHP itself is written in C, so if you wanna change something, you'd have to learn C, right?

"No, thanks. I don't wanna learn C and spend long nights debugging memory leaks etc. I wanna just try something out and then probably discard it anyway. Just play around, experiment..."

What if it would be possible to write a *syntax enhancer* in pure php? That would make things much easier and many experiments possible. No need to learn C and figure out how it all works on low level. Just PHP which you already know.

Actually, this is possible now, and very easily.


## How it works
PHP uses technique called *autoloading* to locate a class on disk when it is being used for the first time, and loads the file for you automagically. An *autoloader* does essentialy two things: 1/ find file, 2/ load it.

Since it is possible to define custom *autoloaders*, we can add one more step in between:
1/ find file
2/ do some magic on it
3/ load it.


The magic can be anything you like. You can for example replace all occurences of `world` by `dummy`:
```php
class DummyEhnancer {
    function enhance($code) {
        return str_replace('world', 'dummy', $code);
    }
}
```

Then such a normal class can make really funny stuff.
```php
class Greeter {
    function greet() {
        echo "Hello, world!\n";
    }
}
-----
$greeter = new Greeter;
$greeter->greet(); // Hello, dummy!
```


## Usage
As I said, you must register a *custom autoloader*. That's pretty simple, look at this [example](https://github.com/juzna/php-enhancer/blob/master/examples/00-dummy/test.php). Then you use everything as expected - look at the last two lines in the example.

If you'd like to start a *Nette* project, you can use [sandbox](https://github.com/juzna/php-enhancer/tree/sandbox) with included enhancer. Just install the dependecies by `composer install`.


## Real enhancers
Ok, such a dummy ehnancer is not very useful. What else is out there?

We're experimenting at the moment and it's good fun. If you don't like curly brackets and semicolons, you can try *typing PHP with elegance* with [ladyphp](https://github.com/unu/ladyphp). Example is on [here](https://github.com/juzna/php-enhancer/tree/master/examples/05-ladyphp) and in [ladyphp sandbox](https://github.com/juzna/php-enhancer/blob/sandbox-ladyphp/app/presenters/HomepagePresenter.php).

You may like a shorter way to [define getters and setters](https://github.com/juzna/php-enhancer/tree/master/examples/01-getters), but you probably won't. That a good experiment, which shows the wrong way. We now know we shall not take it.

It is possible to override *new* operator and get a *helper function* called everytime a new object is being created. You can use it for debugging purposes, or switch between different class implementations on runtime. Anything you wish, get inspired by the [example](https://github.com/juzna/php-enhancer/tree/master/examples/01-getters).


Want more? Try something yourself. Or wait for the next blogpost ;) We're preparing something.


## Special cases
This *php-ehnancer* can enhance only classes loaded by *autoloading* mechanism and cannot do anything with those files
loaded via *require*/*include* in PHP. That's actually not a problem, because most projects use autoloading for 99% of the classes. And you should use *autoloading* as well.

Anyway, should this be a problem, you can hook deeper into php's core. It requires an [extension](https://github.com/juzna/php-enhancer/tree/php-extension) to be compiled, which gives you control over *require*-ing and *include*-ing files. But you probably shouldn't need it anyway.


## Summary
Start experimenting today and have fun :) Let us know if you like it, or if you don't. We love discussions.