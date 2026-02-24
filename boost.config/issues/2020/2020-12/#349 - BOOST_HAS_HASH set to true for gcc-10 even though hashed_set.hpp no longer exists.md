# #349 - BOOST_HAS_HASH set to true for gcc-10 even though hashed_set.hpp no longer exists. [Closed]

> Username: robertramey  
> Created at: 2020-12-17 23:23:34 UTC  
> Updated at: 2020-12-18 11:02:48 UTC  
> Closed at: 2020-12-18 10:58:52 UTC  
> Url: https://github.com/boostorg/config/issues/349  

With the most recent versions of gcc (10) and clang I'm getting an error for the inclusion of "hashed_set.hpp" from the standard library.  The error is that hashed/set/map etc have been deprecated for long enough time that they are not considered errors.  
My jam file contains the line:  
     [ test-bsl-run_files test_set_hashed : A : : [ requires hash ] ] # BOOST_HAS_HASH  
which is meant to suppress the running of this test if the appropropriate SGI header file isn't found.  But now we have the case where the file used to be there but now has been removed.  Yet apparently BOOST_HAS_HASH still is set to true.  Looks like config.hpp needs some updating.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-18 11:02:48 UTC  
> Url: https://github.com/boostorg/config/issues/349#issuecomment-748024288  

It works for me with gcc-10 on Mingw-64, however the headers must be included via `#include BOOST_HASH_SET_HEADER` or `#include BOOST_HASH_MAP_HEADER`, and then the classes accessed via `BOOST_STD_EXTENSION_NAMESPACE::hash_set<>` etc.  
  
The commit above corrects a typo in the docs on this, also adds some logic with __has_include to verify that the headers really do exist before allowing BOOST_HAS_HASH to be set.
