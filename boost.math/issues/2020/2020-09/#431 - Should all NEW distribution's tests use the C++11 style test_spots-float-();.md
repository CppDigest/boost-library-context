# #431 - Should all NEW distribution's tests use the C++11 style   test_spots<float>(); [Closed]

> Username: pabristow  
> Created at: 2020-09-03 16:18:38 UTC  
> Updated at: 2020-09-03 16:59:55 UTC  
> Closed at: 2020-09-03 16:59:55 UTC  
> Url: https://github.com/boostorg/math/issues/431  

Now that we are requiring C++11, or higher, can we (for all NEW distributions) leave behind the historic (2005-ish) but perhaps puzzling (and perhaps warning triggering) test functions like  
  
`template <typename RealType> // Any floating-point type RealType.  
void test_spots(RealType){...}`  
  
called as   
  
`test_spots(0.0F); // Test float.`  
  
(now that compilers work properly) in favour of  
  
`template <typename RealType> // Any floating-point type RealType.  
void test_spots()`  
  
called thus:  
  
`test_spots<float>();`  
  
(I don't propose changing all the old distributions!)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-09-03 16:59:55 UTC  
> Url: https://github.com/boostorg/math/issues/431#issuecomment-686625632  

Yes.  It's not even C++11, it's C++03 but back in the day several compilers couldn't cope with it :(
