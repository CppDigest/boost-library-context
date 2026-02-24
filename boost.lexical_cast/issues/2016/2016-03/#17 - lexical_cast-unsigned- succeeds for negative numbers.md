# #17 - lexical_cast<unsigned> succeeds for negative numbers [Closed]

> Username: petersohn  
> Created at: 2016-03-21 09:43:33 UTC  
> Updated at: 2016-03-21 19:53:24 UTC  
> Closed at: 2016-03-21 19:53:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/17  

Attempting to convert a string containing a number that is too big for an `int` but fits into `unsigned` throws a `bad_lexical_cast`, as expected. However, attempting to convert a string with a negative number to `unsigned` succeeds, although it should also throw.  
  
See example here: http://coliru.stacked-crooked.com/a/73d91c38e5eaa3da

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-03-21 19:53:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/17#issuecomment-199447966  

That's a known and sad "feature" of the C:  
  
boost::lexical_cast has the behavior of std::stringstream, which uses num_get functions of std::locale to convert numbers. If we look at the Programming languages — C++, we'll see, that num_get uses the rules of scanf for conversions. And in the C99 standard for unsigned input value minus sign is optional, so if a negative number is read, no errors will arise and the result will be the two's complement.
