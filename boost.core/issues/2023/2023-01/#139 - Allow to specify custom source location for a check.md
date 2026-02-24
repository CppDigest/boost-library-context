# #139 - [lwt] Allow to specify custom source location for a check [Open]

> Username: Lastique  
> Created at: 2023-01-23 00:17:45 UTC  
> Updated at: 2023-01-23 12:11:31 UTC  
> Url: https://github.com/boostorg/core/issues/139  

Suppose we have a common utility class that performs a check on the passed value:  
  
```  
template< typename T >  
class checker  
{  
    T& m_expected;  
  
public:  
    void operator() (T const& res) const noexcept  
    {  
        BOOST_TEST_EQ(res, m_expected);  
    }  
};  
```  
  
and then we use this class in multiple tests with different types and expected values.  
  
```  
void check_stuff()  
{  
    checker<int> checker1(10);  
    checker1(20);  
  
    checker<long> checker2(30);  
    checker2(40);  
}  
```  
  
The problem is if any of these checks fail, the output always shows the location of the `BOOST_TEST_EQ` line, not the location of the particular use of `checker`.  
  
Proposed solution 1:  
  
Make `test_with_impl` API public, so that the user is able to define his own `BOOST_TEST_EQ`-like macro that will pass a custom source location. This leaves the implementation of such source location tracking mechanism to the user.  
  
Proposed solution 2:  
  
Add a new set of macros that will use a custom source location that was saved previously by a new macro (let's call it `BOOST_TEST_CHECKPOINT`). The user will use `BOOST_TEST_CHECKPOINT` to save the location of the original use of the `checker`. Alternatively (or additionally) we could provide a non-macro API to the same effect. Usage example:  
  
```  
void check_stuff()  
{  
    checker<int> checker1(10); BOOST_TEST_CHECKPOINT;  
    checker1(20);  
  
    checker<long> checker2(30); BOOST_TEST_CHECKPOINT;  
    checker2(40);  
}  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-23 01:25:46 UTC  
> Url: https://github.com/boostorg/core/issues/139#issuecomment-1399675864  

We can also do (2) without the need for the checkpoint:  
```  
template< typename T >  
class checker  
{  
    T& m_expected;  
  
public:  
    void operator() (T const& res, boost::source_location const& loc = BOOST_CURRENT_LOCATION) const noexcept  
    {  
        BOOST_TEST_EQ_SL(res, m_expected, loc);  
    }  
};  
```  
I'm not sure whether we'll be able to overload BOOST_TEST_EQ to avoid the need for separate macros. C++03 will be a problem for sure.  
  
Maybe Glen the Core maintainer can ask Glen the release manager when Boost will move to C++14 as the baseline requirement. :-)

---

## Comment 2

> Username: Lastique  
> Created at: 2023-01-23 12:05:10 UTC  
> Updated at: 2023-01-23 12:11:31 UTC  
> Url: https://github.com/boostorg/core/issues/139#issuecomment-1400233898  

In my case, I will need a checkpoint-like approach anyway since the checker is not called directly by the test code. But a set of macros that accept a custom location will work. I don't think we need to raise the minimum C++ version for that.
