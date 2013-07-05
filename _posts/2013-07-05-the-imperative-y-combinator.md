---
layout: post
title: "The imperative Y Combinator"
description: ""
category: 
tags: [y combinator, imperative]
---
{% include JB/setup %}


**If you don't know what the Y combinator is, you should read [this artical]("the-y-combinator") first.**

## Derivation
---
Suppose:
  
    Y(f) = g ===> f(g) = g

where `f` is the funcion, `Y` is the combinator, and `g` is the fixed point of `f`.

We've known the true that `f(g) = g`. To get `g`, we just set `g` to `f(g)`. So we try:

    (lambda (f)
		(let ((fixed-point 'dull))
			(set! fixed-point 
				  (f fixed-point))
			fixed-point))
			
However, when we apply the function above to a function `f`, actually we get:

    (f 'dull)
	
which is not what we want.

The problem here is that the `fixed-point` inside the expression `(f fixed-point)` evaluates to `dull`.

knowing that, we can easily modify the code to:

	(lambda (f)
		(let ((fixed-point 'dull))
			(set! fixed-point
				(f (lambda (x) (fixed-point x))))
			fixed-point))
			
We assume that `fixed-point` is a function which takes only one argument. so we have:

    fixed-point = (lambda (x) (fixed-point x))
	
Here, because `(fixed-point x)` is inside the lambda expression and will not evaluate immediately, the next time it will evaluate to `(f (lambda (x) (fixed-point x)))`.

Now, we give it a name `Y!` with `define`,

	(define Y!
		(lambda (f)
			(let ((fixed-point 'dull))
				(set! fixed-point
					(f (lambda (x) (fixed-point x))))
						fixed-point)))
						
and this is the imperative Y combinator.

## Difference between functional and imperative Y combinator
---

The functional Y combinator:

	(define Y
	(lambda (f)
		((lambda (m)
			(f (lambda (x) ((m m) x))))
		(lambda (m)
			(f (lambda (x) ((m m) x)))))))
			
The imperative Y combinator:

	(define Y!
		(lambda (f)
			(let ((fixed-point (lambda (x) 'dull)))
				(set! fixed-point
				(f (lambda (y) (fixed-point y))))
			fixed-point)))
			
Test function 1:

	(define biz1
		(let
		 ((x 0))
          (lambda (f)
            (set! x (add1 x))
				(lambda (a)
					(if (= a x)
						0
						(f a))))))
						
Test function 2:

	(define biz2
		(let
			((x 0))
				(lambda (f)
					(lambda (a)
						(set! x (add1 x))
							(if (= a x)
								0
								(f a))))))
								
If we apply `Y` to `biz1` or `biz2`, or apply `Y!` to `biz2`, there is no problem. We will get the result `0`.

However, if we apply `Y!` to `biz1`， we get an infinite loop!

Let's find out what cause the problem.

When we pass `biz1` to `Y!`, indeed we get:

    (define x 'dull)
	
	(define biz1
		(lambda (a)
			(if (= 0 a)
				0
				(biz1 a))))
				
When we pass `biz2` to `Y!`, we get:

    (define x 'dull)
		
	(define biz2
		(lambda (a)
			(set! x (add1 x))  ;; Here we set the x!
			(if (= a x)
				0
				(biz2 a))))
				
Do you notice the difference? 

    `biz1` never sets the `x`, but `biz2` do.
	
So 

    When we use the imperative Y combinator, we should be very careful!
	
	
