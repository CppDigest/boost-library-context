# #126 - Add tests to cover other internal-failure attribute chicanery [Closed]

> Username: tzlaine  
> Created at: 2024-02-28 00:12:33 UTC  
> Updated at: 2024-03-15 21:29:36 UTC  
> Closed at: 2024-03-15 21:29:36 UTC  
> Url: https://github.com/boostorg/parser/issues/126  

I recently fixed a problem in which a non-attribute parser `p2` was evaluated after a successful attribute parser `p1` in a `seq_parser`.  It's internal failure caused the attribute already set by `p1` to get reset.  
  
I need tests that show this is not happening in other cases.
