# #551 - Bug in boost::compute::iota [Closed]

> Username: jszuppe  
> Created at: 2016-01-14 21:49:59 UTC  
> Updated at: 2016-01-24 14:04:10 UTC  
> Closed at: 2016-01-24 14:04:08 UTC  
> Url: https://github.com/boostorg/compute/issues/551  

``` cpp  
BOOST_AUTO_TEST_CASE(iota_uint)  
{  
    bc::vector<bc::uint_> vector(4, context);  
    bc::iota(vector.begin(), vector.end(), bc::uint_(0), queue);  
    CHECK_RANGE_EQUAL(bc::uint_, 4, vector, (0, 1, 2, 3));  
  
    bc::iota(vector.begin(), vector.end(), bc::uint_(-1), queue);  
    CHECK_RANGE_EQUAL(bc::uint_, 4, vector, (bc::uint_(-1), 0, 1, 2));  
}  
  
BOOST_AUTO_TEST_CASE(iota_char)  
{  
    bc::vector<bc::char_> vector(4, context);  
    bc::iota(vector.begin(), vector.end(), bc::uint_(0), queue);  
    CHECK_RANGE_EQUAL(bc::char_, 4, vector, (0, 1, 2, 3));  
}  
  
BOOST_AUTO_TEST_CASE(iota_uchar)  
{  
    bc::vector<bc::uchar_> vector(4, context);  
    bc::iota(vector.begin(), vector.end(), bc::uint_(0), queue);  
    CHECK_RANGE_EQUAL(bc::uchar_, 4, vector, (0, 1, 2, 3));  
}  
```  
  
Those test fail when added to `test_iota.cpp`. Everything works fine when value parameter in `boost::compute::iota()` is greater than 0.

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-01-19 04:54:23 UTC  
> Url: https://github.com/boostorg/compute/issues/551#issuecomment-172736488  

Fixed in PR #552.
