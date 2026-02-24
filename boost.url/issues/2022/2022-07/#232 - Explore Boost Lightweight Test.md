# #232 - Explore Boost Lightweight Test [Closed]

> Username: vinniefalco  
> Created at: 2022-07-16 23:55:18 UTC  
> Updated at: 2022-08-23 22:23:35 UTC  
> Closed at: 2022-08-23 22:23:35 UTC  
> Url: https://github.com/boostorg/url/issues/232  

We might port the `debug_stream` to lightweight test using `BOOST_LIGHTWEIGHT_TEST_OSTREAM`. And we would need `checkpoint`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-07-18 14:30:35 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1187573003  

> We might port the debug_stream to lightweight test using BOOST_LIGHTWEIGHT_TEST_OSTREAM.   
  
This seems easy.  
  
> And we would need checkpoint.  
  
This seems more intrusive. The check would need to go into the implementation in `::boost::detail::test_impl`.  
  
The least intrusive thing I can think of is redefining all macros to do what they already do and then an extra command to check the checkpoint after that.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-18 14:44:33 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1187589952  

Also we need the front-end that lets you choose which test suites to run from the command line. And the suites need names.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-07-18 14:53:52 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1187601471  

We would probably be modifying Boost Lightweight Test until we have what we already have in `test_suite.hpp` now.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-07-18 16:02:03 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1187680731  

Then we should fully port the support for the macros like `BOOST_EQUAL_NE`. I only copied part of it but we need all of it or else it can cause errors.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-07-19 01:51:02 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1188506987  

> Then we should fully port the support for the macros like BOOST_EQUAL_NE.   
  
I've already ported `BOOST_EQUAL_NE` as well as I could at https://github.com/CPPAlliance/url/commit/999dbe147be213da73891ff71bfda0a290199aad  
  
The adaptation is quite similar. The only relevant difference in the adaptation is that lightweight test directly relies on the templates at  
  
https://github.com/boostorg/core/blob/8503c536dc38da4bda16a2c70e27bfe045b535d4/include/boost/core/lightweight_test.hpp#L302  
  
while the `test_suite.hpp` calls a virtual virtual function  
  
https://github.com/CPPAlliance/url/blob/b2c35aa11545d5e51c1d499b0070082eaff0be3e/extra/include/test_suite.hpp#L503  
  
, so the templated logic needs to come before the calling the virtual members.  
  
Is there anything in particular we still need to port?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-07-26 14:31:25 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1195561717  

That is nice work but I have concerns about adding more virtual functions. The idea is that there is supposed to be just one each of `pass` and `fail` and they handle all cases, so that the behavior of the test runner can be easily customized. If we have to keep adding more overloads just to handle better reporting, that defeats the purpose of having a unified API for reporting failures. Is there a way to do this using the existing pass/fail functions?

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-07-26 14:40:35 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1195572712  

> That is nice work but I have concerns about adding more virtual functions.   
  
Yes. I didn't like that either. I was the only way I could adapt the boost lightweight test to our tests. It's also kind of bad that we have to store the string, etc... instead of streaming the error in place.  
  
> Is there a way to do this using the existing pass/fail functions?  
  
There might be a way but I couldn't find any. Maybe if the pass and fail functions take a reference to some other class as a parameter, we could derive from this other class to represent the case where we have a message to display. However, this could be just pushing the problem up one level.  
  
In terms of features, we have "boost lightweight test < boost.url test_suite.hpp < boost test". The boost.url test_suite.hpp has only a few features on top of boost lightweight test. But it's not a good idea to include these few features on boost lightweight test because it wouldn't serve its purpose.   
  
What would be the cost of using a subset of headers from boost.test?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-07-26 14:59:15 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1195596851  

> What would be the cost of using a subset of headers from boost.test?  
  
Hmm I don't like that very much, or do you mean Boost Lightweight Test?

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-07-26 15:02:52 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1195601406  

> or do you mean Boost Lightweight Test?  
  
I don't mind using Boost Lightweight Test either. But it doesn't have the features you want.  
  
Boost Lightweight Test is too little. Boost Test is too much. The only reasonable solutions I see are the current Boost.Url test_suite or Boost Test only including what we use.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-08-23 22:23:35 UTC  
> Url: https://github.com/boostorg/url/issues/232#issuecomment-1224950419  

I updated our test_suite framework to address all the points raised here.
