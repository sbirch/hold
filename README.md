hold
====

A small Python module for incremental computation.

Have you ever thrown away long-running calculations because of an exception from matplotlib?

This is a convenience wrapper around cPickle which makes it easier to save
and load the results of functions. It preserves the output of the
function in addition to the return value.

Documentation
=============

This module provides only one function, `hold`:

```
hold(func, args=(), kwargs={}, v=1, name=None)
```

`hold` returns the value of `func(*args, **kwargs)`, either by evaluating
`func` or by retrieving the value from cache on disk. If the result is
retreived from cache, it will re-print the output generated by
the function.

*func*: the function to call if no stored result is available.
*args*: the arguments to the function, as a tuple.
*kwargs*: keyword arguments to the function, as a dictionary.
*v*: the version, a number or string. This parameter serves to invalidate the
cache when you change the code. If `v=None` the cache will always be passed (you
might use this for development.)
*name*: a unique ID for the function. If `None`, uses `func.__name__` (though
this ID may collide!).

Some examples:

```
from hold import hold

value = hold(really_long_func)

# Arguments are passed as a tuple, keyword arguments as a dictionary.
value = hold(really_long_func, (n,), {'x': 1})
```

If `really_long_func` has been run with the same arguments, version, and name
before the result will be loaded from disk. Otherwise the function will be run
and the result will be stored.

Rather than rely on magic to determine when your function has changed, hold
relies on you to change the version when you want the function to be
re-computed:

```
from hold import hold

value = hold('someid', really_long_func, v=2)
```

Typically you might use `hold` once the function you plan to cache has been
substantially written. Loading cached data speeds up development of code
which depends on earlier results (like matplotlib commands on the results
of a long-running simulation.)

Older versions remain on disk -- it can be useful to be able to load two seperate
versions of the data. But to delete old cached versions you can delete the files,
which start with `.HELD` in the working directory.



*Caveats:*

* This is backed by cPickle, so only functions with pickleable return values and arguments can be held. In some cases storing and loading a pickle can be slower than the original computation.

* On playback the `stdout` and `stderr` transcripts are written to `stdout`.
