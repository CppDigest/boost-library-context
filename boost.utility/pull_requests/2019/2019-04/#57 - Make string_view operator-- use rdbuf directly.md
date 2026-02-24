# #57 Make string_view operator<< use rdbuf directly [Merged]

> Username: glenfe  
> Created at: 2019-04-12 12:28:54 UTC  
> Updated at: 2019-04-18 16:00:54 UTC  
> Merged at: 2019-04-18 16:00:54 UTC  
> Closed at: 2019-04-18 16:00:54 UTC  
> Url: https://github.com/boostorg/utility/pull/57  



---

## Comment 1

> Username: mclow  
> Created_at: 2019-04-12 13:26:02 UTC  
> Updated_at: 2019-04-12 13:26:14 UTC  
> Url: https://github.com/boostorg/utility/pull/57#issuecomment-482573584  

I don't think you can do it this way.  `operator<<(` needs to set the `badbit` in the stream (and possibly throw) on failure. The current code does this by delegating to `write`.

---

## Comment 2

> Username: glenfe  
> Created_at: 2019-04-12 14:04:15 UTC  
> Url: https://github.com/boostorg/utility/pull/57#issuecomment-482586733  

The destructor of `detail::os_sv_holder` sets `badbit` in the stream conditionally (upon any kind of failure, exceptions or otherwise). Any exceptions thrown are propagated.

---

## Review 3 [Changes requested]

> Username: Lastique  
> Created_at: 2019-04-12 17:09:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/utility/pull/57#pullrequestreview-226189063  

I'm not sure I understand the point of these changes and what is meant by "exception safe". If there is a problem, please describe it in more detail.  
  
I've commented on `string_ref`, but the same applies to `string_view`.

📁 include/boost/utility/string_ref.hpp

```diff
 429 |+         {
 430 |+             for (charT c = os.fill(); count > 0; --count) {
 431 |+                 if (traits::eq_int_type(os.rdbuf()->sputc(c), traits::eof())) {
```

> Username: Lastique  
> Created_at: 2019-04-12 16:59:55 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r274988853  

This is much less efficient than the original code. There must be a convincing argument for this change.

> Username: glenfe  
> Created_at: 2019-04-12 18:13:16 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275012803  

Addressed by preserving the 8 charT fill.

---

📁 include/boost/utility/string_ref.hpp

```diff
 443 |+             ~sr_os_holder() {
 444 |+                 if (os_) {
 445 |+                     os_->setstate(std::basic_ostream<charT, traits>::badbit);
```

> Username: Lastique  
> Created_at: 2019-04-12 17:01:48 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r274989434  

If this throws, it will call `terminate` since the destructor is `noexcept` by default.

> Username: glenfe  
> Created_at: 2019-04-12 18:13:44 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275012952  

Corrected noexcept specifier on destructor.

---

📁 include/boost/utility/string_ref.hpp

```diff
 465 |+         typename stream::sentry entry(os);
 466 |+         if (entry) {
 467 |+             std::size_t width = os.width();
```

> Username: Lastique  
> Created_at: 2019-04-12 17:02:21 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r274989593  

`os.width()` is `streamsize`, which is signed, this will generate a warning. Same is below, where `sputn` is involved.

> Username: glenfe  
> Created_at: 2019-04-12 18:14:09 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275013074  

Used streamsize.

> Username: Lastique  
> Created_at: 2019-04-12 21:52:55 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275075324  

This still needs an explicit cast to silence the warning, see the original code. I think, it was an MSVC warning, but I can't remember the version.

> Username: glenfe  
> Created_at: 2019-04-12 22:08:34 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275078280  

Done (and thanks).


---

## Review 4 [Changes requested]

> Username: Lastique  
> Created_at: 2019-04-12 20:54:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/utility/pull/57#pullrequestreview-226275896  

📁 include/boost/utility/string_ref.hpp

```diff
 469 |+         if (entry) {
 470 |+             std::streamsize width = os.width();
 471 |+             std::streamsize size = str.size();
```

> Username: Lastique  
> Created_at: 2019-04-12 20:32:45 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275054757  

`str.size()` is `std::size_t`, this line needed not to change. IMHO, the original code was better in that it explicitly casted `std::streamsize` to `std::size_t` and then proceeded with unsigned integers everywhere since width should not be negative anyway.

> Username: glenfe  
> Created_at: 2019-04-12 21:30:03 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275070199  

Updated. Using `std::size_t` also avoids requiring `ios` in favor of `iosfwd`.

---

📁 include/boost/utility/string_ref.hpp

```diff
  28 | #include <string>
  28 |- #include <iosfwd>
  29 |+ #include <ios>
```

> Username: Lastique  
> Created_at: 2019-04-12 20:34:24 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275055299  

Is this change needed? `iosfwd` is more lightweight and it should be enough when you're not doing IO. When you do, you've already included `ios` via `ostream` for your stream.

> Username: glenfe  
> Created_at: 2019-04-12 20:56:51 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275061928  

For the use of the std::streamsize type (clang/libc++ required it).

> Username: Lastique  
> Created_at: 2019-04-12 20:58:09 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275062330  

In fact, `ios` doesn't necessarily forward-declare `basic_ostream`, so the change is not correct.

> Username: glenfe  
> Created_at: 2019-04-12 21:01:14 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275063242  

`ios` does declare `std::streamsize`.

> Username: Lastique  
> Created_at: 2019-04-12 21:07:01 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275064772  

What if you don't mention `std::streamsize` in the code? Otherwise we'd have to include both headers.

> Username: glenfe  
> Created_at: 2019-04-12 21:23:50 UTC  
> Updated_at: 2019-04-12 22:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/57#discussion_r275068827  

Sounds good to me.


---

## Review 5 [Approved]

> Username: Lastique  
> Created_at: 2019-04-12 22:24:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/utility/pull/57#pullrequestreview-226310087  

LGTM, thanks.

---
