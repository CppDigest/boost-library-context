# #400 - The method `string` is surprising since it does not return a string [Closed]

> Username: madmongo1  
> Created at: 2022-08-13 17:25:37 UTC  
> Updated at: 2022-09-11 23:51:52 UTC  
> Closed at: 2022-09-11 23:51:52 UTC  
> Url: https://github.com/boostorg/url/issues/400  

Before reading the documentation, it seemed obvious to me that in the following code:  
```  
auto  
as_string(boost::urls::url_view uv)  
{  
  return uv.string();  
}  
```  
Would return a string. In fact it returns a string_view, which may well actually be a dangling reference.  
  
It seems to me that this is likely to be the source of many future support calls by surprised library users.  
  
Proposal:  
  
Provide both `url[_view]::string()` which returns a std::string, and `url[_view]::string_view()` which returns a std::string_view.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-16 01:06:40 UTC  
> Url: https://github.com/boostorg/url/issues/400#issuecomment-1216032368  

So once we have `std::string_view string_view() const;`,  
  
- should we also provide `std::string string() const;`?  
- should we provide `operator string_view() const;`, since that's cheap?  
- should we remove `std::string_view string_view() const;` if `operator string_view() const;` is provided?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-16 01:22:29 UTC  
> Url: https://github.com/boostorg/url/issues/400#issuecomment-1216039596  

`string_view url_view_base::encoded_url()` would be consistent with the other names  
  
`string_view url_view_base::str()` looks OK to me too.  
  
There's also an argument to be made for `string_view url_view_base::data()`  
  
`string_view url_view_base::encoded_str` or maybe  
`string_view url_view_base::encoded_string`  
  
If I'm being honest, I don't put very much stock in the argument that `string()` should return a literal `std::string`. I use the term string to refer to string views everywhere in the docs. I am firmly in the "users must be string-view-aware" camp. That means they better know what the return types are and they better treat string views like references in terms of lifetime management.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-16 01:59:00 UTC  
> Url: https://github.com/boostorg/url/issues/400#issuecomment-1216057534  

> If I'm being honest, I don't put very much stock in the argument that string() should return a literal std::string.  
  
I think the names you proposed depend on that premise. In that case, `url_view_base::string()` sounds better. Or `url_view_base::encoded()`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-11 23:51:52 UTC  
> Url: https://github.com/boostorg/url/issues/400#issuecomment-1243071597  

They are all called `buffer` now
