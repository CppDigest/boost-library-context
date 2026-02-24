# #58 Refactor stream write functionality into a standalone utility [Merged]

> Username: glenfe  
> Created at: 2019-04-19 00:32:14 UTC  
> Updated at: 2019-04-23 10:56:04 UTC  
> Merged at: 2019-04-23 10:56:04 UTC  
> Closed at: 2019-04-23 10:56:04 UTC  
> Url: https://github.com/boostorg/utility/pull/58  

Useful in general (e.g. for `boost::beast::static_string` or `boost::container::basic_string`). Also less maintenance between `boost::basic_string_ref` and `boost::basic_string_view`.

---

## Comment 1

> Username: glenfe  
> Created_at: 2019-04-19 00:34:23 UTC  
> Url: https://github.com/boostorg/utility/pull/58#issuecomment-484732041  

I'll add Quickbook documentation if this is approved in general.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2019-04-19 07:59:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/58#pullrequestreview-228618449  

Since this component is only about writing strings and not arbitrary values, it would be better to reflect that in the name of the header and the main public function.

📁 include/boost/utility/ostream_write.hpp

```diff
  28 |+ {
  29 |+     charT c = os.fill();
  30 |+     charT fill[] = { c, c, c, c, c, c, c, c };
```

> Username: Lastique  
> Created_at: 2019-04-19 07:58:05 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276926519  

This way of initialization is not maintenance friendly. If `chunk` is changed, it's easy to forget to modify the initialization. A loop would be better.

> Username: Lastique  
> Created_at: 2019-04-19 08:01:19 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276927190  

Nevermind, you've changed the code already.

> Username: glenfe  
> Created_at: 2019-04-19 08:02:33 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276927490  

`chunk` shouldn't need to be changed at all, since its value is obtained from `sizeof fill / sizeof *fill`.


---

## Comment 3

> Username: glenfe  
> Created_at: 2019-04-19 08:09:00 UTC  
> Url: https://github.com/boostorg/utility/pull/58#issuecomment-484801716  

> Since this component is only about writing strings and not arbitrary values, it would be better to reflect that in the name of the header and the main public function.  
  
How about `ostream_format` (from _FormattedOutputFunction_)? In case we ever want to have other overloads in the header (for things other than sequences of characters) that would differ only by the latter parameters?

---

## Review 4 [Commented]

> Username: Lastique  
> Created_at: 2019-04-19 08:10:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/58#pullrequestreview-228621473  

📁 include/boost/utility/string_ref.hpp

```diff
  28 | #include <iterator>
  29 | #include <string>
  29 |- #include <iosfwd>
```

> Username: Lastique  
> Created_at: 2019-04-19 08:10:06 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276929011  

`iosfwd` is still needed for `basic_ostream` in `operator<<` declaration.

> Username: glenfe  
> Created_at: 2019-04-19 08:11:24 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276929247  

It is still being included (from `ostream_write.hpp` which is included here).

> Username: Lastique  
> Created_at: 2019-04-19 08:18:31 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276930579  

I don't think relying on transitive includes is correct. My rule of thumb is include everything you directly use in the header.

> Username: glenfe  
> Created_at: 2019-04-19 08:34:50 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276934136  

No objection to employing that rule here. (Done).


---

## Comment 5

> Username: Lastique  
> Created_at: 2019-04-19 08:14:20 UTC  
> Url: https://github.com/boostorg/utility/pull/58#issuecomment-484803437  

> In case we ever want to have other overloads in the header (for things other than sequences of characters) that would differ only by the latter parameters?  
  
I think we should resist the urge to expand functionality of this header. This would increase `string_view` footprint and it should remain as minimal as possible, IMHO. If someone wants a formatting library, one should probably use one, we have a few.

---

## Comment 6

> Username: glenfe  
> Created_at: 2019-04-19 08:22:55 UTC  
> Url: https://github.com/boostorg/utility/pull/58#issuecomment-484806119  

> I think we should resist the urge to expand functionality of this header.  
  
OK; named it `ostream_string`.

---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2019-04-19 10:17:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/58#pullrequestreview-228652874  

Other than that one comment, LGTM.

📁 include/boost/utility/ostream_string.hpp

```diff
  20 |+     std::size_t size)
  21 |+ {
  22 |+     return static_cast<std::size_t>(os.rdbuf()->sputn(data, size));
```

> Username: Lastique  
> Created_at: 2019-04-19 10:17:27 UTC  
> Updated_at: 2019-04-19 14:02:42 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276955759  

`std::size_t` requires `#include <cstddef>`.

> Username: glenfe  
> Created_at: 2019-04-19 14:20:23 UTC  
> Url: https://github.com/boostorg/utility/pull/58#discussion_r276998340  

Included.


---

## Comment 8

> Username: glenfe  
> Created_at: 2019-04-19 14:20:44 UTC  
> Updated_at: 2019-04-19 14:20:53 UTC  
> Url: https://github.com/boostorg/utility/pull/58#issuecomment-484910110  

> Other than that one comment, LGTM.  
  
Thanks again.

---
