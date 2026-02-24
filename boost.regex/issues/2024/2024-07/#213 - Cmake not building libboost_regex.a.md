# #213 - Cmake not building libboost_regex.a [Closed]

> Username: Duron27  
> Created at: 2024-07-04 23:26:29 UTC  
> Updated at: 2024-07-05 10:06:21 UTC  
> Closed at: 2024-07-05 10:06:21 UTC  
> Url: https://github.com/boostorg/regex/issues/213  

Not sure why,  it used to I thought  
And if i let it build everything regex is still missing  
cmake ../  -DBOOST_INCLUDE_LIBRARIES="filesystem;program_options;iostreams;regex"

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-07-05 08:32:02 UTC  
> Url: https://github.com/boostorg/regex/issues/213#issuecomment-2210440264  

Other than some deprecated legacy stuff, the separate library hasn't been needed for a few releases now (it's header only).

---

## Comment 2

> Username: Duron27  
> Created at: 2024-07-05 10:06:21 UTC  
> Url: https://github.com/boostorg/regex/issues/213#issuecomment-2210593285  

Ok thanks
