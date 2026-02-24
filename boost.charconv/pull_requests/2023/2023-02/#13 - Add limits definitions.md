# #13 Add limits definitions [Merged]

> Username: mborland  
> Created at: 2023-02-07 17:20:27 UTC  
> Updated at: 2023-02-07 20:20:59 UTC  
> Merged at: 2023-02-07 18:58:59 UTC  
> Closed at: 2023-02-07 18:58:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/13  

@vinniefalco is this what you were looking for? It defines the maximum number of characters needed to store each type in either base10 or the maximum possible amount (binary).

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-02-07 18:49:46 UTC  
> Url: https://github.com/boostorg/charconv/pull/13#issuecomment-1421282352  

as long as those are the correct numbers, yeah that looks good ^_^

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-02-07 20:07:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/13#issuecomment-1421374531  

It should be something like  
```  
template<class T> struct limits  
{  
    static constexpr int max_chars10 = std::numeric_limits<T>::is_integral? std::numeric_limits<T>::digits10 + 1 + std::numeric_limits<T>::is_signed: std::numeric_limits<T>::max_digits10 + 1;  
};  
```  
I'm not sure what `max_chars` is supposed to be.  
  
Also, the test doesn't actually test the specification (which doesn't yet exist). Assuming the specification is "`max_chars10`: the size of the buffer passed to `to_chars` for which `to_chars` is guaranteed to succeed for all values of T (in base 10)", the test should actually check this, that is, pass a buffer of that size to `to_chars` and check that it succeeds for the min/max values of T.

---

## Comment 3

> Username: mborland  
> Created_at: 2023-02-07 20:13:54 UTC  
> Url: https://github.com/boostorg/charconv/pull/13#issuecomment-1421382375  

> It should be something like  
>   
> ```  
> template<class T> struct limits  
> {  
>     static constexpr int max_chars10 = std::numeric_limits<T>::is_integral? std::numeric_limits<T>::digits10 + 1 + std::numeric_limits<T>::is_signed: std::numeric_limits<T>::max_digits10 + 1;  
> };  
> ```  
>   
> I'm not sure what `max_chars` is supposed to be.  
  
It's the characters needed for the base2 representation since that's the largest possible representation.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-02-07 20:20:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/13#issuecomment-1421391503  

Yes, this makes sense. So max_chars is the number of characters that works for any base. (And should be tested accordingly.)

---
