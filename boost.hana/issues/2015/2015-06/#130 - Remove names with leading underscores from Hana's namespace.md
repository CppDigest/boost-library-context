# #130 - Remove names with leading underscores from Hana's namespace [Closed]

> Username: ldionne  
> Created at: 2015-06-17 12:49:21 UTC  
> Updated at: 2015-08-08 12:01:27 UTC  
> Closed at: 2015-08-08 12:01:27 UTC  
> Url: https://github.com/boostorg/hana/issues/130  

When writing `using namespace boost::hana`, we're importing all the names into the global namespace. Because of some names starting with an underscore, that could violate [lex.name] paragraph 3.
