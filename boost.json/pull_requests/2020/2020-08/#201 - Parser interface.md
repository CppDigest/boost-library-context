# #201 Parser interface [Closed]

> Username: sdkrystian  
> Created at: 2020-08-24 16:50:26 UTC  
> Updated at: 2020-08-25 06:59:51 UTC  
> Closed at: 2020-08-25 00:00:52 UTC  
> Url: https://github.com/boostorg/json/pull/201  



---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-08-24 16:51:14 UTC  
> Url: https://github.com/boostorg/json/pull/201#issuecomment-679244264  

@pauldreik Do we need `parser::release` to throw to not break fuzzing?

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:56:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473684641  

📁 include/boost/json/impl/parse.ipp

```diff
  11 |+ #define BOOST_JSON_IMPL_PARSE_IPP
  12 |+ 
  13 |+ #include <boost/json/parse.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:56:50 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475758918  

You don't need to include this because .ipp files are assumed to come after the include of the corresponding public .hpp


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:57:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473685344  

📁 include/boost/json/impl/parse.ipp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2020 Krystian Stasiowski (sdkrystian@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:57:42 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475759478  

hey now... I wrote everything in this file


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:58:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473685735  

📁 include/boost/json/parse.hpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2020 Krystian Stasiowski (sdkrystian@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:58:11 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475759795  

hey now, I wrote everything in this file. moving it to a new file doesn't mean you have the sole authorship

> Username: sdkrystian  
> Created_at: 2020-08-24 17:02:21 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475762309  

omg sorry I'll change that


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:58:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473686079  

📁 include/boost/json/parse.hpp

```diff
  14 |+ #include <boost/json/error.hpp>
  15 |+ #include <boost/json/storage_ptr.hpp>
  16 |+ #include <boost/json/value.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:58:38 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475760064  

might as well add `<boost/json/string_view.hpp>` while we're at it


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473686456  

📁 include/boost/json/parse.hpp

```diff
  21 |+ /** Parse a string of JSON.
  22 |+ 
  23 |+     This function parses an entire single string in
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:07 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475760386  

remove the word "single"


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473686659  

📁 include/boost/json/parse.hpp

```diff
  57 |+ /** Parse a string of JSON.
  58 |+ 
  59 |+     This function parses an entire single string in
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:22 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475760546  

remove the word "single"


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473686829  

📁 include/boost/json/parse.hpp

```diff
  81 |+     @throw system_error Thrown on failure.
  82 |+ 
  83 |+     @return A value representing the parsed JSON upon success.
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:35 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475760677  

Should the return value annotation come before the params?

> Username: sdkrystian  
> Created_at: 2020-08-24 17:04:50 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475763605  

Oops, yup


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473686972  

📁 include/boost/json/parse.hpp

```diff
  96 |+ #endif
  97 |+ 
  98 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2020-08-24 16:59:47 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475760780  

you always do this..

> Username: vinniefalco  
> Created_at: 2020-08-24 17:03:35 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475762949  

(the line is missing a carriage return)

> Username: sdkrystian  
> Created_at: 2020-08-24 17:05:00 UTC  
> Updated_at: 2020-08-24 17:42:23 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475763704  

LOL my bad


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-24 23:23:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/201#pullrequestreview-473992648  

📁 fuzzing/fuzz_parser.cpp

```diff
  32 |-         value jv = p.release();
  32 |+         value jv = p.release( ec );
  33 |         return jv.is_number();
```

> Username: vinniefalco  
> Created_at: 2020-08-24 23:23:45 UTC  
> Updated_at: 2020-08-24 23:23:46 UTC  
> Url: https://github.com/boostorg/json/pull/201#discussion_r475954695  

you have to check `ec` here. correct code:  
```  
    // Take ownership of the resulting value.  
    if(! ec)  
    {  
        value jv = p.release( ec );  
        if(! ec )  
            return jv.is_number();  
    }  
    return false;  
```  
  
I fixed it in my copy


---

## Comment 11

> Username: pauldreik  
> Created_at: 2020-08-25 06:59:51 UTC  
> Url: https://github.com/boostorg/json/pull/201#issuecomment-679842680  

> @pauldreik Do we need `parser::release` to throw to not break fuzzing?  
  
No, it should not matter for correctness. But for fuzzer performance it is better to avoid throwing exceptions. Fuzzing causes problematic input all the time, so the exceptional case is the most common and that is usually the slow path. The change @vinniefalco added looks good.  By the way, the reason to actually use the number in the fuzzer is to avoid the optimizer removing the code and exercise more code paths.

---
