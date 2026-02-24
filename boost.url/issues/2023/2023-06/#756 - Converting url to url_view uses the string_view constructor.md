# #756 - Converting url to url_view uses the string_view constructor [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2023-06-13 18:10:11 UTC  
> Updated at: 2023-07-18 21:52:19 UTC  
> Closed at: 2023-07-18 21:52:19 UTC  
> Url: https://github.com/boostorg/url/issues/756  

If a function takes a `url_view` but is passed a `url`, the compiler considers [`url_view`'s template constructor](https://github.com/boostorg/url/blob/2bae458a79365883d2bee5f5b00d30e99dd5020b/include/boost/url/url_view.hpp#L200-L211) a better match than [the constructor taking a `url_view_base`](https://github.com/boostorg/url/blob/2bae458a79365883d2bee5f5b00d30e99dd5020b/include/boost/url/url_view.hpp#L258-L259), which causes the program to unnecessarily throw away all of the parsing information and re-calculate it.  This was caused by PR #649.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-06-13 18:22:36 UTC  
> Url: https://github.com/boostorg/url/issues/756#issuecomment-1589816013  

Do you have a minimal example of a function you want to work taking `url_view_base` so we replicate the problem when fixing it?

---

## Comment 2

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2023-06-13 18:37:42 UTC  
> Url: https://github.com/boostorg/url/issues/756#issuecomment-1589837423  

I don't want to take a `url_view_base`, I want to take a `url_view`.  For example:  
```c++  
void foo(boost::url_view uv);  
void bar() {  
    boost::url u("http://example.com");  
    foo(u);  
}  
```  
If I call `bar` the string should only be parsed when constructing `u`.  It should not be parsed a second time when `bar` calls `foo` and `u` is converted into a `url_view`.  `url_view_base` is involved since that's the constructor that was selected in boost 1.81 (which didn't parse the string twice), but that's an implementation detail.  If there's some other way of preserving the parsing information that's fine.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2023-06-13 21:57:51 UTC  
> Url: https://github.com/boostorg/url/issues/756#issuecomment-1590076520  

Yes. That definitely needs to be fixed. I just wanted an example to ensure you're solving the issue when testing it.  
  
> If there's some other way of preserving the parsing information that's fine.  
  
You can cast the reference before passing it into the function to make it work for now, but we're definitely going to fix this.
