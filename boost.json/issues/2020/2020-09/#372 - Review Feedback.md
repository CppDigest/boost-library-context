# #372 - Review Feedback [Closed]

> Username: vinniefalco  
> Created at: 2020-09-21 00:24:07 UTC  
> Updated at: 2020-09-22 17:38:41 UTC  
> Closed at: 2020-09-22 17:38:40 UTC  
> Url: https://github.com/boostorg/json/issues/372  

* `value::storage()` should say: Return a reference to the `storage_ptr` to the  
   `memory_resource` associated with the value.  The same thing applies to a few  
   other classes.  
* The reference for the `initializer_list` constructor could use some improvements:  
   e.g. "Construct an object or array from an `initializer_list`".  I find this  
   hugely important since just the mere presence of an `initializer_list`  
   constructor changes the meaning of brace initialization.  
* Additionally, I doesn't mention how nested `initializer_list` how are treated.  
   Most importantly it should show or link to some examples on how the  
   `initializer_list` are converted to objects, arrays, and non structured  
   values because some developers (if not most) might not immediately realize  
   from just looking at the reference documentation how nested  
   `initializer_list` are parsed.  Maybe also reference to the  
   `initializer_list` constructors of `array` and `object`.  
* `object::insert` is not well-documented.  I did not understand what it does  
   when I insert an already existing key, in particular, whether assignment  
   takes place or not.  The same is true for `object::emplace`.  
* The `object` initializer constructor is listed under "Copy constructor".  
* The `object::erase` return value documentation is wrong.  
* `object::reserve` says "This inserts an element into the container."  
* `object::swap(object)` and the friend version `swap(object,object)` have  
   rather inconsistent briefs.  
* The initializer_list constructor for array is listed under move constructors.  
* The pilfer constructors are not quite explained.  There's a reference to the  
   paper, but I think the library should have a page that concisely explains what  
   it does and what a user of the library can expect from pilfered objects.  In  
   particular, how the thing differs from move and why a user would ever want to  
   use it instead of move.  Also the reference pages for pilfer and so on don't  
   explain what it does.  
* `array::reserve` shows `BOOST_FORCEINLINE` in the docs.  
* `value_to` is unclear about the precedence of the three listed options  
   (constructor, library provided conversion or `tag_invoke`) if multiple are  
   present provided this is actually possible.  The same is true for `value_from`.  
* `storage_ptr` thread safety is not documented.  That's a real bummer for a  
   `shared_ptr`-like construct.  
* I found it rather difficult to figure out the difference in the behavior of  
   `parser::write` and `parser::write_some` just from reading the documentation.  
   Either reference documentation should clearly state what the difference is.  
   I had to open both pages side by side to actually spot the wording  
   difference.  For example, I think that the documentation of `write_some`  
   should clearly state that it's intended use case is for parsing multiple JSON  
   values from a byte stream presented through one or more buffers whereas  
   `write` is intended for parsing one JSON value from one or more buffers.  At  
   least that's what I concluded from the documentation after some time.  
* The `parser` documentation should either link to or inline provide one or  
   more examples how the parser is supposed to be used.  In particular  
   `write_some` would benefit hugely from an example that shows how to properly  
   extract a stream of JSON values from a sequence of buffers.  
* If I read the documentation correctly, `basic_parser::write` behave more like  
   `parser::write_some` than `parser::write`.  I would prefer the call it  
   `basic_parser::write_some`.  
* The `basic_parser` reference documentation would benefit from either a link  
   to an example or an inline example.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-22 17:38:40 UTC  
> Url: https://github.com/boostorg/json/issues/372#issuecomment-696872028  

These have been broken out into individual issues
