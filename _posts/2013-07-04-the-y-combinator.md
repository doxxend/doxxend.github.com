---
layout: post
title: "The Y Combinator"
description: ""
category: 
tags: ["y combinator"]
---
{% include JB/setup %}

## Introduction
---
This is the derivation of the Y-Combinator from scratch, in scheme. The following derivation uses a different approach from many other articles. While others often begin with some examples, I begin with the essence.  


## The essence of the Y-Combinator
---
The essence of the Y combinator is that it's a [fixed-point](http://en.wikipedia.org/wiki/Fixed_point_(mathematics) combinator, which is a higher-order function that `consumes a function` and `computes a fixed point of it`.

Suppose `f(x) = x`, `x` is the fixed point of `f`.  

We have `x = f(x) = f(f(x)) = f(f(f(x))) = f(...(f(f(f(x)))))`  


## Derivation
---
Again here, that `the Y combinator consumes a function and computes a fixed point of it`. With this in mind, we can begin.

In scheme, to represent a fixed point of a function, We could write:  

    (f x)
	
where `f` is the function, and `x` is the fixed point of it. Obviously, `x` is equal to `(f x)`.

Because `x` is unbound(within a combinator, all of the variables are bound), we do this:

    (lambda (x) (f x))
		
Since `(f x)` is the fixed point of `f`, we call the function above the `fixed point maker`(which takes the fixed point of `f` as an argument and return the fixed point of it(sounds a bit strange)).

However, We haven't got the fixed point of `f`, so we cannot pass it to the `fixed point maker`. We must be smarter.

We have a fixed point maker but the fixed point. Why not change `(f x)` to `(f (m ??))`, where `m` is the fixed point maker and `(m ??)` is the fixed point of `f`.

Then

    (lambda (x) (f x))

becomes

    (lambda (m) (f (m ??)))
	
where `m` is a fixed point maker. `(lambda (m) (f (m ??)))` is also a fixed point maker, which takes a fixed point maker as an argument...

Now, the fixed point maker consumes a fixed point maker, so `(m ??)` becomes `(m m)`. We get:

    (lambda (m) (f (m m)))

which is a fixed point maker. Applying it to itself`(a fixed point maker consumes a fixed point maker to make a fixed point)`, we get:

    ((lambda (m) (f (m m)))
	 (lambda (m) (f (m m))))
	 
which is the fixed point of `f`.  

Now the only unbound variable is `f`, we simply do:

	(lambda (f)
		((lambda (m) (f (m m)))
	     (lambda (m) (f (m m)))))

The above function is the Y combinator. We give it a name `Y` with `define`:

	(define Y
		(lambda (f)
			((lambda (m) (f (m m)))
			 (lambda (m) (f (m m))))))
			 
We've almost finished here. The only problem is that the Y combinator above is of `normal-order`, which doesn't work for strict language. Because in such a language, when we pass `(m m)` to `f` in the expression `(f (m m))`, the evalution of `(m m)` will never terminate.
		 
However, there is a clever hack here. Suppose the fixed point of `f` is a function that takes only one argument.(It doesn't matter how many arguments it takes). Because `(m m)` is the fixed point of `f`, it takes one argument. So,

    (m m) = (lambda (y) ((m m) y))
	
like

    cos = (lambda (x) (cos x))
	
then we get the `applicative-order Y combinator`:

	(define Y
		(lambda (f)
			((lambda (m) (f (lambda (y) ((m m) y))))
			 (lambda (m) (f (lambda (y) ((m m) y)))))))  
			 


##Further reading
---  

* [The Y Combinator (Slight Return) by mvanier](http://mvanier.livejournal.com/2897.html)
* [Y-Combinator(Based on work by Jim Marshall)](http://dangermouse.brynmawr.edu/cs245/ycomb_jim.html)
