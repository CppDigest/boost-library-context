# #100 - Fix doc claim that BOOST_PARSER_DEFINE_RULES is conditionally available [Closed]

> Username: akrzemi1  
> Created at: 2024-02-21 21:22:15 UTC  
> Updated at: 2024-03-15 21:29:26 UTC  
> Closed at: 2024-03-15 21:29:26 UTC  
> Url: https://github.com/boostorg/parser/issues/100  

The docs for `BOOST_PARSER_DEFINE_RULES` say:  
> This macro is only available when the Boost.Preprocessor headers are available.   
  
[here](https://tzlaine.github.io/parser/doc/html/BOOST_PARSER_DEFINE_RULES.html)  
  
So how do I define a rule in a stand-alone (non-Boost) version?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-22 08:19:48 UTC  
> Url: https://github.com/boostorg/parser/issues/100#issuecomment-1958926580  

That's just a doc bug (cruft from a previous implementation).  It's always available now.
