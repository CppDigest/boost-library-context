# #269 - Deprecated bind placeholders in tests [Closed]

> Username: pabristow  
> Created at: 2020-05-04 16:14:29 UTC  
> Updated at: 2022-02-27 19:46:35 UTC  
> Closed at: 2020-05-17 10:05:15 UTC  
> Url: https://github.com/boostorg/test/issues/269  

I am  using #include <boost/test/included/unit_test.hpp> // Boost.Test  
  
but get a complaint from bind.hpp.  
  
#if !defined(BOOST_BIND_GLOBAL_PLACEHOLDERS)  
  
BOOST_PRAGMA_MESSAGE(  
  "The practice of declaring the Bind placeholders (_1, _2, ...) "  
  "in the global namespace is deprecated. Please use "  
  "<boost/bind/bind.hpp> + using namespace boost::placeholders, "  
  "or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior."  
)  
  
I suspect that Boost.Test might be the cause, although I can't find the actual include of bind.  
  
I don't get this when using the library version  
  
#include <boost/test/unit_test.hpp> // Boost.Test  
  
Is this a bug or a feature?  
  
Is there anything I should do to suppress this message?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-05-06 11:31:44 UTC  
> Url: https://github.com/boostorg/test/issues/269#issuecomment-624595814  

Normally any calls to the deprecated API of boost.bind have been removed (#259) . Would you mind pre-processing your test file and check what is including bind.hpp ?

---

## Comment 2

> Username: pabristow  
> Created at: 2020-05-07 14:12:11 UTC  
> Url: https://github.com/boostorg/test/issues/269#issuecomment-625280236  

I didn't find pre-processing to be very helpful - needle in haystack!  
  
But using a search for "boost/bind.hpp" in /test I found a dozen examples in .cpp , but one in .ipp I:\boost\libs\test\include\boost\test\impl\framework.ipp and test_case.hpp  
  
I patched my local copy to #include <boost/bind/bind.hpp> and the warning went away.  
  
HTH  
  
Paul

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-05-07 22:41:18 UTC  
> Url: https://github.com/boostorg/test/issues/269#issuecomment-625533080  

You mean this line? https://github.com/boostorg/test/blob/develop/include/boost/test/impl/framework.ipp#L52   
  
If I am not mistaken (I have not checked the 1.73 archive thoroughly) this is fixed in 1.73. Are you using the last version?

---

## Comment 4

> Username: pabristow  
> Created at: 2020-05-08 09:00:39 UTC  
> Url: https://github.com/boostorg/test/issues/269#issuecomment-625716727  

I think I have been under the delusion that all branches were up to date with develop branch. I have done a boost module update of all branches recently, but I fear that something went wrong with updating the sub module test and I didn't notice.  
  
Now that I have updated test, the correct #include <boost/bind/bind.hpp> is used.  
  
However there are 5 Boost.Test examples still using #include <boost/bind.hpp>   
  
Sorry for the noise.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2020-05-08 09:03:19 UTC  
> Url: https://github.com/boostorg/test/issues/269#issuecomment-625717785  

> Now that I have updated test, the correct #include <boost/bind/bind.hpp> is used.  
  
Great!  
  
> However there are 5 Boost.Test examples still using #include <boost/bind.hpp>  
  
Yes I saw that and I will correct for those (leaving the ticket open for that precisely).  
  
> Sorry for the noise.  
  
No worries, thank you for the quick update and for continuing enjoying Boost.Test! I still have on my TODO list the remarks you gave about the documentation.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2020-05-17 10:05:15 UTC  
> Url: https://github.com/boostorg/test/issues/269#issuecomment-629772978  

Fixed in master
