# #214 - testNumberRoundTrips has disabled code [Closed]

> Username: vinniefalco  
> Created at: 2020-08-27 20:13:37 UTC  
> Updated at: 2020-09-03 20:05:36 UTC  
> Closed at: 2020-09-03 20:05:36 UTC  
> Url: https://github.com/boostorg/json/issues/214  

`testNumberRoundTrips` in serializer.cpp has tests which are commented out

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-28 01:17:29 UTC  
> Url: https://github.com/boostorg/json/issues/214#issuecomment-682270589  

This is already done in `parser.cpp`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-28 01:40:33 UTC  
> Url: https://github.com/boostorg/json/issues/214#issuecomment-682276831  

If the commented out tests are redundant we should remove them. But how can that be, if one is in the serializer and the other is in the parser? What happens when we uncomment?

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-08-28 01:52:54 UTC  
> Url: https://github.com/boostorg/json/issues/214#issuecomment-682280077  

Round trip implies that when we parse then serialize, we get the same thing. `parser.cpp` does just that :)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-08-28 02:13:29 UTC  
> Url: https://github.com/boostorg/json/issues/214#issuecomment-682285394  

Look more closely at the tests:  
```  
        //BOOST_TEST(parse("-0.0").as_double() == -0);  
        //BOOST_TEST(parse("-0").as_int64() == 0);  
        //BOOST_TEST(to_string(parse("0.0")) == "0");  
        //BOOST_TEST(to_string(parse("-0.0")) == "-0.0");  
  
        // VFALCO Peter is unsure what this should do  
        //BOOST_TEST(to_string(parse("-0")) == "-0");  
```  
  
So you're saying parser.cpp checks `parse("-0.0").as_double() == -0` ?  
And it checks `to_string(parse("0.0")) == "0"` ?
