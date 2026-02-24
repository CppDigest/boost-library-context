# #248 - Add <valgrind> option [Open]

> Username: vinniefalco  
> Created at: 2017-10-03 17:34:59 UTC  
> Updated at: 2017-10-31 01:19:01 UTC  
> Url: https://github.com/boostorg/build/issues/248  

Boost.Context adds a `<valgrind>` property. I think this could be built in to Boost.Build itself perhaps, with a well defined macro. This way multiple libraries can take advantage of the property.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-10-19 01:14:42 UTC  
> Url: https://github.com/boostorg/build/issues/248#issuecomment-337771418  

I would VERY much like to see this and would add it to the CI builds for Boost.Format and Boost.Uuid right away.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-30 23:50:56 UTC  
> Url: https://github.com/boostorg/build/issues/248#issuecomment-340618587  

As far as I can see, the only thing that `<valgrind>` does in Boost.Context is to `#define BOOST_USE_VALGRIND`.  
  
Are you sure you want that, and not `<testing.launcher>valgrind` that runs the test under `valgrind`?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-31 00:55:21 UTC  
> Url: https://github.com/boostorg/build/issues/248#issuecomment-340628046  

Running valgrind would be even better but that's a much bigger ask

---

## Comment 4

> Username: pdimov  
> Created at: 2017-10-31 01:19:01 UTC  
> Url: https://github.com/boostorg/build/issues/248#issuecomment-340631420  

`<testing.launcher>valgrind` already exists. http://www.boost.org/build/doc/html/bbv2/builtins/testing.html
