# #490 - Lex: remove variadic tokenize_and_phrase_parse? [Closed]

> Username: Kojoley  
> Created at: 2019-04-16 23:03:19 UTC  
> Updated at: 2019-04-19 10:13:22 UTC  
> Closed at: 2019-04-19 10:13:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/490  

Both version with and without `skip_flag` does not compile due to bugs (`lex` is not passed here https://github.com/boostorg/spirit/blob/b4c5ef702bf6c28e964a84c9e9abe1a6549bce69/include/boost/spirit/home/lex/tokenize_and_parse_attr.hpp#L104, wrong iterators here https://github.com/boostorg/spirit/blob/b4c5ef702bf6c28e964a84c9e9abe1a6549bce69/include/boost/spirit/home/lex/tokenize_and_parse_attr.hpp#L93). I can fix them, but since there were no complains about either variant in 8 years, probably no one ever tried to use them and it is not worth having them.  
  
Better just remove them instead of fixing, adding tests, etc?

---

## Comment 1

> Username: hkaiser  
> Created at: 2019-04-17 12:39:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/490#issuecomment-484065152  

Fine by me.
