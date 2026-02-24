# #675 [serializer] Added handling for NaN & Inf. [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-02-05 17:58:55 UTC  
> Updated at: 2023-06-02 09:09:02 UTC  
> Closed at: 2023-06-02 09:09:02 UTC  
> Url: https://github.com/boostorg/json/pull/675  

Closes #397

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:01:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873956708  

📁 include/boost/json/detail/impl/format.ipp

```diff
 116 |+     if (std::isnan(d))
 117 |+     {
 118 |+         std::copy(opt.nan.begin(), opt.nan.end(), dest);
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:01:34 UTC  
> Updated_at: 2022-02-05 18:01:41 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800083750  

did you mean  
```  
opt.copy( dest, 0 );  
```  
?

> Username: klemens-morgenstern  
> Created_at: 2022-02-05 18:07:15 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084253  

I did not mean that, because I don't see why.

> Username: vinniefalco  
> Created_at: 2022-02-05 18:14:32 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084969  

So we don't have to include `<algorithm>`, which you also forgot


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:02:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873956789  

📁 include/boost/json/detail/impl/format.ipp

```diff
 142 |+ {
 143 |+     if (std::isnan(d))
 144 |+         ec = make_error_code(error::not_number);
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:02:21 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800083876  

Did you mean  
```  
ec = error::not_number;  
```  
?


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:04:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873956920  

📁 include/boost/json/impl/serialize.ipp

```diff
 133 |+   // serialize to a small buffer to avoid
 134 |+   // the first few allocations in std::string
 135 |+   char buf[BOOST_JSON_STACK_BUFFER_SIZE];
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:04:11 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084019  

Why are we duplicating this function?

> Username: klemens-morgenstern  
> Created_at: 2022-02-05 18:08:52 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084409  

I thought it would turn out different, i.e. need some early breaks for errors. Was wrong, could be redone as a template.

> Username: vinniefalco  
> Created_at: 2022-02-05 18:15:23 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800085066  

A template would still result in duplication in the emitted code, we need to avoid that since Boost.JSON needs to be as lean as possible.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:06:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873957137  

📁 include/boost/json/impl/serializer.ipp

```diff
 719 | read_some(
 715 |-     char* dest, std::size_t size)
 720 |+     char* dest, std::size_t size, const serialize_options & opt)
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:06:38 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084197  

Why are we passing the options into every call to `read_some`? Shouldn't we construct with the options instead, and then access the options as a data member instead of passing it as a parameter to every member function?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:07:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873957197  

📁 include/boost/json/impl/serializer.ipp

```diff
 823 |+     fn1_opt_ = &serializer::write_string<false>;
 824 |+     fn0_err_ = &serializer::write_string<true>;
 825 |+     fn1_err_ = &serializer::write_string<false>;
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:07:27 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084260  

hopefully this nonsense will go away when we make the options a data member

> Username: klemens-morgenstern  
> Created_at: 2022-02-05 18:10:50 UTC  
> Updated_at: 2022-02-05 18:11:41 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084647  

This should make the code faster, since the compiler doesn't need to recheck which it is. If you make it a member you need to add if-else everywhere.

> Username: klemens-morgenstern  
> Created_at: 2022-02-05 18:14:31 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084967  

To be fair, it's probably overdoing it, because this is one unnecessary if -else per double overhead.  I didn't profile either, just copied your model.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:09:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873957316  

📁 include/boost/json/serialize_options.hpp

```diff
  26 |+   /// The value to use for a NaN double.
  27 |+   /// The value will not be quoted in json.
  28 |+   string_view nan  = "null";
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:09:02 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084418  

The defaults are supposed to be empty strings, i.e. throwing on inf/nan is the default behavior.

> Username: klemens-morgenstern  
> Created_at: 2022-02-05 18:12:30 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084800  

Probably the wrong default, I copied Javascript here.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:09:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873957394  

📁 include/boost/json/serializer.hpp

```diff
 245 |     string_view
 234 |-     read(char* dest, std::size_t size);
 246 |+     read(char* dest, std::size_t size, const serialize_options & opts = {});
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:09:53 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084534  

The options should be for the whole serializer not for each individual call to `read`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 18:10:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873957438  

📁 test/double.cpp

```diff
 424 |+     void testInvalid()
 425 |+     {
 426 |+       const json::serialize_options testOpts = {"printed dollars", "government debt", "usd value"};
```

> Username: vinniefalco  
> Created_at: 2022-02-05 18:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800084603  

These example strings are non-sterile


---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-02-05 18:11:53 UTC  
> Url: https://github.com/boostorg/json/pull/675#issuecomment-1030672551  

Exposition (the .qbk files) needs to be updated explaining serialization error handling and format conversion of nan/inf

---

## Comment 10

> Username: grisumbras  
> Created_at: 2022-02-05 18:39:52 UTC  
> Url: https://github.com/boostorg/json/pull/675#issuecomment-1030677565  

The approach we described in the issue is somewhat different:  
1. Serializer options are empty by default (not nulls).  
2. If an option is empty and the corresponding special value is encountered — an error is produced.  
3. Thus, users should be able to use options _and_ error codes together. If an error_code isn't used, the serializer throws.  
  
Top level APIs for serialzer should probably look like this:  
```  
serializer::serializer(serializer_options const& = {});  
  
serializer::read(char* dest, std::size_t size, serializer_options const& = {});  
serializer::read(char* dest, std::size_t size, error_code& ec, serializer_options const& = {});  
serializer::read(char* dest, std::size_t size, std::error_code& ec, serializer_options const& = {}); // for convenience  
```

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-02-05 19:11:19 UTC  
> Url: https://github.com/boostorg/json/pull/675#issuecomment-1030682691  

> Top level APIs for serialzer should probably look like this:  
  
Well no, the serializer should be constructed with the options, they should not be per-read. Alternatively there can be a `set_options` function. But it should be specified that undefined behavior results if the options are changed in the middle of serialization.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-02-05 19:14:29 UTC  
> Url: https://github.com/boostorg/json/pull/675#issuecomment-1030683175  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/675/pullrequest-condensed-b0115d4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/675/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/675/pullrequest.html)

---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 21:50:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/675#pullrequestreview-873972861  

📁 include/boost/json/detail/impl/format.ipp

```diff
 123 |+         if (d > 0.)
 124 |+         {
 125 |+             std::copy(opt.pinf.begin(), opt.pinf.end(), dest);
```

> Username: vinniefalco  
> Created_at: 2022-02-05 21:50:53 UTC  
> Url: https://github.com/boostorg/json/pull/675#discussion_r800103604  

This code has to handle the case where there is not enough room in the output to hold the user-defined string


---

## Comment 14

> Username: grisumbras  
> Created_at: 2023-06-02 09:09:02 UTC  
> Url: https://github.com/boostorg/json/pull/675#issuecomment-1573408812  

Superceded by #884

---
