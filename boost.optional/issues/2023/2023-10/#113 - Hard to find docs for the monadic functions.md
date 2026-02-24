# #113 - Hard to find docs for the monadic functions [Closed]

> Username: hadrielk  
> Created at: 2023-10-01 16:35:58 UTC  
> Updated at: 2023-10-05 21:22:41 UTC  
> Closed at: 2023-10-05 21:22:41 UTC  
> Url: https://github.com/boostorg/optional/issues/113  

As far as I can tell, the monadic functions `value_or()`, `map()`, and `flat_map()`, don't have usable documentation pages.  
  
They're only documented within a reference-type [Optional Values](https://www.boost.org/doc/libs/1_83_0/libs/optional/doc/html/boost_optional/reference/header__boost_optional_optional_hpp_/header_optional_optional_values.html) page, but that page is not listed on the [main page](https://www.boost.org/doc/libs/1_83_0/libs/optional/doc/html/index.html)'s table of contents at all. I can only find it by clicking the next-arrow on each page until I finally get there.  
  
Because `boost::optional` does not use the same monadic function names as `std::optional` does, I find myself often having to search for these, to figure out which one is the equivalent of `and_then()` vs. `transform()`.  
  
It would be nice if the main page's ToC listed this reference page, and perhaps had an explicit link to "Monadic Functions" too, or some such.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-10-05 15:44:19 UTC  
> Url: https://github.com/boostorg/optional/issues/113#issuecomment-1749164843  

Thanks.  
Does this work?  
https://www.boost.org/doc/libs/develop/libs/optional/doc/html/boost_optional/tutorial/monadic_interface.html

---

## Comment 2

> Username: hadrielk  
> Created at: 2023-10-05 16:14:15 UTC  
> Url: https://github.com/boostorg/optional/issues/113#issuecomment-1749245450  

Yup works great - thanks!
