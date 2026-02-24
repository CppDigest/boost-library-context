# #64 - Consider search(), split(), and other algorithms that might take parsers [Closed]

> Username: tzlaine  
> Created at: 2024-01-15 01:19:45 UTC  
> Updated at: 2024-01-20 09:44:06 UTC  
> Closed at: 2024-01-20 09:44:06 UTC  
> Url: https://github.com/boostorg/parser/issues/64  

You can currently use a parser to match the entire input, or some prefix of the input.  You cannot find just the first occurrance of a match, or all matches, of a parser `p` to the input, if those matches are not all right at the beginning.  
  
We could have `search()` to find a single match, `search_all()` to find all of them (`find_match()`/`find_matches()` is probably better).  We could also have `split()` to generate a range of subranges delimited by matches of `p`.  `search_all()`/`find_matches()` and `split()` would produce views of course.  
  
We could also have some kind of transforming parse, that produces a view of attributes.
