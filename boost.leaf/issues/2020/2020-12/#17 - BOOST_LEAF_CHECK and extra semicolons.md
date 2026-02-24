# #17 - BOOST_LEAF_CHECK and extra semicolons [Closed]

> Username: mikezackles  
> Created at: 2020-12-08 19:49:10 UTC  
> Updated at: 2020-12-08 21:39:42 UTC  
> Closed at: 2020-12-08 21:38:14 UTC  
> Url: https://github.com/boostorg/leaf/issues/17  

`BOOST_LEAF_ASSIGN` has no enclosing block, but `BOOST_LEAF_CHECK` does. This seems inconsistent and means that `BOOST_LEAF_CHECK(mycoolthing());` results in an extra semicolon (and potentially a warning).

---

## Comment 1

> Username: zajo  
> Created at: 2020-12-08 20:12:34 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-740962522  

Are you thinking  
  
```  
#define BOOST_LEAF_CHECK(r)\  
    static_assert(::boost::leaf::is_result_type<typename std::decay<decltype(r)>::type>::value, "BOOST_LEAF_CHECK requires a result type");\  
    auto && BOOST_LEAF_TMP = r;\  
    if( BOOST_LEAF_TMP )\  
        ;\  
    else\  
        return BOOST_LEAF_TMP.error()  
```  
?

---

## Comment 2

> Username: mikezackles  
> Created at: 2020-12-08 20:24:16 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-740976398  

Yes, something along those lines, I think.  
  
I had  
```  
#define LEAF_CHECK(r)\  
  static_assert(::boost::leaf::is_result_type<typename std::decay<decltype(r)>::type>::value, "BOOST_LEAF_CHECK requires a result type");\  
  auto &&BOOST_LEAF_TMP = r;\  
  if (!BOOST_LEAF_TMP) return BOOST_LEAF_TMP.error()  
```

---

## Comment 3

> Username: zajo  
> Created at: 2020-12-08 20:39:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-740995595  

Thats not safe in a macro, the `if` is lacking an `else`.

---

## Comment 4

> Username: mikezackles  
> Created at: 2020-12-08 20:47:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-741004575  

Makes sense!

---

## Comment 5

> Username: mikezackles  
> Created at: 2020-12-08 20:54:17 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-741013664  

Actually, I spoke too soon. My macro-fu is weak. Would you just be worried about someone abusing it like `LEAF_CHECK(...); else ...`?  
  
The one you proposed would be fine for me, if you think that one's acceptable.

---

## Comment 6

> Username: zajo  
> Created at: 2020-12-08 21:06:53 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-741032426  

Yes, exactly -- it is a very bad idea to leave an incomplete `if` statement in a macro. This was the main motivation for adding the scope actually, but it's true that it better without the scope, it would allow you to look at the temp variable during debugging.

---

## Comment 7

> Username: mikezackles  
> Created at: 2020-12-08 21:39:42 UTC  
> Url: https://github.com/boostorg/leaf/issues/17#issuecomment-741076103  

I see. Interesting to know the history, and thanks for the fix!
