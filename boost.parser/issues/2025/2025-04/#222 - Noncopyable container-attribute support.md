# #222 - Noncopyable container-attribute support [Closed]

> Username: psiha  
> Created at: 2025-04-19 15:11:12 UTC  
> Updated at: 2025-05-05 10:57:32 UTC  
> Closed at: 2025-04-26 22:17:18 UTC  
> Url: https://github.com/boostorg/parser/issues/222  

Compilation fails w/ (custom) container output types which are only explicitly copy constructible (or not even copy constructible at all):  
https://godbolt.org/z/xr3W8r4G6  
  
The example reveals a few other issues:  
- even though the documentation claims no internal allocations should usually be necessary even this simple example creates a std::map (which has further binsize implications)  
- calls to std iostreams (evil bloatware) are also clearly visible - can this be replace at least with std::format/print (and ideally refactored so that formating code is not present at all if not used)  
- in general there seems a bit too much code generated for such a simple example.

---

## Comment 1

> Username: psiha  
> Created at: 2025-04-24 07:21:31 UTC  
> Url: https://github.com/boostorg/parser/issues/222#issuecomment-2826625786  

I understand that the docs say `There is no support for move-only or non-default-constructible types.` but is there room for change here - as in the case of containers e.g. this is also a matter of overhead/efficiency (we don't want to have these copied 'behind our backs')?

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-04-26 22:17:18 UTC  
> Url: https://github.com/boostorg/parser/issues/222#issuecomment-2832657746  

> Compilation fails w/ (custom) container output types which are only explicitly copy constructible (or not even copy constructible at all): https://godbolt.org/z/xr3W8r4G6  
  
Only Regular types are supported.  So your types must be default constructible and copyable.  That said, if you find a case of a copy being made where a move should instead, please report that.  
  
> The example reveals a few other issues:  
>   
> * even though the documentation claims no internal allocations should usually be necessary even this simple example creates a std::map (which has further binsize implications)  
  
A map is used for the symbol table logic.  If you con't use symbol tables, that map remains empty -- no allocations should occur.  
  
> * calls to std iostreams (evil bloatware) are also clearly visible - can this be replace at least with std::format/print (and ideally refactored so that formating code is not present at all if not used)  
  
I'd much rather have used std::print from the start, but alas it's still not in very widespread use.  I will be making that change at some point though.  
  
> * in general there seems a bit too much code generated for such a simple example.  
  
Then maybe this library is not for you.

---

## Comment 3

> Username: psiha  
> Created at: 2025-05-05 10:57:31 UTC  
> Url: https://github.com/boostorg/parser/issues/222#issuecomment-2850628122  

> Only Regular types are supported. So your types must be default constructible and copyable. That said, if you find a case of a copy being made where a move should instead, please report that.  
  
Sure, I realize that - I'm wondering where does this requirement come from? X3 works without it...  
IOW what would be a situation where you'd need to _copy_ a _container_?  
  
  
> A map is used for the symbol table logic. If you con't use symbol tables, that map remains empty -- no allocations should occur.  
  
I'll return to this in a separate ticket...  
  
  
> I'd much rather have used std::print from the start, but alas it's still not in very widespread use. I will be making that change at some point though.  
  
Honest question: what would be places/platforms where it is not available? "Most" platforms (Android, Linux, Windows, WASM...) have easy access to the latest or at least very recent compilers with solely Apple being 'the new Microsoft' by lagging behind but even there std::format works for some time already (std::print is another matter, requiring latest OS versions)...  
  
In any case there are some relatively easy/'text book' changes that could be implemented to at least mitigate some of the bloat. Most notably extracting template-independent (or 'less depedent') bits.  
For example throw sites generate non trivial code especially w/ GCC and compatibles (where the exception is heap allocated so we get two calls - alloc + exception construction):  
so first extract this:  
```  
[[ gnu::cold, gnu::noinline, noreturn ]]  
void throw_parse_error( pos_t const position, char const * const msg ) {  
    throw boost::parser::parse_error( position, msg );  
}  
```  
  
second, the parser printing is partially independent from the enclosing function template arguments so we make a version for the current stringstream approach:  
```  
[[ gnu::cold, gnu::noinline, noreturn ]]  
void throw_parse_error( pos_t const position, std::stringstream const & msg ) {  
    throw boost::parser::parse_error( position, msg.str() );  
}  
  
[[ gnu::cold, gnu::noinline, noreturn ]]  
void throw_parse_error( pos_t const position, auto const & ctx, auto const & parser ) {  
    std::stringstream oss;  
    detail::print_parser(context, parser, oss);  
throw_parse_error( position, oss );  
}  
```  
  
Next, if not switching to std::format, would be to allow for a configurable printing facility (even if it still requires the streams interface - it is relatively easy to implement a 'light std::string streamer' - a wrapper which simply provides all the '<<' overloads, and so on...  
  
  
> > * in general there seems a bit too much code generated for such a simple example.  
>   
> Then maybe this library is not for you.  
  
As in the library does not care about efficiency, e.g. even if X3 generates less code for the 'same' thing you just can't be bothered about that or just that it is a lower priority but you would accept PRs and/or general ideas about improving in these areas?
