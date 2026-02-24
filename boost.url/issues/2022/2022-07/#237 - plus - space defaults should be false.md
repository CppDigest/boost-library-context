# #237 - plus / space defaults should be false [Closed]

> Username: alandefreitas  
> Created at: 2022-07-19 21:00:11 UTC  
> Updated at: 2022-10-24 19:43:38 UTC  
> Closed at: 2022-10-24 19:43:38 UTC  
> Url: https://github.com/boostorg/url/issues/237  

https://stackoverflow.com/questions/2678551/when-should-space-be-encoded-to-plus-or-20

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-14 00:41:37 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1246092706  

I think we handle it well

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-09-14 06:13:26 UTC  
> Updated at: 2022-09-14 06:13:51 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1246290021  

I find the default values very problematic  
  
https://github.com/boostorg/url/blob/5ff23a35045fe948cfad8f305aef63a8b785bcac/include/boost/url/decode_opts.hpp#L34  
  
https://github.com/boostorg/url/blob/5ff23a35045fe948cfad8f305aef63a8b785bcac/include/boost/url/encode_opts.hpp#L35

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-14 15:27:10 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1246938964  

Well what does "application/x-www-form-urlencoded" expect?

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-09-14 16:03:11 UTC  
> Updated at: 2022-09-14 16:05:43 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1246985426  

According to the [ref](https://stackoverflow.com/questions/2678551/when-should-space-be-encoded-to-plus-or-20) above  
  
> `+` means a space _only_ in application/x-www-form-urlencoded content, such as the query part of a URL:  
>   
> http://www.example.com/path/foo+bar/path?query+name=query+value  
  
In any other context, or even other URL components in the same context:  
  
- `+` does not mean space  
- `+` is ambiguous (`+` might mean `+` or space) and round trips don't work  
  
If we make this specific context of application/x-www-form-urlencoded the default, we are then choosing a default that is invalid according to the RFC, invalid for most contexts, and invalid to other URL components.  
  
> In this URL, the parameter name is query name with a space and the value is query value with a space, but the folder name in the path is literally foo+bar, not foo bar.  
  
And even if we make application/x-www-form-urlencoded the default, we would need to treat the ambiguities. One way to do that might be writing any real `+` as `%2B` whenever `bool space_to_plus = true;` regardless of the charset.  
  
The other problem is ` bool plus_to_space = true; ` and `bool space_to_plus = false; `, which should be one or the other.  
  
And even in application/x-www-form-urlencoded, `+` to space might not be really recommended:  
  
> %20 is a valid way to encode a space in either of these contexts. So if you need to URL-encode a string for inclusion in part of a URL, it is always safe to replace spaces with %20 and pluses with %2B. This is what, e.g., encodeURIComponent() does in JavaScript. Unfortunately it's not what [urlencode](http://php.net/manual/en/function.urlencode.php) does in PHP ([rawurlencode](http://php.net/manual/en/function.rawurlencode.php) is safer).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-16 04:54:26 UTC  
> Updated at: 2022-09-16 04:54:36 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1248918053  

what do you want to set them to? sounds like you want both to be false?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-09-16 05:10:22 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1248925241  

Yes. `false`

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-16 15:15:44 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249491751  

@pdimov was the driving force behind the current defaults, what do you think?

---

## Comment 8

> Username: pdimov  
> Created at: 2022-09-16 17:00:31 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249587555  

Is an unencoded `+` allowed in a URL?

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-09-16 17:01:15 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249588167  

Yes

---

## Comment 10

> Username: pdimov  
> Created at: 2022-09-16 17:03:47 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249590099  

Then I suppose the default should be `false`. I don't remember why it isn't.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-09-16 17:35:54 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249618058  

I believe that the encoding option is false, because %20 is better than +. The decoding option is true, because you get reasonable behavior out of the box - our research indicating that because of javascript, the use of plus in queries to represent a space is pervasive.

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-09-16 17:41:34 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249623998  

All that discussion about reasonable behavior and common use assume only HTTP/`application/x-www-form-urlencoded` URLs  exist and only queries will ever have `+`s.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-09-16 17:43:14 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249626362  

Sure. But... we designed the library so that 99% of users don't need to touch percent encoding and decoding options or algorithms. Correct encoding and decoding is automatically performed, and we even convert plus to space by default in the query and parameters. Out of the people who do need to use the decoding/encoding algorithms I would expect that most of them are doing so for form-urlencoded. But I could be wrong.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-09-16 17:53:02 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249643935  

@alandefreitas up to you if you want to change it, I don't feel strongly either way

---

## Comment 15

> Username: pdimov  
> Created at: 2022-09-16 18:12:55 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249664093  

I feel like I'm not quite understanding the repercussions of this change. Is it true that (today) when we encode a URL, wherever `+` is allowed, it will stay `+`, and it will turn to %2B in the query part? And that when we decode a URL, `+` in the non-query parts will stay `+`, but `+` in the query part will decode to space?  
  
Isn't this what we want?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2022-09-16 18:30:32 UTC  
> Updated at: 2022-09-16 18:30:46 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249679771  

The library sets the options internally to their own values.  
  
When encoding from the user's plain strings:  
https://github.com/boostorg/url/blob/08b66fd1356816869cb8b4a6ec4d34fb3a6c7c32/include/boost/url/detail/impl/any_params_iter.ipp#L164  
  
When decoding from a valid URL:  
https://github.com/boostorg/url/blob/08b66fd1356816869cb8b4a6ec4d34fb3a6c7c32/include/boost/url/url_view_base.hpp#L1891  
  
The library always explicitly sets these options so that they are not affected by defaults.  
  
The default settings for `encode_opts` and `decode_opts` will be meaningful when the user calls the percent encoding and decoding algorithms directly, or if they declare their own struct to override the library's behavior. For example if they don't want plus to space in the query they might write:  
```  
decode_opts opt;  
opt.plus_to_space = false;  
std::string s = u.encoded_query().decode( opt );  
```  
  
If they want to use a string token they have to pass an opt (because `decode` takes two parameters). In this case they will be affected by the default:  
  
```  
std::string s;  
decode_opts opt;  
u.encoded_query().decode( opt, string_token::assign_to( s ) );  
```

---

## Comment 17

> Username: alandefreitas  
> Created at: 2022-09-16 18:39:44 UTC  
> Updated at: 2022-09-16 18:40:40 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249688722  

> we designed the library so that 99% of users don't need to touch percent encoding and decoding options or algorithms  
  
Right. Users would need to change the options all the time just to avoid invalid output in case of a potential `+`. For instance:  
  
```cpp  
assert( decode("path/to/my+file.txt") == "path/to/my file.txt" );  
```  
  
which is just wrong. Even in `application/x-www-form-urlencoded`.  
  
If the default is `false`, the user might find `%20` less convenient, but they will never have to adjust `decode_opts` just to ensure the result is not invalid.  
  
> Out of the people who do need to use the decoding/encoding algorithms I would expect that most of them are doing so for `form-urlencoded`. But I could be wrong.  
  
Even if most are decoding for `form-urlencoded`, that doesn't mean they are decoding only for `form-urlencoded` queries. This would be a very weird use case.  
  
The default for `decode_opt` doesn't have to be the default for `params`. `url_view_base` has its own default charsets and params for each component.   
  
In practice, we are mostly discussing the default behavior when decoding strings.  
  
> Is it true that (today) when we encode a URL, wherever + is allowed, it will stay +, and it will turn to %2B in the query part?   
  
The rationale is correct, `+` to space can only be unambiguous if `+` is not in the allowed charset. But unfortunately, that's not what happens now. We currently use the charset `pchars + '/' + '?'` to set the query. `pchars` includes `subdelim_chars`, which includes `+`.   
  
So any `+` in the original input will not survive a round-trip. In this aspect, we can choose to follow the URL protocol (allow `+`, `plus_to_space = false`) or the HTML protocol + common practice (any original `+` is encoded, any original space becomes `+`). It's impossible to satisfy both.  
  
This is one question, but that's not a big problem, because each component observer has its own defaults. So the default `decode_opts` doesn't affect the query observers. In practice, the default `decode_opts` will only be used for the `decode` function, where we have no idea whether that string is from a query let alone a `form-urlencoded` query. That's where the default is not useful.  
  
> And that when we decode a URL, + in the non-query parts will stay +, but + in the query part will decode to space? Isn't this what we want?  
  
That is true in the current implementation. But that is orthogonal to the default `decode_opts` because each component has its own defaults. So this would work either way. The problem is the effect of the default `decode_opts` when decoding arbitrary strings. I don't think we should assume an arbitrary string is neither a query nor a `form-urlencoded` query.  
  
  
  
  
  
  
So the problem is that we're not deciding between (a) a convenience or (b) an inconvenient default.  The problem is that we're not deciding between (a) `%20` instead of `+` by default for arbitrary strings or (b) a default that generates invalid output, is only applicable to queries, is only applicable to `application/x-www-form-urlencoded`, is ambiguous even in queries unless we adjust the charset, is not part of the URL protocol, doesn't allow round-trips, and is currently impossible for the user to fix because `url_view_base::query` has no `decode_opt`.

---

## Comment 18

> Username: pdimov  
> Created at: 2022-09-16 18:52:11 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249699435  

> The problem is that we're not deciding between (a) `%20` instead of `+` by default for arbitrary strings  
  
I'm afraid I'm still missing something. `space_to_plus` is `false`: https://github.com/boostorg/url/blob/5ff23a35045fe948cfad8f305aef63a8b785bcac/include/boost/url/encode_opts.hpp#L35  
  
So space is always encoded to %20 by default and there's no debate over it. The question is whether we want to change `plus_to_space` to `false`.  
  
If I understand correctly, this will only affect people who call the decoding functions directly, right? Any uses that go through the `url` accessors will be unaffected?

---

## Comment 19

> Username: vinniefalco  
> Created at: 2022-09-16 18:56:22 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249702942  

> If I understand correctly, this will only affect people who call the decoding functions directly, right?  
  
This will affect anyone who default-constructs an options structure and passes it to a library function. Basically yes, to your question.

---

## Comment 20

> Username: alandefreitas  
> Created at: 2022-09-16 19:14:15 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1249716340  

> If I understand correctly, this will only affect people who call the decoding functions directly, right? Any uses that go through the url accessors will be unaffected?  
  
Exactly. This is the default for "arbitrary" strings. Not for `url::params`.

---

## Comment 21

> Username: alandefreitas  
> Created at: 2022-10-11 00:23:34 UTC  
> Url: https://github.com/boostorg/url/issues/237#issuecomment-1273938056  

We should do what javascript does:  
  
- set_query follows rfc3986  
- params follow application/x-www-form-urlencoded: http://www.w3.org/TR/REC-html40/interact/forms.html#form-content-type
