# #64 - Docs: recommended changes [Open]

> Username: akrzemi1  
> Created at: 2023-08-10 04:06:38 UTC  
> Updated at: 2023-08-10 05:15:41 UTC  
> Url: https://github.com/boostorg/cobalt/issues/64  

The following suggestions for changes in the documentation come from a perspective of someone who is not very well familiar with asynchronity, C++ coroutines, or Node.js/Python, someone who also wants to learn something about asynchronity.  
  
- [ ] Following the advice from Robert Ramey, the first thing that the docs should say, in very brief, is what this library is for, so that I can make a call whether I will need it, and if it is worth investing the next 15 minutes in reading the library overview. Currently the docs start with a warning about MSVC compilers and from the tables representing parts of the library. This information, useful as it is, should come a bit later. Niall Douglas made an introduction in the Boost Review announcement, and maybe you want to reuse parts of it. He mentions terms "coroutine support library with excellent support for foreign asynchronous design patterns".  
- [ ] It looks like the introduction assumes that the reader is already familiar with the Node.js or Python equivalents.   
- [ ] The introduction seems to use terms "concurrent" and "asynchronous" interchnageably, but I do not think they are. The former stresses the fact that one task doesn't need to finish bofore another starts. the latter stresses the control flow: one operation is not interested in the results of another operation that is being launched. I think your library enables both single-threades concurrency (`generator`) and single-threaded asynchrony (`task`, `promise`).  
- [ ] Does this mean this library cannot be used for multi-threaded asynchrony? For instance when I want more than one thread to process the ASIO's task queue?  
- [ ] In the Overview we have "Table 1. Coroutine types". But is this name correct? I do not think `promise` or `generator` are *coroutine types* or coroutines. They are types that help define when coroutine suspends and what interface is exposed to the caller. But "coroutine" in C++ nomenclature is the body of the function inside the curly braces. I know there is no good term for that. Maybe use "Coroutine Interface Types".  
- [ ] Mention in the initial sections that this library depends on Boost.ASIO (if it does), or that parts of it do not depend on Boost.ASIO (I can see some ASIO headers beeing `#include`d)  
- [ ] Document if the familiarity with Boost.ASIO is necessary to understand these docs, and if using this library without Boost.ASIO makes sense.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-10 05:15:41 UTC  
> Url: https://github.com/boostorg/cobalt/issues/64#issuecomment-1672566804  

Thanks for the suggestions Andrzej. I'l work on it after the review.
