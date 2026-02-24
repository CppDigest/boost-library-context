# #66 - Consider providing front and back aliases [Closed]

> Username: ldionne  
> Created at: 2015-05-04 17:03:04 UTC  
> Updated at: 2015-06-28 10:30:17 UTC  
> Closed at: 2015-06-28 10:30:17 UTC  
> Url: https://github.com/boostorg/hana/issues/66  

For consistency with C++, we might want to provide `front` and `back` aliases to `head` and `last`. Actually, a renaming might even be better, but then we would have to reconsider `tail` and `init`. I don't really like `tail` and I really don't like `init`, but I'm strongly against `pop_front` and `pop_back` too, so there's a dilemma.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-06 15:37:05 UTC  
> Url: https://github.com/boostorg/hana/issues/66#issuecomment-109604584  

How about `drop_front`, `drop_back`, `front` and `back`?

---

## Comment 2

> Username: vendethiel  
> Created at: 2015-06-06 16:20:42 UTC  
> Url: https://github.com/boostorg/hana/issues/66#issuecomment-109610575  

why did C++ even go with "front" and "last" in the first place?

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-07 02:53:12 UTC  
> Url: https://github.com/boostorg/hana/issues/66#issuecomment-109678085  

I don't know. Do you have something else to propose?

---

## Comment 4

> Username: vendethiel  
> Created at: 2015-06-07 06:52:08 UTC  
> Url: https://github.com/boostorg/hana/issues/66#issuecomment-109705697  

No, I'm used to `head` and `tail` myself

---

## Comment 5

> Username: Manu343726  
> Created at: 2015-06-11 20:48:52 UTC  
> Url: https://github.com/boostorg/hana/issues/66#issuecomment-111273905  

This is one of such debates where C++ers vs FPers may fight between. For me, since many Hana features have a functional flavor, I would stuck with the Haskell like naming conventions: `map`, `filter`, `head`, etc.   
On the other hand, it's true that most of C++ people would be used much more to the STLish names such as `transform`, `remove_if`, `front`, etc; than to the Haskell alternatives.  
Here I wonder why we have aliases for type names, aliases for variable names (i.e. references), but no aliases for function names...  
  
``` cpp  
#define funcdef(f, name) auto name = that_trick_to_fix_odr_fuckoff([](auto... args){ return f(std::forward<decltype(args)>(args)...); });  
```  
  
Doing this with metafunctions was easy xD. I wrote high-order metafunctions with fp names, then a couple of aliases for C++ers.  
  
I would prefer to have both naming alternatives alternatives, but the approaches that come to my mind do not scale at all:  
- `functional` namespace with aliases. primary names are STLish. Manual aliasing, boring and error prone.  
- `ENABLE_XXX_NAMING` macro to switch between naming conventions. Same problems as above, plus ugly preprocessor

---

## Comment 6

> Username: ldionne  
> Created at: 2015-06-11 20:56:58 UTC  
> Url: https://github.com/boostorg/hana/issues/66#issuecomment-111275419  

I fear that having two different conventions is not realistic. First, both would have to be maintained, which is problematic. But another problem is that people won't be able to talk about their use of Hana because there will be no agreed on convention. For example, imagine trying to ask a Stackoverflow answer about Hana if there were two different conventions. Which function names would you search for? Instead, I'd rather (gently) dictate which names are used, while of course making sure they kinda satisfy most of the people.  
  
I kinda like `drop_front`, `drop_back`, `back` and `front`. In addition, `drop_front` and `drop_back` could take an optional integer representing the number of elements to be dropped, which would make `drop_front` equivalent to the current `drop`. This way, we could even remove one function (`drop`).  
  
Also, regarding `map` specifically; this isn't possible because of the `Map` data structure. It would be too confusing, especially for C++ programmers.
