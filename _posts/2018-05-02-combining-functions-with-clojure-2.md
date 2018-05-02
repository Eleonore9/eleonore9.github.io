---
layout: default
title:  "Combining functions with Clojure (2)"
date:   2018-05-02
category: programming languages
tags: [clojure, coding]
---


# Combining functions with Clojure (2)

My first technical post was named [Combining functions with Clojure (1)]({% post_url 2018-01-17-combining-functions-with-clojure-1 %}) which calls for a part two :)

I actually forgot what my initial plan was, because I took so long to draft the part two :-/

In the part one I used Clojure built-in functions *juxt* and *comp* to perform several steps of transformation on a data collection.

The data collection is very simple: `[:value :location :unit]`, and the aim is for it to undergo two steps of transformation (using the built-in functions *name* and *clojure.string/capitalize*) to get the following collection: `["Value" "Location" "Unit"]`.

This time I'm using Clojure built-in function [*partial*](https://clojuredocs.org/clojure.core/partial).

## Meet *partial*

Here's what's the doc says:
> Takes a function f and fewer than the normal arguments to f, and
> returns a fn that takes a variable number of additional args. When
> called, the returned function calls f with args + additional args.

Afaik *partial* is a [higher-order function](https://en.wikipedia.org/wiki/Higher-order_function) as it takes in a function and returns a function. It's quite cool and also useful!

In case you're curious, it's defined using nested [multi-arity](http://clojure-doc.org/articles/language/functions.html#multi-arity-functions).

As you can see below, *partial* always expects a function as an argument. The multi-arity enables it to accept as many arguments as the user wants, but always with a function as its first argument.
Then it returns a function, and multi-arity enables this new function to be called with the arguments given when defining the "partial function" and the arguments given when calling this new function.

{% highlight clojure %}
(defn partial
  ([f] f)
  ([f arg1]
   (fn
     ([] (f arg1))
     ([x] (f arg1 x))
     ([x y] (f arg1 x y))
     ([x y z] (f arg1 x y z))
     ([x y z & args] (apply f arg1 x y z args))))
  ([f arg1 arg2]
   (fn
     ([] (f arg1 arg2))
     ([x] (f arg1 arg2 x))
     ([x y] (f arg1 arg2 x y))
     ([x y z] (f arg1 arg2 x y z))
     ([x y z & args] (apply f arg1 arg2 x y z args))))
  ([f arg1 arg2 arg3]
   (fn
     ([] (f arg1 arg2 arg3))
     ([x] (f arg1 arg2 arg3 x))
     ([x y] (f arg1 arg2 arg3 x y))
     ([x y z] (f arg1 arg2 arg3 x y z))
     ([x y z & args] (apply f arg1 arg2 arg3 x y z args))))
  ([f arg1 arg2 arg3 & more]
	(fn [& args] (apply f arg1 arg2 arg3 (concat more args)))))
{% endhighlight %}
The way *partial* is used is by defining a function without arguments using `def` (as opposed to `defn`) and  then call partial with a build-in Clojure function or an anonymous function:
{% highlight clojure %}
(def ktcs-partial
  (partial #(-> %
                name
                clojure.string/capitalize)))
{% endhighlight %}
Here I expect this new function (`ktcs-partial`) to be called on each element of the collection inside a *map*. For this reason I want it to return a *thread* (Clojure function represented as a [`->`](https://clojuredocs.org/clojure.core/-%3E) like here, or a [`->>`](https://clojuredocs.org/clojure.core/-%3E%3E)) of the two steps of transformation needed.
{% highlight clojure %}
(map ktcs-partial [:value :location :unit])

("Value" "Location" "Unit")
{% endhighlight %}

In this case there's no benefit over using *comp* for example (see blog post [part 1]({% post_url 2018-01-17-combining-functions-with-clojure-1 %})). However when you have several steps of transformations to be executed several times, using *partial* in this way gets useful.

## *partial* combined with *comp*

In the previous post I used the Clojure built-in function *comp* (for "compose") to combine two functions (namely *name* and *capitalize*) into one.

Here I got wondering if I could define a *partial* function that uses *comp* to combine the two steps of transformation I want to perform. I'm just combining Clojure functions for the fun of it!

If I'd need this transformations performed many times and always on a collection of keywords, I'd define this function:
{% highlight clojure %}
(def comp-partial
  (partial (fn [list-kw]
             (map #((comp clojure.string/capitalize name) %)
			 list-kw))))
{% endhighlight %}
And it'd be used like so:
{% highlight clojure %}
(comp-partial [:value :location :unit])
("Value" "Location" "Unit")
{% endhighlight %}
So again, no revolution here. Just trying out functions so I can use them efficiently when needed!

## What did I learn?

* I reviewed the use of Clojure built-in function *partial*, and i think I'll use it more now.
* I had never looked at the source code for *partial* and it's quite cool to see the two levels of multi-arity B-)


**That's it for now! Hopefully I'll (finally) get faster at writing blog posts!**

_____

References:

* Documentation for [*partial*](https://clojuredocs.org/clojure.core/partial)
* Concept of functional programming: [higher order functions](https://en.wikipedia.org/wiki/Higher-order_function)
* Interesting feature of Clojure: [multi-arity](http://clojure-doc.org/articles/language/functions.html#multi-arity-functions)
