# #61 use Python version of Docca [Merged]

> Username: grisumbras  
> Created at: 2025-05-02 11:01:59 UTC  
> Updated at: 2025-10-22 16:36:36 UTC  
> Merged at: 2025-10-22 16:35:38 UTC  
> Closed at: 2025-10-22 16:35:38 UTC  
> Url: https://github.com/boostorg/static_string/pull/61  

This switches the project to use Python version of Docca.  
  
Changes on the results:  
* Better support for `constexpr` and `noexcept`.  
* Links in code declaration code blocks (e.g. to parameter and return types).  
* Top-level sorting of names.  
* Some other improvements (not sure if relevant to StaticString).  
  
Changes on tooling:  
* Removes dependency on Java and Saxon HE.  
* Adds dependency on Python 3 and Jinja (both already required by Boost docs build).

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-05-02 11:09:33 UTC  
> Url: https://github.com/boostorg/static_string/pull/61#issuecomment-2846960168  

An automated preview of the documentation is available at [https://61.static-string.prtest2.cppalliance.org/libs/static_string/doc/html/index.html](https://61.static-string.prtest2.cppalliance.org/libs/static_string/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-10-22 10:26:51 UTC  
> Url: https://github.com/boostorg/static_string/pull/61#issuecomment-3431625219  

An automated preview of the documentation is available at [https://61.static-string.prtest2.cppalliance.org/libs/static_string/doc/html/index.html](https://61.static-string.prtest2.cppalliance.org/libs/static_string/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-22 10:26:50 UTC

---

## Comment 3

> Username: gennaroprota  
> Created_at: 2025-10-22 16:36:36 UTC  
> Url: https://github.com/boostorg/static_string/pull/61#issuecomment-3433268324  

Merged, thanks.

---
