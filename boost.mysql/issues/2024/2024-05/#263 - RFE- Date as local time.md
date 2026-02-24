# #263 - RFE: Date as local time [Closed]

> Username: SpareSimian  
> Created at: 2024-05-15 22:09:29 UTC  
> Updated at: 2024-05-19 08:37:29 UTC  
> Closed at: 2024-05-19 08:37:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/263  

I'm working with an old database with date fields with no timezone information. (The dates assume Pacific time.) boost::mysql::date appears to assume a UTC date when invoking as_time_point. It would be nice to be able to extract a local time_point, instead. For now, I think I need to build my own time_point from the y/m/d values.   
  
Also, something not obvious from the documentation was whether months and days are zero- or one-based. Reading through the sources, it seems they're one-based.

---

## Comment 1

> Username: SpareSimian  
> Created at: 2024-05-15 23:15:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2113632436  

It seems what I want is a local_days object, so I'm creating that from the raw y/m/d in the results. https://stackoverflow.com/questions/70755499/create-stdchronozoned-time-from-zone-and-time

---

## Comment 2

> Username: anarthal  
> Created at: 2024-05-16 08:53:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2114563148  

You're completely correct. As this library targets C++11 and above, it used the only `time_point` facilities it had at the moment.  
  
For now, and since the `date::time_point` you're getting is attempting to represent what C++20 `local_days` does, I'd advise you to cast the time point to `local_days`, like this:  
  
```  
mysql::date d; // get it from DB  
std::chrono::local_days locdays (d.as_time_point().time_since_epoch());  
std::chrono::zoned_time zt ("Your/time/zone", locdays);  
```  
  
(See [this godbolt](https://godbolt.org/z/445KhPacE)).  
  
That being said, the ideal would be making `get_time_point` and `as_time_point` return `local_days`, but I can't just change it because that'd break people not in C++20. What I could do is adding `get_local_time_point` and `as_local_time_point` accessors returning `local_days` (and same for `datetime`).  
  
I've also opened #264 to clarify the docs.  
  
Let me know your thoughts about this.  
  
Cheers,  
Ruben.

---

## Comment 3

> Username: SpareSimian  
> Created at: 2024-05-16 16:24:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2115678238  

That sounds reasonable. Is the cast to local_days like reinterpret_cast, leaving the value untouched and just changing the clock type? I wasn't sure if it would try to do math to convert it to the local timezone, but it now sounds like I misunderstood local_days to mean the actual local timezone and not just a time_point with an unspecified clock.  
  
I agree about the compatibility and adding separate accessors makes sense to me.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-05-16 16:29:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2115688607  

`local_days` carries no time zone information with it - it's like a `sys_days`, but with a placeholder clock such as, when you pass it to `zoned_time` constructor, it knows it's not UTC, but in the time zone you pass. Think of it as the "time-zone unaware" datetimes in Python.  
  
So my cast is just discarding the time zone information `sys_days` has with it. It's building a time-zone unaware time point, with the same duration as the original. "Hey, this is not UTC, but a local time zone yet not specified"

---

## Comment 5

> Username: SpareSimian  
> Created at: 2024-05-16 16:42:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2115738993  

That sounds perfect, then.

---

## Comment 6

> Username: anarthal  
> Created at: 2024-05-17 15:50:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2117890421  

Which compiler/OS/stdlib are you using? gcc and clang have currently problems with the `__cpp_lib_chrono` feature test macro. They are missing some of the bits required to define the macro, but still have the `local_time` class.

---

## Comment 7

> Username: SpareSimian  
> Created at: 2024-05-17 17:42:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2118104397  

I'm using VS 2022 with C++20 on Win10x64. I had to switch the default language version to get the newer C++20 chrono features.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-05-18 12:56:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2118817835  

Can you please have a quick look at the #269 PR?

---

## Comment 9

> Username: SpareSimian  
> Created at: 2024-05-18 17:12:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/263#issuecomment-2118884184  

I went through all the commits and it all looks good. Thanks!
