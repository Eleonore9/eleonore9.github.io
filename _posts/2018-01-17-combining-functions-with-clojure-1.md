---
layout: default
title:  "Combining functions with Clojure (1)"
date:   2018-01-17
category: programming languages
tags: [clojure, coding]
---


# Combining functions with Clojure (1)

___
**DISCLAIMER**
This post doesn't contain any amazing coding ninja trick!
It's basically me taking the time to try out two built-in Clojure functions I'm uncomfortable with instead of doing it the way I'm comfortable with.

___


This first technical post is based on a gist I wrote (two years ago apparently) to keep notes while writing code. As you can deduce from the title this is about combining functions (here, in order to transform data). It should be a straightforward example to follow even if you don't know Clojure (if not, please give me feedback [0]) and will at one point be followed by another post on the same subject using a different built-in Clojure function.

## What's the "problem" at hand?

At the time I was simply trying to transform a collection of *keywords* [1] `[:value :location :unit]` into a collection of capitalised strings `["Value" "Location" "Unit"]` using Clojure (note: here I don’t care if the output is a list or a vector [2]).

My idea is to [*map*](https://clojuredocs.org/clojure.core/map) over the input collection and apply a combination of functions that would perform the transformation. I know the functions I need for this basic transformation are [*name*](https://clojuredocs.org/clojure.core/name) and [*capitalize*](https://clojuredocs.org/clojure.string/capitalize) [3].

My go-to function would be a [threading macro](https://clojure.org/guides/threading_macros):
{% highlight clojure %}
(map #(-> %
          name
          str/capitalize)
    [:value :location :unit])
{% endhighlight %}[4]

But I wanted to try out a “more elaborate” way of doing it while I had an easy example at hand. And by more elaborate, I mean using the [*juxt*](https://clojuredocs.org/clojure.core/juxt) or [*comp*](https://clojuredocs.org/clojure.core/comp) functions. Both those functions are expected to take in functions and return a function that in a way combines the functions pasted in as arguments [5].

## Meet *juxt*
*Juxt* stands for juxtapose.
You can have a look at the source code [here](https://github.com/clojure/clojure/blob/clojure-1.9.0-alpha14/src/clj/clojure/core.clj#L2549). Let see what the doc says:
> Takes a set of functions and returns a fn that is the juxtaposition of those fns.
> The returned fn takes a variable number of args, and returns a vector containing the result of applying each fn to the args (left-to-right).

It looks like I can use *juxt* to juxtapose the functions *name* and *capitalize* and I'll get the output collection as a vector (which is fine with me).

Here's how I tried it:
{% highlight clojure %}
(map #((juxt name str/capitalize) %)
       [:value :location :unit])
{% endhighlight %}
And the result was: `(["value" ":value"] ["location" ":location"] ["unit" ":unit"])`. So obviously not what I want. And if I inverse my argument functions:
{% highlight clojure %}
(map #((juxt str/capitalize name) %)
       [:value :location :unit])
{% endhighlight %}
This gives me `([":value" "value"] [":location" "location"] [":unit" "unit"])`, so still not what I'm looking for!

I don't know about you, but sometimes I want so much for a function to do what I need that I misread the doc. Here *juxt* as indeed applied *name* and *capitalize* on each element of my collection and returned it in a vector: `[":location" "location"]`. It's not obvious for *capitalize* and I'd have expected an exception when a function intended for strings is used on a keyword. But we can reproduce it separately:
{% highlight clojure %}
> (clojure.string/capitalize :my-keyword)
":my-keyword"
{% endhighlight %}
*Capitalize* when used on a keyword somehow returns a string containing a copy of the keyword.

Now let's try *comp*!

## Meet *comp*
*Comp* stands for compose.
You can have a look at the source code [here](https://github.com/clojure/clojure/blob/clojure-1.9.0-alpha14/src/clj/clojure/core.clj#L2530). Let see what the doc says:
> Takes a set of functions and returns a fn that is the composition of those fns.
> The returned fn takes a variable number of args, applies the rightmost of fns to the args, the next fn (right-to-left) to the result, etc.
This time it looks more like what I need; creating a function from *name* and *capitalize*, then apply it on elements of my collection. I just need to remember the functions are applied right to left:
{% highlight clojure %}
(map #((comp str/capitalize name) %)
       [:value :location :unit])
{% endhighlight %}
And this returns `("Value" "Location" "Unit")` just as I wanted! W00T :D

## What did I learn?
* *clojure.string/capitalize* can be used on keywords, who knew? (Not me, obviously.)
* Be more careful when reading the documentation!
* Although there's not an obvious gain here, I should now remember what *juxt* and *comp* do. And if I don't I'll come back to this post :)

______
Notes:

\[0] I haven't implemented comments (yet), but for feedback you can @ me or DM me on Twitter at [@EleonoreMayola](https://twitter.com/eleonoremayola)!

\[1] [Keywords](https://clojure.org/reference/data_structures#Keywords) are a data type in Clojure often used as index in the key/value data structure called a map.

[2] More on Clojure data types, see [here](https://clojure.org/reference/data_structures).

\[3] The *capitalize* function is defined in the clojure.string namespace, so if you try it at home you need to use `clojure.string/capitalize` or set `str` as `clojure.string/capitalize`.

[4] If you're not used to this syntax `#(%)`, know that `#()` let's you define an anonymous function like `fn(x)` and `%` in that case is a placeholder for the function's argument. Also `->` is the threading function in Clojure called "thread first" as it adds `%` as the first argument of the function *name* and the output of calling *name* on our argument will be the first argument of the *capitilize function. Although here it's not a big deal as those functions only take one argument.

\[5] Well, we're using a functional programming language here after all so there was bound to be a little bit of function inception ;)
