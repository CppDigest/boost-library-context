# #122 - Enhancement proposal: verices() and edges() returning boost::iterator_range [Open]

> Username: mkatliar  
> Created at: 2018-10-17 18:08:26 UTC  
> Updated at: 2018-11-23 12:32:54 UTC  
> Url: https://github.com/boostorg/graph/issues/122  

Currently the `vertices()` and `edges()` functions in BGL return an `std::pair` of iterators. To iterate over vertices, a code like following is usually written:  
```c++  
typename graph_traits<Graph>::vertices_size_type b = 0;  
typename graph_traits<Graph>::vertex_iterator i, end;  
for (boost::tie(i, end) = vertices(g); i != end; ++i)  
    do_something(*i);  
```  
  
If `vertices()` and `edges()` returned a [boost::iterator_range](https://www.boost.org/doc/libs/1_55_0/libs/range/doc/html/range/reference/utilities/iterator_range.html), a cleaner code could be written:  
```c++  
for (auto v : vertices(g))  
    do_something(v);  
```

---

## Comment 1

> Username: anadon  
> Created at: 2018-10-18 01:34:23 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-430844877  

It would be a massive improvement, but would also require a fair bit of work since the old way is used all over graph.  I'm working on a replacement that addresses that off of a from scratch code base which will do this.  It has been on my radar.

---

## Comment 2

> Username: mkatliar  
> Created at: 2018-10-18 13:08:48 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-431001709  

@anadon I could work on that change if it is approved and will be integrated in the library.

---

## Comment 3

> Username: anadon  
> Created at: 2018-10-18 14:41:50 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-431035831  

If you don't break the macros and existing code, I'll buy you a drink.  It would be a welcomed improvement.

---

## Comment 4

> Username: mkatliar  
> Created at: 2018-10-19 11:12:35 UTC  
> Updated at: 2018-10-19 11:15:01 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-431329064  

@anadon my concern is that it will break user's code relying on the old semantics. To keep it compatible, we probably need new names for these functions.  
  
Or, those functions should return an object which has both old `std::pair` and the new `boost::iterator_range` semantics. Then the existing code relying on the old semantics can remain unchanged.  
  
What do you think?

---

## Comment 5

> Username: anadon  
> Created at: 2018-10-19 12:53:39 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-431353055  

There are a few macros which would have to be preserved as well.  One of the reasons why it is a big task.

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2018-10-21 06:42:31 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-431643376  

`std::pair` of iterators satisfies the Boost Range concept: https://www.boost.org/doc/libs/master/libs/range/doc/html/range/introduction.html  
  
So whilst it is true that you cannot use it in a C++11 range-for loop, you can use it with any Boost.Range algorithm.  
E.g.:  
  
```C++  
#include <boost/range.hpp>  
#include <boost/range/for_each.hpp>  
  
// ...  
  
boost::for_each(vertices(g), do_something);  
```  
Also, adapting `std::pair` to a standard readable range is not as difficult as you think, you can just wrap it in a call to `boost::make_iterator_range` like so:  
  
```C++  
for (auto const &vertex : boost::make_iterator_range(vertices(g)))  
  do_something(vertex);  
```  
  
I agree that it would be _nice_ if the result of `vertices(g)` were a standard readable range... but in my opinion it's a fairly low priority given what I have demonstrated above.

---

## Comment 7

> Username: mkatliar  
> Created at: 2018-11-23 12:32:54 UTC  
> Url: https://github.com/boostorg/graph/issues/122#issuecomment-441229975  

I discovered that there are some issues with using `boost::iterator_range` in combination with `boost::counting_iterator`: https://github.com/boostorg/range/issues/82, https://github.com/boostorg/range/issues/83. `boost::counting_iterator` is used as an implementation for some iterators in BGL.
