# #31 - int -> int32_t [Closed]

> Username: rdiez  
> Created at: 2018-02-14 13:41:14 UTC  
> Updated at: 2018-11-22 06:40:12 UTC  
> Closed at: 2018-11-22 06:39:59 UTC  
> Url: https://github.com/boostorg/chrono/issues/31  

In this routine:  
  
inline int32_t days_from_1970(int32_t year)  
    {  
      static const int days_from_0_to_1970 = days_from_0(1970);  
      return days_from_0(year) - days_from_0_to_1970;  
}  
  
Shouldn't type "int" above (for variable 'days_from_0_to_1970') actually be "int32_t"?

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-14 18:07:39 UTC  
> Url: https://github.com/boostorg/chrono/issues/31#issuecomment-365694391  

yes :)

---

## Comment 2

> Username: viboes  
> Created at: 2018-09-07 04:03:49 UTC  
> Url: https://github.com/boostorg/chrono/issues/31#issuecomment-419315941  

https://github.com/boostorg/chrono/commit/ab84c68b6e49babfe3068db380d77de8a30931a6

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-21 21:50:44 UTC  
> Url: https://github.com/boostorg/chrono/issues/31#issuecomment-440821951  

This has milestone 1.70 but it is in 1.69 beta 1.

---

## Comment 4

> Username: viboes  
> Created at: 2018-11-22 06:40:11 UTC  
> Url: https://github.com/boostorg/chrono/issues/31#issuecomment-440928232  

Yes, you are right.
