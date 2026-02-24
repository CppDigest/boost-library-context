# #211 improve unsigned_decimal parsing algorithm [Merged]

> Username: alandefreitas  
> Created at: 2022-06-27 20:32:29 UTC  
> Updated at: 2022-06-28 18:25:35 UTC  
> Merged at: 2022-06-27 23:03:52 UTC  
> Closed at: 2022-06-27 23:03:52 UTC  
> Url: https://github.com/boostorg/url/pull/211  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 20:35:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020750295  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
  60 |+             Unsigned>::max)();
  61 |+ 
  62 |+     static constexpr Unsigned Digits10 =
```

> Username: vinniefalco  
> Created_at: 2022-06-27 20:35:19 UTC  
> Updated_at: 2022-06-27 20:35:20 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907775578  

these constants could stay in the function, which is arguable better since if we move the definition to the impl/xxx.hpp eventually, then there is less material in this header.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 21:02:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020778228  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
 106 |+ 
 107 |+         constexpr auto div = (Max / ten);
 108 |+         constexpr char rem = (Max % ten);
```

> Username: vinniefalco  
> Created_at: 2022-06-27 21:02:47 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907795173  

You can move div and rem down to just before they are used


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 21:03:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020778613  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
 122 |         }
 123 |+ 
 124 |+         if(
```

> Username: vinniefalco  
> Created_at: 2022-06-27 21:03:09 UTC  
> Updated_at: 2022-06-27 21:03:10 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907795416  

Should this `if` should be in the body of the previous `if`?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 21:23:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020798395  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
  87 |+                 Unsigned>::digits10;
  88 |+         static constexpr Unsigned ten = 10;
  89 |+         auto const safe_end = (std::min)(it + Digits10, end);
```

> Username: vinniefalco  
> Created_at: 2022-06-27 21:23:31 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907809465  

```  
auto const safe_end = (  
    std::min)(it + Digits10, end);  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 21:25:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020799928  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
 102 |+         if(
 103 |+             it != end &&
 104 |+             digit_chars(*it))
```

> Username: vinniefalco  
> Created_at: 2022-06-27 21:25:01 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907810483  

```  
        if( it != end &&  
            digit_chars(*it))  
```


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 21:25:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020800291  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
 121 |+             if(
 122 |+                 it < end &&
 123 |+                 digit_chars(*it))
```

> Username: vinniefalco  
> Created_at: 2022-06-27 21:25:24 UTC  
> Updated_at: 2022-06-27 21:25:25 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907810798  

```  
            if( it < end &&  
                digit_chars(*it))  
```


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-27 21:48:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/211#pullrequestreview-1020822659  

📁 include/boost/url/grammar/unsigned_decimal.hpp

```diff
  58 |     static
  59 |     void
  60 |     parse(
```

> Username: vinniefalco  
> Created_at: 2022-06-27 21:48:31 UTC  
> Url: https://github.com/boostorg/url/pull/211#discussion_r907826425  

this function definition should go in `<boost/url/grammar/impl/unsigned_decimal.hpp>`


---
