# #217 - Data test cases fail with bool initializer list [Closed]

> Username: ShadowIce  
> Created at: 2019-04-18 10:31:00 UTC  
> Updated at: 2020-01-07 06:44:30 UTC  
> Closed at: 2019-10-21 18:28:13 UTC  
> Url: https://github.com/boostorg/test/issues/217  

If a data test case uses boolean values directly passed to make in the BOOST_DATA_TEST_CASE macro the resulting value will be more or less random. This happens when compiling it on macOS / clang 10.0.1 / boost 1.7.0, but not on Windows with VS2017. It also doesn't happen with any other data types except bool.  
  
```C++  
constexpr bool ExpectedValues[] = { false, true, true, true, false, false};  
BOOST_DATA_TEST_CASE(BoostDataTest  
	, boost::unit_test::data::make({ false, true, true, true, false, false }) ^  
	  boost::unit_test::data::make(ExpectedValues)  
	, value, expectedValue)  
{  
	BOOST_TEST(value == expectedValue);  
}  
```  
  
It doesn't happen if I rewrite it like this:  
```C++  
constexpr bool ExpectedValues[] = { false, true, true, true, false, false};  
auto DataValues = boost::unit_test::data::make({ false, true, true, true, false, false });  
BOOST_DATA_TEST_CASE(BoostDataTest2  
	,  DataValues ^  
	  boost::unit_test::data::make(ExpectedValues)  
	, value, expectedValue)  
{  
	BOOST_TEST(value == expectedValue);  
}  
```

---

## Comment 1

> Username: b-spencer  
> Created at: 2019-10-07 17:52:45 UTC  
> Url: https://github.com/boostorg/test/issues/217#issuecomment-539129326  

This appears to happen because the `boost::unit_test::data::monomorphic::init_list<bool>` specialization that captures the data set uses a `vector<bool>` itself.  Thus, its iterators dereference to a proxy type that only references the original value from the initial data set, and it is those proxies that get captured in the bound function during test setup.  
  
One fix that seems to work is to introduce a wrapper structure inside `init_list<bool>` with a bool conversion operator, and then store the data set in a vector of that instead.  Its iterators return non-proxy values, and then it should work.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-10-07 22:00:23 UTC  
> Url: https://github.com/boostorg/test/issues/217#issuecomment-539222760  

Thanks for the hints, it helped a lot. I've made a fix in the branch `topic/GH-217-init-list-boolean`, it would be great if you can test it.

---

## Comment 3

> Username: b-spencer  
> Created at: 2019-10-09 11:39:31 UTC  
> Url: https://github.com/boostorg/test/issues/217#issuecomment-539963153  

Great!  I will take a look and try it.  It might be a few days, though.

---

## Comment 4

> Username: b-spencer  
> Created at: 2019-10-15 15:48:29 UTC  
> Url: https://github.com/boostorg/test/issues/217#issuecomment-542279663  

The fix on that topic branch seems to work for me.  Thanks!

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-10-21 18:28:13 UTC  
> Url: https://github.com/boostorg/test/issues/217#issuecomment-544645702  

In master, closing.
