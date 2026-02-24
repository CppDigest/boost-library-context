# #162 - Add delimiter[] directive that applies only to the new permutation parser [Closed]

> Username: tzlaine  
> Created at: 2024-03-10 21:24:28 UTC  
> Updated at: 2024-12-20 04:27:41 UTC  
> Closed at: 2024-12-20 04:27:41 UTC  
> Url: https://github.com/boostorg/parser/issues/162  

This is to make it easy to write `a || b || c`, and have delimiters in between all of them, but not before the first or after the last.  Without this directive, you'd have to establish some kind of counter that all the subparsers above would be able to see and increment, and have all of them skip, then parse a delimiter, if they were not the first one used.
