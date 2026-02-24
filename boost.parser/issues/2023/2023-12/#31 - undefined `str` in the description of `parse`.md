# #31 - undefined `str` in the description of `parse` [Closed]

> Username: akrzemi1  
> Created at: 2023-12-29 20:46:04 UTC  
> Updated at: 2023-12-30 22:30:12 UTC  
> Closed at: 2023-12-30 22:27:42 UTC  
> Url: https://github.com/boostorg/parser/issues/31  

In this part of reference:  
https://tzlaine.github.io/parser/doc/html/boost/parser/parse_idm20259.html  
  
The description uses term "Parses `str`", whereas the corresponding parameter is called `r`.   
  
The wider issue is that when one looks at the synopsis in https://tzlaine.github.io/parser/doc/html/header/boost/parser/parser_hpp.html, the name `R` doesnt give much hint about what the parameter is. Maybe call it `input` or `text`?

---

## Comment 1

> Username: tzlaine  
> Created at: 2023-12-30 21:30:40 UTC  
> Url: https://github.com/boostorg/parser/issues/31#issuecomment-1872610689  

`R` has become the standard way to call the range passed to an algorithm, as of C++20.  I use it all over the place in my code as well.  I think this is fine.  I don't think people will be confused by the name.  However, I did fix the `str`/`r` confusion -- thanks for pointing that out.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-12-30 22:09:53 UTC  
> Url: https://github.com/boostorg/parser/issues/31#issuecomment-1872615831  

> `R` has become the standard way to call the range passed to an algorithm  
  
Well, maybe you mean "has become the standard in some circles, like WG21"? I must say I wasn't familiar with that pattern. I am used to have identifiers actually reflecting what they represent in the declarations.

---

## Comment 3

> Username: tzlaine  
> Created at: 2023-12-30 22:30:11 UTC  
> Url: https://github.com/boostorg/parser/issues/31#issuecomment-1872618378  

WG21 sure, but also the bible that the whole community collectively uses:  
  
https://en.cppreference.com/w/cpp/algorithm/ranges/sort
