# #1114 - Enable non-header-only compilation of Beast [Closed]

> Username: djarek  
> Created at: 2018-05-04 00:02:24 UTC  
> Updated at: 2018-07-16 13:49:00 UTC  
> Closed at: 2018-07-16 13:49:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1114  

ASIO supports non-header-only to speed up compilation, Beast should follow this pattern.  
  
Actions required:  
* `*.hpp` impl files should contain only template code  
* `*.ipp` impl files should contain code which can be compiled in a separate TU

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-04 00:02:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1114#issuecomment-386472032  

Good to know

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-06-03 00:32:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1114#issuecomment-394126451  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: TechnikEmpire  
> Created at: 2018-06-09 07:51:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1114#issuecomment-395949142  

FYI this isnt about speed but about   
supporting a use-case of compiling into shared libraries.   
  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/using.html#boost_asio.using.optional_separate_compilation  
  
Also, afaik, this system in asio is more complicated because there's a great deal of OS abstraction (os specific code) in asio and also there's hidden helpers via static initializers that don't play nice in a shared library context on all platforms.  
  
Given this, I believe the use case would simply become:  
  
- Asio is shared lib.  
- Beast doesn't care.  
- Everyone lives happily ever after.  
  
I guess what I'm saying is don't take asio's structure or compilation options as a standard to follow. It's very asio-specific and beast != asio.  
  
Also I'd be interested to see the use case where someone is constantly editing and using beast and incurring a significant performance penalty. Recent compiler/toolchain benchmarks are pretty stellar and so are standard processors these days. :)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-09 13:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1114#issuecomment-395968777  

Yeah, I looked through the .ipp files and there isn't really much that would go into a translation unit.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-07-09 13:22:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1114#issuecomment-403476591  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-07-16 13:48:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1114#issuecomment-405253403  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
