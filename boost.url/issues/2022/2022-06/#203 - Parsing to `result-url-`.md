# #203 - Parsing to `result<url>` [Closed]

> Username: akrzemi1  
> Created at: 2022-06-16 20:04:47 UTC  
> Updated at: 2022-07-31 08:17:35 UTC  
> Closed at: 2022-07-31 08:16:53 UTC  
> Url: https://github.com/boostorg/url/issues/203  

This is to report an unsatisfied expectation; not necessarily a bug.  
  
Because the following examples work:  
  
```c++  
result<url_view> u1 = parse_uri(s);  
url_view u2 = parse_uri(s).value();  
url u3 = parse_uri(s).value();  
```  
I naturally expected that the following would also work:  
  
```c++  
result<url> u4 = parse_uri(s); // result<url>  
```  
  
But it doesn't. But wouldn't that be a frequent use case? I want the string to be validated, and if it works, have it represented in a `url` container. I can of course do it in two lines.  
  
The example with `u4` would work if `boost::system::result<T>` were convertible to `boost::system::result<U>`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-16 20:44:02 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158112383  

Mmmm... I never thought about that.   
  
We have   
  
```cpp  
url u4 = parse_uri(s).value();  
```  
  
but not  
  
```cpp  
result<url> u4 = parse_uri(s);  
```  
  
But when we think about it, `url u4 = parse_uri(s).value();` is kind of useless because it cancels either the easy conversion or the error checking.  
  
I had never seen this comment before but we had a similar comment on the ML suggesting something like `from_chars`:  
  
```cpp  
result<url_view> u1;  
parse_uri(s, u1);  
result<url> u2;  
parse_uri(s, u2);  
url_view u3;  
parse_uri(s, u3);  
url u4;  
parse_uri(s, u4);  
```  
  
where `parse_uri(s, u3);` and `parse_uri(s, u4);` might throw. It's a little more verbose but not so much.  
  
I can't see any other way to improve that unless we implement another `result` type just to make `result<url_view>` implicitly convertible to `result<url>` (or convince Peter to include that functionality in `result` somehow). I don't think this kind of implicit conversion is really desirable.  
  
If `result<T>` was also convertible to `T`, throwing on error, we could even have:  
  
```cpp  
result<url_view> u1 = parse_uri(s);  
result<url> u2 = parse_uri(s);  
url_view u3 = parse_uri(s);  
url u4 = parse_uri(s);  
```  
  
but again, that doesn't look very safe.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 21:04:04 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158129104  

I prefer two lines, because it makes the construction of the expensive object very explicit.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-06-16 22:33:03 UTC  
> Updated at: 2022-06-16 22:33:24 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158204831  

I found the e-mail where this came up:  
  
https://lists.boost.org/Archives/boost//2021/10/252046.php  
  
I think there's no suggestion of   
  
```cpp  
result<url_view> u;  
parse_uri(s, u);  
```  
  
in there. I guess that was my idea and I thought it was someone else's.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-06-16 22:55:50 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158239825  

https://github.com/boostorg/system/issues/82 might be relevant here.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-06-16 23:48:07 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158283834  

There would be also the problem of the person how's used to writing  
  
```cpp  
result<url> u = parse_uri(s);  
```  
  
, starts to think `u` owns the string, and one day goes  
  
```cpp  
auto u = parse_uri(s);  
return u;  
```  
  
Implicit conversions would either make `url_view` convertible to `url`, in which case `auto u` would be misleading, or make parse_uri always return `result<url>`, which would be wasteful.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-17 03:48:25 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158461132  

When I designed `url_view` and `url` it was my intention that the construction of `url` from `url_view` would be visible and deliberate, since it allocates memory and creates a copy. That's why none of the parsing algorithms return a `url` directly.   
  
If you want to parse a URI directly into a `url` you can use this constructor:  
  
https://master.url.cpp.al/url/ref/boost__urls__url/url/overload5.html  
  
My thinking with this constructor is that if you already don't care about using exceptions to indicate a parsing error, then you probably won't mind the potentially needless allocation and copy.

---

## Comment 7

> Username: akrzemi1  
> Created at: 2022-06-17 08:20:33 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158624044  

The idea to make every resource allocation explicit (like when creating a `url`) looks sound to me, but it is inconsistent with the decision to provide a converting constructor from `url_view` to `url`.  
  
> If you want to parse a URI directly into a url you can use this constructor:  
> https://master.url.cpp.al/url/ref/boost__urls__url/url/overload5.html  
  
First, is this synopsis correct? Can't the constructor also throw `std::length_error` on valid but excessively long inputs?   
  
Second, can I offer a suggestion not to make this a constructor, but instead a static factory function? In the spirit of "single responsibility principle", it should not be the business of `url` container class how to parse inputs. You could give it a name (or a set of names) that would clearly indicate how it interprets the string input. You could also change the return type from `url` to `result<url>`. Of course then, it would make the function so close to `parse_uri()` that one might start to wonder if it is really needed.  
  
Third, the use case that I presented is this: first validate the input without throwing exceptions (because I want a normal control flow) and then give me the `url` container. It is not that I "don't care about using exceptions", it is because I need an explicit control path for my use case.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-06-17 13:06:15 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158852601  

> Can't the constructor also throw std::length_error on valid but excessively long inputs?  
  
Yeah, that needs to be added @alandefreitas   
  
> ...make this a constructor, but instead a static factory function? In the spirit of "single responsibility principle"...  
  
Yeah I totally see your point and this was my thinking originally. BUT... the "Ramey Rule" trumps SRP. When I started writing documentation and examples it became clear that this constructor was useful. Especially when you compare it to example code from other URL libraries. If I remove the constructor I would rather have nothing instead of factory functions. Because then we would be lacking a way to construct `static_url`:  
  
https://master.url.cpp.al/url/ref/boost__urls__static_url/static_url/overload5.html  
  
> Third, the use case that I presented is this: first validate the input without throwing exceptions (because I want a normal control flow) and then give me the url container. It is not that I "don't care about using exceptions", it is because I need an explicit control path for my use case.  
  
Yes for this use case, use two lines.

---

## Comment 9

> Username: akrzemi1  
> Created at: 2022-06-17 14:16:24 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1158916706  

> Yeah I totally see your point and this was my thinking originally. BUT... the "Ramey Rule" trumps SRP. When I started writing documentation and examples it became clear that this constructor was useful. Especially when you compare it to example code from other URL libraries. If I remove the constructor I would rather have nothing instead of factory functions. Because then we would be lacking a way to construct `static_url`:  
  
Does this mean that for platforms with exceptions disabled there is no way to validate-and-construct a `static_url`?  
  
Anyway, what is "Ramey Rule"?

---

## Comment 10

> Username: alandefreitas  
> Created at: 2022-06-17 16:49:21 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159060430  

> Does this mean that for platforms with exceptions disabled there is no way to validate-and-construct a static_url?  
  
It's the same case with had with `url`:  
  
```cpp  
result<url_view> uv = parse_uri(str);  
static_url<120> u;  
if (uv.has_value())  
    u = uv.value();  
else  
    handle(ev.error());  
```  
  
Considering all possible cases, the explicit API is actually less verbose than `result`:  
  
```cpp  
static_url<120> uv;  
if (auto ec = parse_uri(str, uv))  
    handle(ec);  
```

---

## Comment 11

> Username: pdimov  
> Created at: 2022-06-17 17:05:58 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159074904  

> When I designed `url_view` and `url` it was my intention that the construction of `url` from `url_view` would be visible and deliberate, since it allocates memory and creates a copy.  
  
Why are the `url` constructors from `url_view` and `string_view` implicit then?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-06-17 19:12:31 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159161044  

> Why are the url constructors from url_view and string_view implicit then?  
  
Should they not be? This constructs `url_view`  
  
```  
auto u = parse_uri( s ).value();  
```  
  
while this is explicit conversion  
  
```  
url u = parse_uri( s ).value();  
```  
  
because the type is named, and thus visible. Or am I looking at it wrong?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-06-17 19:14:26 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159162150  

> ...what is "Ramey Rule"?  
  
It is the phenomenon of discovering, during the process of writing documentation, that your API is suboptimal: https://twitter.com/FalcoVinnie/status/1451925517381222403

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-06-17 19:15:30 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159162793  

This signature is sort of intersting:  
```  
bool parse_uri( string_view, url& dest, error_code& );  
```

---

## Comment 15

> Username: pdimov  
> Created at: 2022-06-17 20:10:46 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159196631  

People who care about not "invisibly" allocating generally use explicit constructors, because  
```  
void f( url const& u );  
  
int main()  
{  
    f( "http://" );  
}  
```

---

## Comment 16

> Username: vinniefalco  
> Created at: 2022-06-17 21:16:51 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159231948  

I didn't think of that scenario. But in this case the signature for `f` should be `url_view` not `url const&`

---

## Comment 17

> Username: akrzemi1  
> Created at: 2022-06-18 08:12:48 UTC  
> Updated at: 2022-06-18 08:13:48 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159394033  

So, after the Ramey Rule kicked in, do the library design goals need adjustment?  Does the "keep every resource allocation explicit" rule still apply?  
  
Apparently, there is a friction between convenience and explicitness.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2022-06-18 13:42:26 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159467505  

Well nothing is set in stone yet. In terms of this particular it is solved with `result<T>` converting to `result<U>`. Besides that, I think the current interfaces are reasonable.

---

## Comment 19

> Username: alandefreitas  
> Created at: 2022-06-18 21:22:52 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159566596  

> Why are the url constructors from url_view and string_view implicit then?  
  
Mmmm...  
  
 ```cpp  
auto u = parse_uri( s ).value();  
```  
  
is nice, but not visible and deliberate.  
  
>  This signature is sort of interesting:  
> bool parse_uri( string_view, url& dest, error_code& );  
  
I guess it can't get much better than this. `bool` and `error_code` look redundant though.  
  
> But in this case the signature for f should be url_view not url const&  
  
The user is not always in control of the signature. At least, that's what justified many decisions in this library so far.  
  
> Apparently, there is a friction between convenience and explicitness  
  
True. Although  
  
```cpp  
static_url<120> uv;  
if (auto ec = parse_uri(str, uv))  
    handle(ec);  
```  
  
seems more explicit _and_ more convenient than  
  
```cpp  
result<url_view> uv = parse_uri(str);  
static_url<120> u;  
if (uv.has_value())  
    u = uv.value();  
else  
    handle(ev.error());  
```  
  
> In terms of this particular it is solved with result<T> converting to result<U>  
  
It doesn't solve the problem with  
  
```cpp  
auto u = parse_uri(str);  
return u; // BOOM  
```

---

## Comment 20

> Username: pdimov  
> Created at: 2022-06-18 21:54:28 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159571083  

Well the point of using `result` is to be able to write this:  
```  
static_url<120> u = parse_uri(str).value();  
```  
If you switch to returning `ec` you can no longer express a throwing overload because the arguments are the same in both cases.  
  
The nonthrowing case with `result` looks more like  
```  
auto r = parse_uri(str);  
if( !r ) return handle_error( r.error() );  
static_url<120> u = *r;  
```

---

## Comment 21

> Username: alandefreitas  
> Created at: 2022-06-18 22:08:40 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159573025  

That makes sense.   
  
```cpp  
auto r = parse_uri(str);  
if( !r ) return handle_error( r.error() );  
static_url<120> u = *r;  
```  
  
is much better.  
  
I guess the only problem then is   
  
```cpp  
auto r = parse_uri(str);  
return r; // BOOM  
```  
  
which would probably be solved with `result::operator result<U>`. (?)

---

## Comment 22

> Username: pdimov  
> Created at: 2022-06-18 22:14:43 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159574048  

The boom depends on the return type of the function; if it's declared to return `result<url_view>`, and `str` is local, then yes. But if it returns `result<url>`, then no.

---

## Comment 23

> Username: alandefreitas  
> Created at: 2022-06-18 22:24:15 UTC  
> Updated at: 2022-06-19 23:35:05 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159575120  

It should be safe then. If the user declares the return type as auto, then it’s like they're asking for the boom.

---

## Comment 24

> Username: pdimov  
> Created at: 2022-06-18 23:02:36 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159578941  

The converting `result` constructor is now on develop.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2022-06-19 14:46:39 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1159745971  

> `if (auto ec = parse_uri(str, uv))`  
  
I wish _you people_ would stop writing this, because it irks me greatly. I'm already uncomfortable with the second style of returning errors that we have introduced (`result<T>`). And now you are introducing a third style (using the return value instead of an out-param).

---

## Comment 26

> Username: vinniefalco  
> Created at: 2022-07-31 03:28:41 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1200340336  

It looks like nothing is actionable here. With respect to "boom": this is C++ and users are expected to understand non-owning references whether they are expressed syntactically like `T&` and `T const&` or whether they are expressed semantically like `string_view` or even `char const*`. Dangling references is not a universally preventable category of error without turning it into something that is no longer C++.  
  
 Can we close this issue?

---

## Comment 27

> Username: akrzemi1  
> Created at: 2022-07-31 08:16:53 UTC  
> Updated at: 2022-07-31 08:17:35 UTC  
> Url: https://github.com/boostorg/url/issues/203#issuecomment-1200375078  

Actually, Peter solved the problem reported in the beginning of this thread, in Boost.System, by adding a converting constructor to `result<>`: [https://www.boost.org/doc/libs/1_80_0_beta1/libs/system/doc/html/system.html#changes_in_boost_1_80](https://www.boost.org/doc/libs/1_80_0_beta1/libs/system/doc/html/system.html#changes_in_boost_1_80).  
  
The issue is resolved.
