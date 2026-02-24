# #63 - Track axis size on development platform [Closed]

> Username: HDembinski  
> Created at: 2018-07-18 11:25:32 UTC  
> Updated at: 2018-11-28 23:11:48 UTC  
> Closed at: 2018-11-28 23:11:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/63  

It would be nice to track the sizes of all axes, because they should remain as small as possible, so that as many axes as possible fit into the CPU cache. Unfortunately, the sizes are platform-dependent, so it is not possible to write a naive test that checks the size.  
  
```  
// axis sizes (cannot check them, they are platform-dependent)  
{  
     BOOST_TEST(sizeof(axis::regular<>) <= 64);  
     BOOST_TEST(sizeof(axis::circular<>) <= 56);  
     BOOST_TEST(sizeof(axis::variable<>) <= 56);  
     BOOST_TEST(sizeof(axis::integer<>) <= 48);  
     BOOST_TEST(sizeof(axis::category<>) <= 48);  
     BOOST_TEST(sizeof(axis::any_std) <= 80);  
}  
```  
  
One solution would be to write a file with the current sizes on the development platform and then test against that in subsequent runs. This would allow one to track unintended changes at least. Nothing would happen on Travis, where the test is always executed in a pristine environment.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-11-28 23:11:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/63#issuecomment-442641484  

CI testing is not necessary for this and it is also overdoing things. A simple executable was added to develop which shows the various sizes, but only as a check, not a test.
