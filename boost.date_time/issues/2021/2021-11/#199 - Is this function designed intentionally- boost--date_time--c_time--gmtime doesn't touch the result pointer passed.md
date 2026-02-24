# #199 - Is this function designed intentionally?  boost::date_time::c_time::gmtime doesn't touch the result pointer passed. [Open]

> Username: fragrans  
> Created at: 2021-11-23 06:00:50 UTC  
> Updated at: 2022-02-20 12:09:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/199  

`inline static std::tm* gmtime(const std::time_t* t, std::tm* result);`  
  
I assume this function will change the result pointer by filling the struct; but it doesn't;  
  
`Windows 10;  
MSVC 16.11.7`  
  
https://github.com/boostorg/date_time/blob/7156392706faa282044ce9f1d6ba9fdd207f6da9/include/boost/date_time/c_time.hpp#L109-L120

---

## Comment 1

> Username: fragrans  
> Created at: 2021-12-06 04:59:35 UTC  
> Url: https://github.com/boostorg/date_time/issues/199#issuecomment-986439045  

Any one?

---

## Comment 2

> Username: JeffGarland  
> Created at: 2021-12-06 12:09:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/199#issuecomment-986716807  

I'm not sure what the confusion is.  The function calls gmtime to assign the result pointer and then returns it? Also this is really an implementation detail of the library, so not really part of the documented api.

---

## Comment 3

> Username: fragrans  
> Created at: 2021-12-07 01:17:56 UTC  
> Url: https://github.com/boostorg/date_time/issues/199#issuecomment-987472967  

the content of the 2nd argument (pointer result) passed to the function will not change;  
didn't you see that?

---

## Comment 4

> Username: JeffGarland  
> Created at: 2021-12-07 02:36:25 UTC  
> Updated at: 2021-12-07 02:36:48 UTC  
> Url: https://github.com/boostorg/date_time/issues/199#issuecomment-987513447  

```         result = std::gmtime(t); ```  
  
That changes it.

---

## Comment 5

> Username: fragrans  
> Created at: 2021-12-07 04:00:14 UTC  
> Url: https://github.com/boostorg/date_time/issues/199#issuecomment-987546567  

I am sorry, and confused. if you want to change this pointer, you need to  
  
```  
auto temp = std::gmtime(t);   
*result = *temp;  
```

---

## Comment 6

> Username: jeking3  
> Created at: 2022-02-20 12:09:57 UTC  
> Url: https://github.com/boostorg/date_time/issues/199#issuecomment-1046223789  

@fragrans This is a C++03-compatible library so `auto` would not be appropriate, but I agree with you otherwise.  
  
@JeffGarland if you look in date_clock_device.hpp, the implementation is passed a std::tm& but that is never filled in, and the returned std::tm* is going to be different than what was passed in for result.  It does seem a bit confusing and likely a bug as there could be other bad assumptions made by consumers about get_local_time and get_universal_time based on the inputs.
