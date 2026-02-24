# #33 - Back reference documentation bug [Closed]

> Username: codesniffer13  
> Created at: 2017-07-15 13:02:06 UTC  
> Updated at: 2017-07-30 17:51:40 UTC  
> Closed at: 2017-07-30 17:51:40 UTC  
> Url: https://github.com/boostorg/regex/issues/33  

According to the Boost documentation for Back references  
(http://www.boost.org/doc/libs/1_64_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html#boost_regex.syntax.perl_syntax.back_references) which seems to be the same at least from 1.37 - 1.64.  
  
Quoting a piece of the documentation:  
  
-----  
  
For example the expression:  
  
   ^(a*).*\1$  
  
Will match the string:  
  
   aaabbaaa  
  
But not the string:  
  
   aaabba  
  
-----  
  
The string which is specified to not match (aaabba), in fact does match.  Confirmed on the boost-users mailing list that the engine is correct and the documentation is wrong.
