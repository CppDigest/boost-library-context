# #99 bit.hpp: Fix MSVC warnings about constant conditional expressions [Closed]

> Username: Lastique  
> Created at: 2021-10-31 21:07:33 UTC  
> Updated at: 2021-11-01 19:56:39 UTC  
> Closed at: 2021-11-01 19:36:01 UTC  
> Url: https://github.com/boostorg/core/pull/99  

Also use `if constexpr` where possible.  
  
Closes https://github.com/boostorg/core/issues/98.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-10-31 22:53:55 UTC  
> Url: https://github.com/boostorg/core/pull/99#issuecomment-955806297  

Don't older MSVC versions (which do not have if-constexpr) also have this warning, though? I would have thought multiple overloads with enable-if would be the way to go here.

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-11-01 01:17:10 UTC  
> Url: https://github.com/boostorg/core/pull/99#issuecomment-955842508  

> Don't older MSVC versions (which do not have if-constexpr) also have this warning, though?  
  
This is why I also disable the warning with a pragma.  
  
> I would have thought multiple overloads with enable-if would be the way to go here.  
  
`enable_if` is not necessary, a simple set of overloads with a tag argument would suffice. But that would be a more invasive change that I didn't feel like doing before 1.78. I can do that if that's the preferred way.

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-11-01 14:17:31 UTC  
> Url: https://github.com/boostorg/core/pull/99#issuecomment-956273432  

I suppose I can rewrite it with tag dispatch, but I don't see it as an improvement. I've disabled the warnings.  
  
Andrey, since I enabled `warnings-as-errors`, I had to touch some of the tests you wrote. Feel free to revert those and apply a different suppression if you prefer.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-11-01 16:53:06 UTC  
> Url: https://github.com/boostorg/core/pull/99#issuecomment-956402802  

Should I close this PR?  
  
> Andrey, since I enabled `warnings-as-errors`, I had to touch some of the tests you wrote.  
  
That's ok, though I generally don't support warnings as errors.

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-11-01 19:56:39 UTC  
> Url: https://github.com/boostorg/core/pull/99#issuecomment-956542141  

There's no sense in fixing warnings if we don't use warnings as errors in the tests. Every change inevitably has a chance of reintroducing some.

---
