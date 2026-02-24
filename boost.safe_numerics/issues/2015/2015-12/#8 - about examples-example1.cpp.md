# #8 - about examples/example1.cpp [Closed]

> Username: akrzemi1  
> Created at: 2015-12-15 09:36:00 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2017-04-09 23:03:04 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/8  

A couple of suggestions how [examples/example1.cpp](https://github.com/robertramey/safe_numerics/blob/master/examples/example1.cpp) could be improved:  
  
(1) The value that is assigned to variable `x` (127) assumes that type `char` is signed, which is true on some platforms, but the Standard allows the implementations to make it unsigned, and I can read [here](http://stackoverflow.com/a/15533179/838509) that on EBCDIC platforms it is in fact unsigned. On these platforms the example does not illustrate what the example was intended to illustrate. Perhaps use value `CHAR_MAX` instead or make a note or `static_assert` that you assume a `signed char`.  
  
(2) In line 18 the comment says "but assert fails to detect it since C++ implicitly converts variables to int before evaluating he expression!" which looks wrong. If you put an assertion for equality: `assert(z == x + y)` it does detect the problem alright.

---

## Comment 1

> Username: robertramey  
> Created at: 2015-12-15 17:22:27 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/8#issuecomment-164832925  

very good catch - this is a very, very key exampl  
  
On 12/15/15 1:36 AM, Andrzej Krzemieński wrote:  
  
> A couple of suggestions how examples/example1cpp   
> https://githubcom/robertramey/safe_numerics/blob/master/examples/example1cpp   
> could be improved:  
>   
> (1) The value that is assigned to variable |x| (127) assumes that type   
> |char| is signed, which is true on some platforms, but the Standard   
> allows the implementations to make it unsigned, and I can read here   
> http://stackoverflowcom/a/15533179/838509 that on EBCDIC platforms   
> it is in fact unsigned On these platforms the example does not   
> illustrate what the example was intended to illustrate Perhaps use   
> value |CHAR_MAX| instead or make a note or |static_assert| that you   
> assume a |signed char|  
>   
> I changed it to std::int8_t to remove any ambiguity.  
>   
> (2) In line 18 the comment says "but assert fails to detect it since   
> C++ implicitly converts variables to int before evaluating he   
> expression!" which looks wrong If you put an assertion for equality:   
> |assert(z == x + y)| it does detect the problem alright  
>   
> of course your right.  But then when the assert traps the program fails   
> to run to completion so I had to comment it out.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/robertramey/safe_numerics/issues/8.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: robertramey  
> Created at: 2017-04-09 23:03:04 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/8#issuecomment-292819929  

fixed
