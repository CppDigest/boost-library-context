# #920 - Fragment improperly preserved when resolving query-only URL [Closed]

> Username: Ericson2314  
> Created at: 2025-08-26 14:49:29 UTC  
> Updated at: 2025-08-26 22:45:00 UTC  
> Closed at: 2025-08-26 22:45:00 UTC  
> Url: https://github.com/boostorg/url/issues/920  

Consider what the browser does with this:  
  
```javascript  
(new URL("?asdf%20qwer=1%202%20","https://www.example.org/path/index.html?a%20b=5%206&x%20y=34#frag")).href  
```  
returns  
```  
https://www.example.org/path/index.html?asdf%20qwer=1%202%20   
```  
  
`#frag` is properly discarded. However, I believe that (unless my calling code messed things up) that Boost URL is returning  
  
```  
https://www.example.org/path/index.html?asdf%20qwer=1%202%20#frag   
```  
  
I caught this working on https://github.com/NixOS/nix/pull/13819 once again

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-08-26 17:10:14 UTC  
> Url: https://github.com/boostorg/url/issues/920#issuecomment-3225037106  

So, do you mean that both libraries discard the fragments and obtain the same results? What's the code calling Boost.URL to generate that?  
  
Isn't it more likely that both libraries are correct and that it should be discarded according to the RFC because the fragment is not part of the resource description?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2025-08-26 17:38:47 UTC  
> Url: https://github.com/boostorg/url/issues/920#issuecomment-3225115491  

> that it should be discarded according to the RFC because the fragment is not part of the resource description?  
  
Oh... I just noticed the ref doesn't even contain a fragment. Only the base does. Maintaining the ref wouldn't only be wrong. It would be absurd because `#frag` represents a location in the base resource.

---

## Comment 3

> Username: xokdvium  
> Created at: 2025-08-26 20:58:27 UTC  
> Url: https://github.com/boostorg/url/issues/920#issuecomment-3225709829  

@alandefreitas, sorry for the confusion. Seems like @Ericson2314 accidentally mis-copied the second example. The issue is that according to https://datatracker.ietf.org/doc/html/rfc3986#section-5.2.2 the fragment is always taken from the reference, but Boost.URL incorrectly preserves the one from the base URL.  
  
> T.fragment = R.fragment;  
  
Here's a minimal example using Boost 1.88:  
  
```c++  
#include <boost/url.hpp>  
#include <iostream>  
  
int main() {  
  boost::urls::url base("https://www.example.org/index.html#frag");  
  boost::urls::url ref("?a=b");  
  base.resolve(ref);  
  std::cout << base;  
}  
```  
  
This program outputs `https://www.example.org/index.html?a=b#frag`.  
  
As you say above:  
  
> Oh... I just noticed the ref doesn't even contain a fragment. Only the base does. Maintaining the ref wouldn't only be wrong. It would be absurd because #frag represents a location in the base resource.  
  
If I'm reading the spec and your comment right then Boost.URL seems to be in the wrong here?

---

## Comment 4

> Username: alandefreitas  
> Created at: 2025-08-26 21:08:48 UTC  
> Url: https://github.com/boostorg/url/issues/920#issuecomment-3225735676  

OK. I debugged the whole thing.  
  
TL;DR: This is an uncovered edge case where Boost.The URL should also remove the fragment. It only happens when the ref has no scheme, no authority, the path is empty, the query is not empty, the fragment is empty, and the target fragment is not empty.  
  
To explain the issue, this is what we get in node.js:  
  
```  
> new URL("?asdf%20qwer=1%202%20","https://www.example.org/path/index.html?a%20b=5%206&x%20y=34#frag")  
URL {  
  href: 'https://www.example.org/path/index.html?asdf%20qwer=1%202%20',  
  origin: 'https://www.example.org',  
  protocol: 'https:',  
  username: '',  
  password: '',  
  host: 'www.example.org',  
  hostname: 'www.example.org',  
  port: '',  
  pathname: '/path/index.html',  
  search: '?asdf%20qwer=1%202%20',  
  searchParams: URLSearchParams { 'asdf qwer' => '1 2 ' },  
  hash: ''  
}  
```  
  
And this fails in Boost.URL:  
  
```cpp  
url u("https://www.example.org/path/index.html?a%20b=5%206&x%20y=34#frag");  
url ref("?asdf%20qwer=1%202%20");  
BOOST_TEST(u.resolve(ref));  
BOOST_TEST_CSTR_EQ(u.buffer(), "https://www.example.org/path/index.html?asdf%20qwer=1%202%20");  
```  
  
with  
  
```  
#20443 url.cpp(1056) failed: "https://www.example.org/path/index.html?asdf%20qwer=1%202%20#frag" == "https://www.example.org/path/index.html?asdf%20qwer=1%202%20" (boost::urls::detail::to_sv(u.buffer()) == boost::urls::detail::to_sv("https://www.example.org/path/index.html?asdf%20qwer=1%202%20"))  
```  
  
meaning Boost.URL does **not** remove the fragment while node.js does.   
  
That's a problem because Boost.URL *should* remove the fragment. node.js is right here because of section [5.2.2 Transform References](https://datatracker.ietf.org/doc/html/rfc3986#section-5.2.2) of the specification.  
  
The pseudo-code in the specification means the fragment ("hash" in node.js) comes from the ref in all cases:  
  
```  
if defined(R.scheme) then  
   T.scheme    = R.scheme;  
   T.authority = R.authority;  
   T.path      = remove_dot_segments(R.path);  
   T.query     = R.query;  
else  
   if defined(R.authority) then  
      T.authority = R.authority;  
      T.path      = remove_dot_segments(R.path);  
      T.query     = R.query;  
   else  
      if (R.path == "") then  
         T.path = Base.path;  
         if defined(R.query) then  
            T.query = R.query;  
         else  
            T.query = Base.query;  
         endif;  
      else  
         if (R.path starts-with "/") then  
            T.path = remove_dot_segments(R.path);  
         else  
            T.path = merge(Base.path, R.path);  
            T.path = remove_dot_segments(T.path);  
         endif;  
         T.query = R.query;  
      endif;  
      T.authority = Base.authority;  
   endif;  
   T.scheme = Base.scheme;  
endif;  
  
T.fragment = R.fragment;  
```  
  
In fact, Boost.URL already implemented this logic, but you found an edge case that was missing. It only happens when the ref has no scheme, no authority, the path is empty, the query is not empty, the fragment is empty, and the target fragment is not empty. So a quite edgy edge case.

---

## Comment 5

> Username: xokdvium  
> Created at: 2025-08-26 21:13:26 UTC  
> Url: https://github.com/boostorg/url/issues/920#issuecomment-3225751742  

Thanks! I was looking at the code and came up with a similar patch locally as well. Thanks for the help!
