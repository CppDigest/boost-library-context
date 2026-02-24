# #10 - Is there any way to parse a include able file at compile time? [Closed]

> Username: Superlokkus  
> Created at: 2020-01-21 16:22:54 UTC  
> Updated at: 2020-01-23 10:07:14 UTC  
> Closed at: 2020-01-23 10:07:14 UTC  
> Url: https://github.com/boostorg/metaparse/issues/10  

Because I am almost sure this is not documented or even missing, this question is the same as my SO question https://stackoverflow.com/q/59844966/3537677 :  
  
Is there a way to separate the to be parsed DSL into a file? Like instead of  
  
    using input = BOOST_METAPARSE_STRING("DSL content");  
I want to use something like  
  
    using input = BOOST_METAPARSE_STRING(#include "dsl.txt");  
with dsl.txt containing  
  
    DSL content  
?

---

## Comment 1

> Username: sabel83  
> Created at: 2020-01-22 07:03:21 UTC  
> Url: https://github.com/boostorg/metaparse/issues/10#issuecomment-577038186  

I'm not aware of any better way of doing this, than what is already mentioned in the SO answers.
