# #126 - What's with the "template<class = void>"? [Closed]

> Username: vinniefalco  
> Created at: 2016-10-11 21:27:04 UTC  
> Updated at: 2016-10-19 11:06:41 UTC  
> Closed at: 2016-10-19 11:06:41 UTC  
> Url: https://github.com/boostorg/beast/issues/126  

`template<class = void>` is one of the techniques used to make function and class definitions header-only. The alternative is to use `inline`. What should Beast use?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-11 21:33:04 UTC  
> Url: https://github.com/boostorg/beast/issues/126#issuecomment-253053314  

If this becomes a problem during the Boost review, I could add this:  
  
```  
#ifndef BEAST_INLINE  
# if BEAST_INLINE_KEYWORD  
#  define BEAST_INLINE inline  
# else  
#  define BEAST_INLINE template<class = void>  
# endif  
#endif  
```  
  
and let the users of the library choose which version to use.

---

## Comment 2

> Username: HowardHinnant  
> Created at: 2016-10-11 21:34:02 UTC  
> Url: https://github.com/boostorg/beast/issues/126#issuecomment-253053560  

It is true that `inline` is only a hint.  The only normative effect it has is to give functions (and variables in C++17) "weak linkage".  That is, allow multiple definitions among translation units (as long as they are all identical), and have the linker pick one.  Templating has the same effect as far as weak linkage goes.  
  
However I believe it valuable that the programmer use `inline` judiciously as if the compiler were actually going to respect the hint.  Don't `inline` things that are too big to inline, and do `inline` things that are small enough to inline.  If nothing else, programmer intent is documented.  And a future maintenance pass could more easily replace `inline` with the various (implementation defined) versions of "always inline".  
  
If a function is too big to inline, and still needs weak linkage to meet the header-only goals of a library, the only tool left is the gratuitous template technique.

---

## Comment 3

> Username: seelabs  
> Created at: 2016-10-11 21:47:09 UTC  
> Url: https://github.com/boostorg/beast/issues/126#issuecomment-253056654  

Bikeshed: Rename `BEAST_INLINE` macro `BEAST_WEAK_LINKAGE` to show intent?   
  
FWIW, I prefer `inline` to the gratuitous template. I'm used to seeing `inline` in header-only libraries to allow multiple definitions, and I suspect most C++ programers would know what it means. I also suspect (but don't know) that a compiler is going to optimize both constructs exactly the same. Reaching for a `template` here may make people wonder if something else is going on.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-11 21:49:48 UTC  
> Url: https://github.com/boostorg/beast/issues/126#issuecomment-253057222  

I'm open to a rename

---

## Comment 5

> Username: HowardHinnant  
> Created at: 2016-10-12 03:03:30 UTC  
> Url: https://github.com/boostorg/beast/issues/126#issuecomment-253106593  

I'm used to seeing massive amounts of templated code that had to be templated (e.g. the entire implementation of `map<Key, T, Compare, Allocator>`) be gratuitously inlined and compilers swallowing that hint hook line and sinker.   This was generally done by implicitly inlining member functions by defining them within the class declarations.  The compilers literally did not know how to outline them.  And neither did the programmers.  The result:  templates == code bloat.  
  
The situation is much better these days.  But we are doomed to repeat history if we forget this mistake.  
  
Clearly document what you intend to be inlined, and what you intend to be outlined.  Doing otherwise leads us back to a giant mess that we are still in the process of successfully fighting our way out of.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-19 11:06:41 UTC  
> Url: https://github.com/boostorg/beast/issues/126#issuecomment-254782063  

I think we're good here
