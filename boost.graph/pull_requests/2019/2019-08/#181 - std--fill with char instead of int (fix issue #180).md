# #181 std::fill with char instead of int (fix issue #180) [Closed]

> Username: jasjuang  
> Created at: 2019-08-20 18:35:14 UTC  
> Updated at: 2019-08-22 18:19:24 UTC  
> Closed at: 2019-08-22 18:19:24 UTC  
> Url: https://github.com/boostorg/graph/pull/181  

fix issue #180

---

## Comment 1

> Username: anadon  
> Created_at: 2019-08-20 21:55:24 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523210556  

I'd like to delay this until I get my CI PR finished.

---

## Comment 2

> Username: glenfe  
> Created_at: 2019-08-22 00:09:08 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523695361  

You're replacing 0 with '0'? Why do you believe this is the solution?

---

## Comment 3

> Username: jasjuang  
> Created_at: 2019-08-22 00:13:14 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523696077  

@glenfe It's not about believing it or not. This actually solves the compilation error as indicated in #180.

---

## Comment 4

> Username: anadon  
> Created_at: 2019-08-22 00:17:24 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523696828  

@jasjuang I didn't look at this too closely, but understanding why it fixes it and why it is right is important.

---

## Comment 5

> Username: jasjuang  
> Created_at: 2019-08-22 00:21:52 UTC  
> Updated_at: 2019-08-22 00:22:18 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523697573  

It is explained in previous linked threads. `data` is a `unsigned char`, `0` is an int, so there's an implicit type conversion when `std::fill` `data` with `0`, visual studio is strict hence it raises warnings against it. This PR turns `0` to `'0'`, so now it's filled with char and matches type with `data`, so visual studio stops complaining. Is this explanation clear enough?

---

## Comment 6

> Username: anadon  
> Created_at: 2019-08-22 00:23:45 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523697870  

You didn't reference any threads here.  Furthermore, the only way to preserve identical behavior if what you are describing is complete is to use `'\0'`.  So it sounds like there is missing information.  What's up?

---

## Comment 7

> Username: jasjuang  
> Created_at: 2019-08-22 00:33:31 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523699670  

Ok, I made the changes as requested. Nothing special is up, just trying to resolve the bug here.  Everything was explained in #180 and the link at the end of #180. Please merge. Thanks.

---

## Comment 8

> Username: anadon  
> Created_at: 2019-08-22 01:52:16 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523713884  

@glenfe Looks clean.  Would you like to do the honors?

---

## Comment 9

> Username: glenfe  
> Created_at: 2019-08-22 02:11:41 UTC  
> Updated_at: 2019-08-22 03:59:37 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523717614  

@jasjuang  
>  It's not about believing it or not. This actually solves the compilation error as indicated in #180.  
  
Just eliminating a compilation error in the wrong way is **not** a solution. '0' and 0 have two different values. Just like 'a' and 0 have two different values.  
  
If you want an `unsigned char` with a value of 0, you should use an unsigned char with a value of 0. Not one with a value of '0'.  
  
Either `'\0'` or `static_cast<unsigned char>(0)` would have been more appropriate.

---

## Comment 10

> Username: glenfe  
> Created_at: 2019-08-22 02:19:08 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523719040  

@anadon   
> Looks clean. Would you like to do the honors?  
  
Instead of `new unsigned char[n]`  followed by `std::fill` to initialize all the elements to 0, I would prefer using just `new unsigned char[n]()` which would value-initialize all the elements to 0. This would eliminate the need to use `std::fill` entirely.

---

## Comment 11

> Username: glenfe  
> Created_at: 2019-08-22 02:25:07 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523720212  

@anadon To elaborate on my comment above: Right now, the code is doing:  
  
```  
explicit two_bit_color_map(std::size_t n, const IndexMap& index = IndexMap())  
  : n(n), index(index),  
    data(new unsigned char[(n + elements_per_char - 1) / elements_per_char])  
{  
  // Fill to white  
  std::fill(data.get(), data.get() + (n + elements_per_char - 1) / elements_per_char, 0);  
}  
```  
  
I'm suggesting instead doing:  
  
```  
explicit two_bit_color_map(std::size_t n, const IndexMap& index = IndexMap())  
  : n(n), index(index),  
    data(new unsigned char[(n + elements_per_char - 1) / elements_per_char]())  
{ }  
```  
  
Note the extra parenthesis `()` in the `new` expression for value-initialization, and the elimination of the `std::fill()` call.

---

## Comment 12

> Username: anadon  
> Created_at: 2019-08-22 02:27:48 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523720708  

@glenfe I was just going through that and figuring out where you intended that.  I did find it before reading this!  I should have caught that.

---

## Comment 13

> Username: glenfe  
> Created_at: 2019-08-22 04:50:06 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-523746565  

See d3b157f4656a462beb9ca767838e69b60b393b4b

---

## Comment 14

> Username: glenfe  
> Created_at: 2019-08-22 18:19:24 UTC  
> Url: https://github.com/boostorg/graph/pull/181#issuecomment-524019863  

I've merged d3b157f to develop. We don't need this PR.

---
