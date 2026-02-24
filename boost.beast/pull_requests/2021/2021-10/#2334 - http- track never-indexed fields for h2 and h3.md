# #2334 http: track never-indexed fields for h2 and h3 [Closed]

> Username: cbodley  
> Created at: 2021-10-25 19:48:38 UTC  
> Updated at: 2021-10-26 20:24:27 UTC  
> Closed at: 2021-10-26 20:24:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2334  

an additional requirement for fields in HTTP/2 and 3 is the handling of  
'never-indexed' fields. when sending a given field, the client or server  
can request that its name and value not be cached by header compression,  
citing various security concerns. this is specified for h2 in the HPACK  
RFC 7541 [1], and for h3 in the QPACK draft [2]. both versions also  
require that proxies preserve this flag over all hops  
  
this flag is ignored by the http/1.1 serializer  
  
encoders for h2 and h3 can use value_type::never_index() to determine  
whether a given field can be indexed, and decoders can provide this  
flag for each field as an additional argument to insert()  
  
[1] https://datatracker.ietf.org/doc/html/rfc7541#section-6.2.3  
[2] https://www.ietf.org/archive/id/draft-ietf-quic-qpack-21.html#name-never-indexed-literals  
  
TODO:  
- [x] unit tests for insert, set, and set with overwrite

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-10-25 21:21:07 UTC  
> Updated_at: 2021-10-25 22:10:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-951341793  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2334](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8736a85) into [develop](https://codecov.io/gh/boostorg/beast/commit/b15a5ff0e47e72ba3d4711d2514bc65749d036ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15a5ff) will **increase** coverage by `0.00%`.  
> The diff coverage is `96.66%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2334/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2334   +/-   ##  
========================================  
  Coverage    94.88%   94.89%             
========================================  
  Files          151      151             
  Lines        12230    12247   +17       
========================================  
+ Hits         11605    11622   +17       
  Misses         625      625             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2334/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.47% <96.15%> (+0.07%)` | :arrow_up: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2334/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b15a5ff...8736a85](https://codecov.io/gh/boostorg/beast/pull/2334?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-10-26 02:34:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-951502170  

This feels like it belongs in a different layer. The message is not supposed to know anything about the underlying transport

---

## Comment 3

> Username: cbodley  
> Created_at: 2021-10-26 16:23:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-952105478  

hi @vinniefalco, thanks for taking a look!  
  
as this never-indexed flag is called out under the Security Considerations of both HPACK/QPACK documents linked above, i think it's important that implementations allow client and server applications direct control over which fields they consider sensitive  
  
this *could* just mean they provide a list of field names that should never be indexed by any request, and associate that list either with a given transport endpoint, or a specific connection. this would not require any additional support from the http message or its fields, so i certainly understand your sentiment about layering  
  
however, [this additional requirement on http proxies](https://www.ietf.org/archive/id/draft-ietf-quic-qpack-21.html#name-never-indexed-literals) implies that the http message being proxied does, itself, need to remember this flag for each of its fields:  
  
> Refusing to insert a field line into the dynamic table is only effective if doing so is avoided on all hops. The never-indexed literal bit (see Section 4.5.4) can be used to signal to intermediaries that a particular value was intentionally sent as a literal.  
>  
> An intermediary MUST NOT re-encode a value that uses a literal representation with the 'N' bit set with another representation that would index it. If QPACK is used for re-encoding, a literal representation with the 'N' bit set MUST be used. If HPACK is used for re-encoding, the never-indexed literal representation (see Section 6.2.3 of [RFC7541]) MUST be used.  
  
so i believe that these changes are necessary in order to build a compliant http proxy using `beast::http::fields`  
  
for context, i'm prototyping a quic/h3 library for asio in https://github.com/cbodley/nexus, and would love to use beast's message types in all cases - but had to create a duplicate [h3::fields](https://github.com/cbodley/nexus/blob/master/include/nexus/h3/fields.hpp) that can support this one  
  
what do you think?  
  
p.s. i very much appreciate your efforts to push the networking ts forward!

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-10-26 16:45:51 UTC  
> Updated_at: 2021-10-26 16:46:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-952122694  

> what do you think?  
  
I think that Beast was a very nice first draft of what an Asio-based HTTP/Websocket library might look like. I am mostly satisfied with the websocket part, but I am not at all satisfied with the HTTP part. I think some of the fundamental design choices for the HTTP message model were suboptimal. For example, trying to standardize a type that combines a generic body with a generic header, constrains implementations in ways that make certain use-cases difficult.  
  
I'm working on a new library called http-proto which makes different design choices. For example, the new parser is stateful, non-templated, and lasts for the lifetime of the connection. It has its own owning-buffer, and it returns a "view" to the headers:  
  
https://github.com/vinniefalco/http_proto/blob/02bac50a752828f76f9dc2b9b11c3539f4028033/include/boost/http_proto/headers_view.hpp#L29  
  
No more hash table or separate memory allocation per header field. It is just a view over the existing serialized message. Storing "bits" there is going to be problematic. If you want to have a mutable container, you can just copy the fields in the view into your own container, and add whatever stuff you want. It is no more expensive than Beast's current implementation, which already copies the fields except it doesn't offer the header view and it forces the fields container to conform to some overly complex API (the _Fields_ concept).  
  
> p.s. i very much appreciate your efforts to push the networking ts forward!  
  
Thanks! However, the Christopher Kohlhoff vision of Net.TS has been effectively killed off by WG21. Eric Niebler, Kirk Shoop, Lewis Baker, and nVIDIA are now in control of what networking will look like in the C++ standard. And it will not look like Asio or have its _CompletionToken_ model.  
  
Thus, I am no longer expending my energy in committee activities, preferring instead to focus my work on Boost.Asio which will remain an external library offering with the performance benefits and design benefits it brings.

---

## Comment 5

> Username: cbodley  
> Created_at: 2021-10-26 17:12:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-952143519  

> Thanks! However, the Christopher Kohlhoff vision of Net.TS has been effectively killed off by WG21. Eric Niebler, Kirk Shoop, Lewis Baker, and nVIDIA are now in control of what networking will look like in the C++ standard. And it will not look like Asio or have its _CompletionToken_ model.  
>   
> Thus, I am no longer expending my energy in committee activities, preferring instead to focus my work on Boost.Asio which will remain an external library offering with the performance benefits and design benefits it brings.  
  
the delays have certainly been frustrating. but it has been nice to see recent improvements in asio, like the per-op cancelation and deferred handlers, that take some of the good ideas from this standardization process. to me, that's a testament to the flexbility of its async model

---

## Comment 6

> Username: cbodley  
> Created_at: 2021-10-26 17:56:42 UTC  
> Updated_at: 2021-10-26 18:29:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-952176563  

> (snip)  
>   
> No more hash table or separate memory allocation per header field. It is just a view over the existing serialized message. Storing "bits" there is going to be problematic. If you want to have a mutable container, you can just copy the fields in the view into your own container, and add whatever stuff you want. It is no more expensive than Beast's current implementation, which already copies the fields except it doesn't offer the header view and it forces the fields container to conform to some overly complex API (the _Fields_ concept).  
  
interesting, thanks for sharing. that sounds like a very natural optimization for parsing and, when reading from the http/1 transport, i wouldn't expect it to store any extra bits  
  
do you imagine using a similar strategy for the serialization case, with the application writing fields directly into the buffer? or would that still prefer a container like the existing `http::fields`? it seems like `http::fields` with a custom allocator would behave just as well there  
  
thinking about h2 and h3, their HEADER frame decoders could copy the decoded headers into a single buffer to be exposed by a view type, and could arrange for this extra never-index flag's storage. but i'm guessing your `header_view` is relying on the http/1 representation, and searching for `:`s and `\r\n`s to delimit the field names and values? can you think of any way this could be generalized?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-10-26 19:56:14 UTC  
> Updated_at: 2021-10-26 19:56:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-952270437  

> do you imagine using a similar strategy for the serialization case, with the application writing fields directly into the buffer?   
  
Yes. A mutable container for HTTP messages could store the message in its serialized form. The headers would thus resemble a _ForwardRange_ of `pair<string_view, string_view>`. I have done exactly that here:  
  
https://github.com/vinniefalco/http_proto/blob/02bac50a752828f76f9dc2b9b11c3539f4028033/include/boost/http_proto/headers.hpp  
  
> or would that still prefer a container like the existing `http::fields`?  
> it seems like `http::fields` with a custom allocator would behave just as well there  
  
`http::fields` allocates memory for each key/value pair and it uses a node-based approach. While the new container stores the entire message in its serialized form.  Most of the time, outgoing HTTP messages are composed by appending each field one at a time. This can be implemented as appending to a notional `std::string`, inserting the necessary delimiters to make the string correctly serialized. The case where fields are inserted into the middle, erased, or replaced with different-sized values does require `memmove` but the caller can mitigate this with some structuring. And I believe these cases are rare enough that it is a net win. Of course, it will need to be measured.  
  
>  i'm guessing your `header_view` is relying on the http/1 representation, and searching for `:`s and `\r\n`s to delimit the field names and values?  
  
Yes exactly.  
  
> can you think of any way this could be generalized?  
  
I can :) A _ForwardRange_ whose iterator's value type is convertible to this:  
  
```  
struct http_field_value  
{  
  field id;  
  string_view name;  
  string_view value;  
};  
```

---

## Comment 8

> Username: cbodley  
> Created_at: 2021-10-26 20:24:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2334#issuecomment-952295797  

thanks a lot for the illuminating discussion, i'll be sure to follow your progress there  
  
i'll go ahead and close this one

---
