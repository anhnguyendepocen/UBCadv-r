# Big picture notes
`r format(Sys.time(), '%d %B, %Y')`  



*Here we record questions and comments that linger even after our group discussions.*

## [Functions][func]

#### Returning nothing and/or invisibly

Later, e.g. when reading about [Environments][env], we had some discussion of how and why to return nothing and/or how to return invisibly. In the same way Wickham describes his own policies re: using `return()` and handling default values for function arguments, it would be interesting to hear his thoughts on what to return and how noisily to do so. That would be good content to add to the subsection on [Return values][ret val].

#### Recursive functions

Also with hindsight from reading [Environments][env], a section on recursive functions in [Functions][func] would be helpful. They are critical to solving the [Environments][env] exercises, but it's hard to learn recursive functions while also manipulating rather abstract and unfamiliar objects -- i.e. environments. All of us who worked those exercises had to practice writing much simpler recursive functions first. The explication of of `where()` in [Recursing over environments][recurse] is extremely educational; something like that would be useful in a more general introduction to recursive functions.

## [Environments][env]

#### Multiple vs unique names

In the [Environment basics][env basics] section, we read that

  * "The objects don’t live in the environment so multiple names can point to the same object."
  * "Every object in an environment has a unique name."
  
This is confusing. In the examples and diagrams, we work with an environment `e` in which the object name `d` is bound to the vector (1, 2, 3). Then we make a new assignment `e$a <- e$d` and the diagram shows `a` and `d` pointing to the same vector (1, 2, 3). This suggests that the same underlying object is accessible under the names `a` and `d`, which is compatible with the first quote above. But then the second quote says that every object has a unique name. Also, we did some experimentation (see below), that makes it clear that any equivalence of `a` and `d` is very coincidental / fragile; there is definitely no long-term structural relationship between what `a` and `d` refer to.


```r
library(pryr)
e <- new.env()
e$d <- 1:3
e$a <- e$d
e$d
```

```
## [1] 1 2 3
```

```r
e$a
```

```
## [1] 1 2 3
```

```r
address(e$d)
```

```
## [1] "0x103cd8aa0"
```

```r
address(e$a)
```

```
## [1] "0x103cd8aa0"
```

At this point, reality follows the diagram. The object bound to `d` and that bound to `a` are identical and are even occupying the same memory. So it seems like there is an object that has two names! How to reconcile with "every object in an environment has a unique name"?

However, as soon as we alter an element of the vector, the link between `a` and `d` is broken.


```r
e$d[2] <- 9
e$d
```

```
## [1] 1 9 3
```

```r
e$a
```

```
## [1] 1 2 3
```

```r
address(e$d)
```

```
## [1] "0x105101ad8"
```

```r
address(e$a)
```

```
## [1] "0x103cd8aa0"
```

In terms of a statement about uniqueness in an environment, it seems like the statement should be more about *names* and less about *objects*. Is this what's meant: "in an environment, any name can be bound to at most one object," i.e. it is impossible for an environment to have two notions of `a`?

#### Names, expressions, symbols, etc.

This will probably get resolved after reading [Expressions][expr], but several exercises have exposed our ignorance of issues around names vs symbols vs expressions:

  * Exercise 3 near the end of [Functions][func]: "Write a function that opens a graphics device, runs the supplied code ...."
  * Exercise 2 in [Recursing over environments][recurse]: "Write your own version of `get()` ....".
  
In these contexts, we've needed to write functions where code, objects, or object names are passed as arguments of functions. There's been lots of trial and error about whether to surround code or a name with quotes, when to put multi-line expressions into curly braces, and how to use `as.name()`, `parse()`, `eval()`.

[func]: http://adv-r.had.co.nz/Functions.html
[ret val]: http://adv-r.had.co.nz/Functions.html#return-values
[env]: http://adv-r.had.co.nz/Environments.html
[recurse]: http://adv-r.had.co.nz/Environments.html#env-recursion
[env basics]: http://adv-r.had.co.nz/Environments.html#env-basics
[expr]: http://adv-r.had.co.nz/Expressions.html
