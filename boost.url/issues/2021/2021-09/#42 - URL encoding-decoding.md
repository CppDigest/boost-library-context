# #42 - URL encoding/decoding [Closed]

> Username: BlankZhu  
> Created at: 2021-09-10 01:33:19 UTC  
> Updated at: 2021-09-13 00:37:56 UTC  
> Closed at: 2021-09-13 00:37:56 UTC  
> Url: https://github.com/boostorg/url/issues/42  

After a quick glance of  Boost.URL, I wonder if Boost.URL provides any URL encoding/decoding methods working like [curl_easy_escape](https://curl.se/libcurl/c/curl_easy_escape.html) or [curl_easy_unescape](https://curl.se/libcurl/c/curl_easy_unescape.html) in libcurl?  
  
```C++  
// in libcurl  
char *curl_easy_escape( CURL *curl, const char *string , int length );  
char *curl_easy_unescape( CURL *curl, const char *url , int inlength, int *outlength );  
  
// or in c++, something may be like...  
std::string encode_url(std::string_view to_encode);  
std::string decode_url(std::string_view to_decode);  
```  
  
If so, is there any guide, document or code pieces that show how to do it with Boost.URL? Otherwise, is there any plan to support this in the future?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-10 01:36:08 UTC  
> Url: https://github.com/boostorg/url/issues/42#issuecomment-916563124  

Yes it will be a feature, but note that Boost.URL is still in development. Right now, the only thing that works robustly is the `url_view` which is a read-only view. This interface has support for percent-decoding individual fields of the URL as needed.  
  
For example, this function returns the username after applying percent-decoding:  
https://github.com/CPPAlliance/url/blob/4de7268b65cd7db5bdad83e11be680300d5cd5e0/include/boost/url/url_view.hpp#L281  
  
All fields are available in their percent-encoded form after parsing. Use `parse_uri` and `parse_relative_ref` to parse your strings. More parse functions will be added, and there will also be a container that lets you make modifications.  
  
If you have any other questions or feature requests, feel free to ask here or open a new issue, thanks!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-09-10 01:39:12 UTC  
> Url: https://github.com/boostorg/url/issues/42#issuecomment-916564168  

The API would look like this:  
```  
template< class CharSet, class Allocator = std::allocator<char>  >  
string_type< Allocator >  
percent_encode( string_view s, CharSet const& cs, Allocator const& alloc = {} );  
  
template< class Allocator = std::allocator<char>  >  
string_type< Allocator >  
percent_decode( string_view s, Allocator const& alloc = {} );  
  
template< class Allocator = std::allocator<char>  >  
string_type< Allocator >  
percent_decode( string_view s, error_code& ec, Allocator const& alloc = {} );  
```

---

## Comment 3

> Username: BlankZhu  
> Created at: 2021-09-10 02:17:47 UTC  
> Url: https://github.com/boostorg/url/issues/42#issuecomment-916579327  

That will be cool, looking forward to it!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-09-11 05:18:57 UTC  
> Url: https://github.com/boostorg/url/issues/42#issuecomment-917343625  

@BlankZhu Okay, there are some new routines for you to try:  
https://github.com/CPPAlliance/url/blob/develop/include/boost/url/pct_encoding.hpp

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-09-11 05:20:24 UTC  
> Url: https://github.com/boostorg/url/issues/42#issuecomment-917343819  

You can find the _CharSets_ here:  
https://github.com/CPPAlliance/url/blob/develop/include/boost/url/rfc/char_sets.hpp  
  
`pchar_set` is typically used for url-encoded forms. There are also some more char sets here:  
https://github.com/CPPAlliance/url/blob/develop/include/boost/url/bnf/char_set.hpp

---

## Comment 6

> Username: BlankZhu  
> Created at: 2021-09-11 10:02:32 UTC  
> Url: https://github.com/boostorg/url/issues/42#issuecomment-917379647  

The following code runs quite well for me, truly appreciate!  
  
```c++  
std::string raw_str{R"r(こんにちは)r"};  
auto encoded = boost::urls::pct_encode(raw_str, boost::urls::pchar_set{});  
auto decoded = boost::urls::pct_decode(encoded, boost::urls::pchar_set{});  
std::cout << encoded << std::endl;  // %e3%81%93%e3%82%93%e3%81%ab%e3%81%a1%e3%81%af  
std::cout << decoded << std::endl;  // こんにちは  
```  
  
The design of `pct_encode_opts` and `pct_decode_opts` are also very pretty. Can't wait to see the first stable release！
