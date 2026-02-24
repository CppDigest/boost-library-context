# #135 - Docs say 'if' when they meant to say 'of' [Closed]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 17:11:08 UTC  
> Updated at: 2024-03-15 21:29:29 UTC  
> Closed at: 2024-03-15 21:29:29 UTC  
> Url: https://github.com/boostorg/parser/issues/135  

On the page [Parsing in Detail](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/parsing_in_detail.html) the docs say:  
  
> Control passes up to the parent if int_pair, +int_pair.   
  
they meant to say  
  
> Control passes up to the parent of int_pair, +int_pair.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 23:50:00 UTC  
> Url: https://github.com/boostorg/parser/issues/135#issuecomment-1970109843  

Thanks for pointing out all these typos!  These small mistakes are essentially invisible to me.   I'vebeen reading, re-reading, and editing these docs for weeks, and still missed all this stuff.  This and the other open issues are fixed on my mid-review branch.
