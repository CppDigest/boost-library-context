# #303 - optional<string_view> ? [Closed]

> Username: vinniefalco  
> Created at: 2022-08-02 20:05:04 UTC  
> Updated at: 2022-09-03 20:11:42 UTC  
> Closed at: 2022-09-03 20:11:42 UTC  
> Url: https://github.com/boostorg/url/issues/303  

We might consider returning `optional<string_view>` and `optional<pct_encoded_view>` from `url_view` functions returning componetns which are optionally present (e.g. userinfo).

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-03 00:49:22 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1203359040  

So we ask Peter?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-03 00:59:32 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1203364778  

It sounds inconvenient when we know or don't care if the thing exists:  
  
```cpp  
auto h = u.host();  
```  
  
becomes  
  
```cpp  
auto hr = u.host();  
pct_encoded_view h;  
if (hr)  
    h = hr.value();     
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-03 01:17:46 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1203375000  

Well `host` is one of those things that always exists.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-03 01:23:40 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1203378100  

Not always. It's always an empty placeholder when `!u.has_authority()`. Anyway, we also have `has_scheme()`, etc... which could be just as inconvenient.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-08-03 02:24:24 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1203409584  

What is the difference between  
```  
if( u.has_scheme() )  
    f( u.scheme() );  
```  
and  
```  
if( u.scheme().has_value() )  
  f( *u.scheme() );  
```  
?  
  
Now I'm not arguing for changing anything (yet) but it seems to me like the same convenience. The different is that with optional I can write  
  
```  
void do_something( optional<string_view> userinfo );  
```  
  
instead of  
  
```  
void do_something( bool has_userinfo, string_view userinfo );  
```

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-08-03 19:02:56 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1204361191  

The difference is when an empty string is an acceptable replacement for a missing component in that application. Especially if the user knows where the URL is coming from.  
  
```cpp  
str = u.scheme(); // the user expects `scheme` to exist 99.99999% of the time because `u` was parsed with `parse_uri`, but "" won't break anything nonetheless  
```  
  
```cpp  
str = *u.scheme(); // Maybe BOOM 100% - 99.99999% of the time  
```  
  
Because it's always _maybe_ a BOOM :boom:, the user has to use the longer form all the time:  
  
```cpp  
if( u.scheme().has_value() )  
     f( *u.scheme() );  
```  
  
So it ends up being `f(u.scheme());` vs. `if( u.scheme().has_value() ) f( *u.scheme() ); else f({});` in that case.  
  
To be fair, the best pattern, in that case, would be  
  
```cpp  
f(u.scheme().value_or({}));  
```  
  
but I'm not sure many people would do that.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-08-03 19:33:07 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1204389521  

Scheme can't be empty, so `! u.scheme().empty()` is just as good as `u.has_scheme()`. Furthermore we can make calling `scheme()` well-defined behavior by simply mentioning in the javadoc that if there is no scheme, the return value is empty (that might even be there already). Also note they can write `u.scheme_id() != scheme::none`. Lots of ways to feed a cat.

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-08-03 23:35:52 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1204583867  

> ! u.scheme().empty() is just as good as u.has_scheme()  
  
`! u.scheme().empty()` implies it could be empty.  
  
> mentioning in the javadoc that if there is no scheme, the return value is empty  
  
That's basically what we have now. No optional.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-08-04 02:59:41 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1204699818  

Yeah. Well, we don't have enough information so we should do the least amount of work. And right now that means doing nothing. If the reviewers feel strongly about `optional` arguments, we can revisit. For now we leave this open.

---

## Comment 10

> Username: alandefreitas  
> Created at: 2022-08-05 01:30:29 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1205941692  

I've been thinking about this. In particular why I thought it was a good idea to use optional in the magnet link example. And I guess the reason is that most URL components have a position/offset on the string, even if their size is zero.   
  
For instance, when `has_query()` differentiates two kinds of empty strings, these are empty strings just because we are removing the "?" prefix to make things nicer or more useful. However, in both cases, the query has a particular valid position on the string, even if it's an empty substring or at the "?" prefix we are removing.  
  
That is different from say `params` returning `""` for elements that don't exist. They don't exist and there's no concept of position for them. The same is valid for magnet links.  
  
I guess that's the implicit rationale that made me comfortable with the API from the first time I saw it.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-09-03 20:11:42 UTC  
> Url: https://github.com/boostorg/url/issues/303#issuecomment-1236192165  

This seems worse to me because you don't have the option of accepting an empty string in lieu of a disengaged optional.
