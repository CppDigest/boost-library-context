# #638 iostream parsing [Closed]

> Username: grisumbras  
> Created at: 2021-10-10 09:41:05 UTC  
> Updated at: 2022-11-06 10:56:14 UTC  
> Closed at: 2022-11-06 10:56:11 UTC  
> Url: https://github.com/boostorg/json/pull/638  

Fix #619

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-10-10 11:44:59 UTC  
> Url: https://github.com/boostorg/json/pull/638#issuecomment-939467499  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/638/pullrequest-condensed-bda60fb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/638/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/638/pullrequest.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-10-10 18:15:57 UTC  
> Url: https://github.com/boostorg/json/pull/638#issuecomment-939528232  

You didn't give my man a chance to do it?

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:16:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697594  

📁 include/boost/json.hpp

```diff
  17 | #include <boost/json/error.hpp>
  18 | #include <boost/json/fwd.hpp>
  19 |+ #include <boost/json/iostreams.hpp>
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:16:32 UTC  
> Updated_at: 2021-10-10 18:16:33 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677384  

Does this force the include of `<iostream>`? It is better to only require `<iosfwd>`.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:16:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697618  

📁 include/boost/json/impl/iostreams.ipp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2021 Dmitry Arkhipov (grisumbras@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677406  

oooh Pinocchio is turning into a Real Boy :)


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:17:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697671  

📁 include/boost/json/impl/iostreams.ipp

```diff
  44 |+ {
  45 |+     if( mem_res_ )
  46 |+         mem_res_->deallocate(pbase(), 1024, alignof(char));
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:17:32 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677497  

nice, using the storage!


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:18:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697759  

📁 include/boost/json/impl/iostreams.ipp

```diff
  63 |+ 
  64 |+     ec = std::move(ec_);
  65 |+     return ec
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:18:41 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677646  

It is better to write it this way:  
```  
if( ec.failed() )  
  return nullptr;  
return p_.release();  
```  
  
because it helps coverage reports. Otherwise the ternary operator sometimes confuses gcov.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:19:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697811  

📁 include/boost/json/impl/iostreams.ipp

```diff
  81 |+ 
  82 |+ streambuf::int_type
  83 |+ streambuf::overflow(int_type ch)
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:19:21 UTC  
> Updated_at: 2021-10-10 18:19:22 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677708  

in all cases when the return type is a nested type I use auto and a trailing return. But this is just style, so not terribly important.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:20:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697866  

📁 include/boost/json/impl/iostreams.ipp

```diff
 132 |+     if( !is )
 133 |+     {
 134 |+         ec = std::make_error_code(std::io_errc::stream);
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:20:08 UTC  
> Updated_at: 2021-10-10 18:20:09 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677809  

in theory shouldn't this assignment also work:  
```  
ec = std::io_errc::stream  
```  
?   
  
Thanks to Peter's changes? If not, we might consider updating Boost.System to support it, if possible.

> Username: pdimov  
> Created_at: 2021-10-10 22:19:22 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725707312  

It's not going to work because `io_errc` specializes `std::is_error_code_enum` but not `boost::system::is_error_code_enum`, but it might be possible to make it work. Make me an issue in boostorg/system.

> Username: vinniefalco  
> Created_at: 2021-10-10 22:27:03 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725708244  

Yep https://github.com/boostorg/system/issues/70

> Username: pdimov  
> Created_at: 2021-10-12 14:44:33 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r727211620  

This should work now.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:20:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775697925  

📁 include/boost/json/impl/iostreams.ipp

```diff
 150 |+ 
 151 |+ value
 152 |+ parse(
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:20:40 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725677894  

if this is private, you might make it `static`


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-10 18:21:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/638#pullrequestreview-775698049  

📁 include/boost/json/iostreams.hpp

```diff
  45 |+     parse_options const& opt = {});
  46 |+ 
  47 |+ class streambuf : public std::streambuf
```

> Username: vinniefalco  
> Created_at: 2021-10-10 18:21:58 UTC  
> Url: https://github.com/boostorg/json/pull/638#discussion_r725678080  

Are you sure you want to make this public? Then we have to include iostreams which kind of suxxxxx. Better to just have the `std::istream&` overload of `parse`, include `<iosfwd>`, and put the rest of this in the .cpp perhaps in an anonymous or detail namespace.


---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2021-10-10 18:49:04 UTC  
> Url: https://github.com/boostorg/json/pull/638#issuecomment-939533088  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/638/pullrequest-condensed-6177ac0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/638/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/638/pullrequest.html)

---

## Comment 12

> Username: pdimov  
> Created_at: 2021-10-12 14:46:54 UTC  
> Url: https://github.com/boostorg/json/pull/638#issuecomment-941082426  

I don't get it, why does this need its own streambuf?

---

## Comment 13

> Username: grisumbras  
> Created_at: 2021-10-13 13:00:42 UTC  
> Url: https://github.com/boostorg/json/pull/638#issuecomment-942280480  

> I don't get it, why does this need its own streambuf?  
  
It doesn't. Vinnie previously directed me at using an output streambuf and I've made one. Then using a streambuf became the simplest way to implement parsing from an istream. Now, the question is whether there is a use case for such a streambuf by itself.

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2021-10-13 13:08:20 UTC  
> Url: https://github.com/boostorg/json/pull/638#issuecomment-942288377  

>  Now, the question is whether there is a use case for such a streambuf by itself.  
  
I would make it private. If a user requests the feature then we can think about making it public.

---
