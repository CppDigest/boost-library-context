# #1494 - string_base ideas [Closed]

> Username: vinniefalco  
> Created at: 2019-03-02 16:11:30 UTC  
> Updated at: 2024-02-14 10:31:37 UTC  
> Closed at: 2024-02-14 10:31:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1494  

We can refactor `static_string`:  
  
* Call it `char_string_base`, make it a normal class (i.e. not a template)  
* Eschew `char_traits` and `CharT`, this is for `char` **only** and no locales  
* Express all operations in terms of a virtual member  `realloc`  
* function by function compatibility with `std;:string`  
* Full tests and coverage,   
  
Now we can rewrite `static_string` using `char_string_base`, where `realloc` just throws if the static buffer is exceeded.  
  
Then we can write a new class, a "flexible string" that has both an inline buffer, and an allocator. When the inline buffer overflows, the implementation switches to a dynamic allocation. This can replace some of the code that currently does this manually (e.g. in the parser and RFC range adaptors).

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-14 10:31:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1494#issuecomment-1943480926  

https://github.com/boostorg/beast/pull/2406 replaced `static_string` with Boost.StaticString.
