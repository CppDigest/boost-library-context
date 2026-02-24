# #4 Use default visibility for exception_detail::clone_impl<T> [Merged]

> Username: vslavik  
> Created at: 2015-10-10 15:16:45 UTC  
> Updated at: 2015-10-11 22:10:12 UTC  
> Merged at: 2015-10-11 22:09:46 UTC  
> Closed at: 2015-10-11 22:09:46 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/4  

When building code with hidden visibility on ELF, exception classes must explicitly use the default visibility for catching to work across shared objects; the same must be done for all their base classes. While other classes in the `boost::exception_detail` namespace had visibility adjusted accordingly, `clone_impl<T>` didn't.  
  
The newest version of clang started giving me a warning about this (possibly/probably due to some other changes that added catching):  
  
>  ld: warning: direct access in `void boost::throw_exception<std::runtime_error>(std::runtime_error const&)` to global weak symbol `typeinfo for boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >` means the weak symbol cannot be overridden at runtime. This was likely caused by different translation units being compiled with different visibility settings.  
  
Took me much head-scratching and digging around to realize that the `clone_impl` bit is relevant (`error_info_injector` already has correct visibility) — I do have consistent visibility settings across the projects involved. This PR fixes the warning and is exactly the same thing that is done for other classes in `exception.hpp`.  
  
As a refresher in case you forgot all about it by now, here are some of the more relevant links I went through today:  
1. Visibility author explaining the need for default visibility for exceptions _and_ related classes:  http://stackoverflow.com/questions/14268736/symbol-visibility-exceptions-runtime-error  
2. Previous Boost issue: https://svn.boost.org/trac/boost/ticket/4594  
3. And a more recent related one: https://svn.boost.org/trac/boost/ticket/10899

---

## Comment 1

> Username: zajo  
> Created_at: 2015-10-10 23:55:08 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/4#issuecomment-147137992  

Please correct me if I'm wrong but it seems to me that the compiler is warning you that you can't catch objects of type clone_impl across SO boundaries. If this understanding is correct, note that this class template is not part of the public interface; most definitely users should not be attempting to catch exceptions of that type.

---

## Comment 2

> Username: vslavik  
> Created_at: 2015-10-11 06:54:44 UTC  
> Updated_at: 2015-10-11 06:55:25 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/4#issuecomment-147164563  

> Please correct me if I'm wrong  
  
Correcting away: As a super-quick hint that you might be on a wrong track, observe how I mentioned `error_info_injector` which _also isn't part of public interface_, yet it _already has this treatment_ in Boost's code. In terms of public API, there's no difference between `error_info_injector` and `clone_impl` (both are off limits), so if such code is good for the former, it should be appropriate for the latter (i.e. this PR) as well.  
  
**Longer version:**  
  
This has nothing to do with catching `clone_impl` (explicitly). Re-reading the PR, this was poor phrasing on my part, catching exceptions is the reason why `exception.hpp` deals with visibility at all in the first place, not the direct reason for this PR's fix.   
  
Even then, though, the fact that `clone_impl` is considered-private at C++ source level is irrelevant: what matters is that at runtime, this private class is what is caught and _this_ type's typeinfo is used to find the base classes and match them in `catch`. User code is not catching `clone_impl` explicitly, but under the hood, it's being caught _all the time_ and its typeinfo inspected to find the base exception the user code actually is referencing.  
  
 The linker (not compiler) is telling me a few things:  
1. The problem is in _the implementation of `boost::throw_exception<T>`_, not my code. This implementation is inline in the header and as such is compiled into weak-linked symbol, in my case with hidden visibility (because the code doesn't say otherwise).  
2. This library code references `typeinfo for boost::exception_detail::clone_impl<…>`.  
3. It tells me `clone_impl` has hidden visibility too — that's the "direct access" part. It seems the symbol is hidden, i.e. used only in the translation unit and a) it can call it more efficiently without lookup and b) it doesn't need to be exposed in the global symbol table (as symbols with default visibility are). Consequently, it won't be merged with other instances of the same weak (because inline!) symbol ("weak symbol cannot be overridden at runtime").  
  
This last bit is what matters. If `clone_impl` is compiled as a symbol with hidden visibility, a private copy in the translation unit (with code that catches, again, not `clone_impl`, but anything thrown via `throw_exception`) is used and won't be correctly matched to the "real" exception (e.g. `runtime_error` here) when throwing across SO boundaries. This is a well-understood problem that is remedied by using default visibility for  exception classes — as Boost _already does_ for all the rest of things involved, just with the curious omission of `clone_impl` (which I think is a fair guess happened at some point as an accident).  
  
(_My_ problem that prompted this PR isn't even with that kind of catching, but with the linker being smart, detecting problematic code and warning about it. The warning is legit though, it uncovers a real (albeit obscure) issue with the code.)  
  
**Even longer version:**  
  
Please refer to the three links I included, they discuss this very issue (only applied on the other non-catchable classes in the same header) and actually explain it pretty well.

---

## Comment 3

> Username: zajo  
> Created_at: 2015-10-11 22:10:12 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/4#issuecomment-147250662  

Yes indeed. Thanks!

---
