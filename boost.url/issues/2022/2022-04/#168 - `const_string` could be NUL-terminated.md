# #168 - `const_string` could be NUL-terminated [Closed]

> Username: sehe  
> Created at: 2022-04-27 02:32:30 UTC  
> Updated at: 2022-07-26 20:47:13 UTC  
> Closed at: 2022-07-26 20:47:13 UTC  
> Url: https://github.com/boostorg/url/issues/168  

If `const_string` guaranteed NUL-termination, some interop would be helped: especially invoking API's that take C-style strings wouldn't require the slightly non-intuitive copy:  
  
```c++  
// Set SNI Hostname (many hosts need this to handshake successfully)  
if(! SSL_set_tlsext_host_name(stream_.native_handle(), std::string(url.host()).c_str()))  
{  
```  
  
Not to mention what happens if people forget about the absense, and just use `url.host().data()` naively.  
  
We could make it explicit by providing the `c_str()` accessor (`boost::url::c_str()` already has it).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-04-27 04:57:57 UTC  
> Url: https://github.com/boostorg/url/issues/168#issuecomment-1110541238  

It would have to be via `c_str()`, because `string_view::data()` is not guaranteed to return a null-terminated string.

---

## Comment 2

> Username: sehe  
> Created at: 2022-04-27 13:43:55 UTC  
> Updated at: 2022-04-27 13:44:52 UTC  
> Url: https://github.com/boostorg/url/issues/168#issuecomment-1111021640  

We could easily make the additional guarantee, of course:  
  
```c++  
char const sz[] = "foo"; // char const[4] includes NUL  
std::string_view sv(sz/*, 3*/);  
::strlen(sv.data()); // guaranteed 3  
```  
  
But yeah, to make the semantics supported by conventional naming, I'd add `c_str()` as a more descriptive alias for `data()`. (Very similar to how `std::basic_string::c_str()`  is _de-facto_ alias for `std::basic_string::data()` since C++11 due to the complexity requirements).
