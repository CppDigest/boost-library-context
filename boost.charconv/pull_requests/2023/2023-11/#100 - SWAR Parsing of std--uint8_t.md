# #100 SWAR Parsing of std::uint8_t [Closed]

> Username: mborland  
> Created at: 2023-11-29 10:14:01 UTC  
> Updated at: 2023-11-30 08:28:37 UTC  
> Closed at: 2023-11-30 08:28:37 UTC  
> Url: https://github.com/boostorg/charconv/pull/100  

Closes: #99

---

## Comment 1

> Username: mborland  
> Created_at: 2023-11-29 12:05:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/100#issuecomment-1831768382  

Initial data from `from_chars_integral` benchmark run on M1 Mac  
  
This diff:  
```  
Clang version 17.0.5  
libc++ version 170005  
  
            std::from_chars<signed char>:   432 ms (s=18446744073678854091)  
boost::charconv::from_chars<signed char>:   261 ms (s=18446744073678854091)  
  
            std::from_chars<unsigned char>:   295 ms (s=6379836875)  
boost::charconv::from_chars<unsigned char>:   315 ms (s=6379836875)  
  
            std::from_chars<short>:   516 ms (s=1079401675)  
boost::charconv::from_chars<short>:   295 ms (s=1079401675)  
  
            std::from_chars<unsigned short>:   307 ms (s=1637628009675)  
boost::charconv::from_chars<unsigned short>:   212 ms (s=1637628009675)  
  
            std::from_chars<int>:   801 ms (s=18446704711163993291)  
boost::charconv::from_chars<int>:   499 ms (s=18446704711163993291)  
  
            std::from_chars<unsigned>:   550 ms (s=107400318105705675)  
boost::charconv::from_chars<unsigned>:   359 ms (s=107400318105705675)  
  
            std::from_chars<long>:  1021 ms (s=15336988169399983307)  
boost::charconv::from_chars<long>:   629 ms (s=15336988169399983307)  
  
            std::from_chars<unsigned long>:  1025 ms (s=15336988169399983307)  
boost::charconv::from_chars<unsigned long>:   639 ms (s=15336988169399983307)  
  
            std::from_chars<long long>:  1018 ms (s=15336988169399983307)  
boost::charconv::from_chars<long long>:   629 ms (s=15336988169399983307)  
  
            std::from_chars<unsigned long long>:  1027 ms (s=15336988169399983307)  
boost::charconv::from_chars<unsigned long long>:   636 ms (s=15336988169399983307)  
```  
  
on current develop:  
  
```  
Clang version 17.0.5  
libc++ version 170005  
  
            std::from_chars<signed char>:   427 ms (s=18446744073678854091)  
boost::charconv::from_chars<signed char>:   253 ms (s=18446744073678854091)  
  
            std::from_chars<unsigned char>:   285 ms (s=6379836875)  
boost::charconv::from_chars<unsigned char>:   217 ms (s=6379836875)  
  
            std::from_chars<short>:   504 ms (s=1079401675)  
boost::charconv::from_chars<short>:   285 ms (s=1079401675)  
  
            std::from_chars<unsigned short>:   303 ms (s=1637628009675)  
boost::charconv::from_chars<unsigned short>:   210 ms (s=1637628009675)  
  
            std::from_chars<int>:   779 ms (s=18446704711163993291)  
boost::charconv::from_chars<int>:   486 ms (s=18446704711163993291)  
  
            std::from_chars<unsigned>:   536 ms (s=107400318105705675)  
boost::charconv::from_chars<unsigned>:   349 ms (s=107400318105705675)  
  
            std::from_chars<long>:   989 ms (s=15336988169399983307)  
boost::charconv::from_chars<long>:   609 ms (s=15336988169399983307)  
  
            std::from_chars<unsigned long>:   993 ms (s=15336988169399983307)  
boost::charconv::from_chars<unsigned long>:   609 ms (s=15336988169399983307)  
  
            std::from_chars<long long>:  1004 ms (s=15336988169399983307)  
boost::charconv::from_chars<long long>:   611 ms (s=15336988169399983307)  
  
            std::from_chars<unsigned long long>:   996 ms (s=15336988169399983307)  
boost::charconv::from_chars<unsigned long long>:   622 ms (s=15336988169399983307)  
```  
  
So far this is a decent bit worse.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-11-30 08:28:37 UTC  
> Url: https://github.com/boostorg/charconv/pull/100#issuecomment-1833309345  

Removing the buffer improves things a little bit better, but is still substantially worse than the existing implementation:  
  
```  
            std::from_chars<unsigned char>:   282 ms (s=6379836875)  
boost::charconv::from_chars<unsigned char>:   302 ms (s=6379836875)  
```  
  
I don't think this is worth pursuing further.

---
