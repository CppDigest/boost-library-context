# #585 - [Question] How to create a real_policy to honor max. representable digits? [Closed]

> Username: jpfeuffer  
> Created at: 2020-04-08 12:40:35 UTC  
> Updated at: 2021-02-15 13:54:50 UTC  
> Closed at: 2021-02-15 13:54:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/585  

Hi!  
  
We would like to construct a policy that generates strings from let's say doubles with the minimally needed amount of digits to uniquely read them back in.  
Unfortunately, the "precision" member only controls the number of digits in the fraction part and if you have a long integer part, it will create strings that are  
a) unreasonably long (since they cannot be accurately represented [EDIT: sorry, they can but are not needed for uniqueness] when read back in) and  
~~b) I am not sure how those additional digits are generated from the internal representation and how they even would make sense~~  
  
I understand that I could overwrite precision with something like this:  
`return std::numeric_limits<T>::max_digits10::value - floor(log10(n));`  
  
But I am not sure how that would work with scientific representations and how safe and fast this method would be (related to #529 )

---

## Comment 1

> Username: jpfeuffer  
> Created at: 2020-04-09 17:09:32 UTC  
> Updated at: 2020-04-09 17:12:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/585#issuecomment-611642767  

I realized that this might be impossible since karma seems to limit the fractional digits for double to 16 (no matter what I return in the precision member),  
which is not enough for fixed notation. Very unfortunate. I'd rather not use scientific notation for everything below 1.
