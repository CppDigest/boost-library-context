# #41 Change handling of ERANGE values to match std::strtod [Merged]

> Username: mborland  
> Created at: 2023-05-17 07:09:39 UTC  
> Updated at: 2023-05-17 15:35:30 UTC  
> Merged at: 2023-05-17 15:34:57 UTC  
> Closed at: 2023-05-17 15:34:57 UTC  
> Url: https://github.com/boostorg/charconv/pull/41  

Closes: #37

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-05-17 07:20:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#issuecomment-1550879527  

An automated preview of the documentation is available at [https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-05-17 07:35:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#issuecomment-1550899712  

An automated preview of the documentation is available at [https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-05-17 08:45:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#issuecomment-1550996178  

An automated preview of the documentation is available at [https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 4

> Username: mborland  
> Created_at: 2023-05-17 12:50:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#issuecomment-1551333719  

@pdimov can you please double check this one? I added a note to our docs saying that we diverge from the standard in case users expect a 1:1 replacement of the STL implementation.

---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2023-05-17 13:47:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/41#pullrequestreview-1430751701  

📁 test/from_chars_float.cpp

```diff
  31 |+     auto r = boost::charconv::from_chars(buffer.c_str(), buffer.c_str() + std::strlen(buffer.c_str()), v, fmt);
  32 |+ 
  33 |+     if (!(BOOST_TEST_EQ(v, expected_value), BOOST_TEST_EQ(r.ec, ERANGE)))
```

> Username: pdimov  
> Created_at: 2023-05-17 13:47:55 UTC  
> Updated_at: 2023-05-17 13:47:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#discussion_r1196553534  

The comma operator discards the value of the first test. I think you want `&&` here?


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2023-05-17 13:49:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/41#pullrequestreview-1430754792  

📁 test/from_chars_float.cpp

```diff
  28 |+ void overflow_spot_value(const std::string& buffer, T expected_value, boost::charconv::chars_format fmt = boost::charconv::chars_format::general)
  29 |+ {
  30 |+     T v = 0;
```

> Username: pdimov  
> Created_at: 2023-05-17 13:49:23 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#discussion_r1196555531  

We need to initialize `v` to something other from 0 here if we're going to check whether we're setting it to 0 in `from_chars`.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-05-17 14:20:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/41#issuecomment-1551488593  

An automated preview of the documentation is available at [https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://41.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
