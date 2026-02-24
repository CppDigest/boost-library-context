# #198 - Support BOOST_UNIT_TEST_FRAMEWORK_DYN_LINK et al [Closed]

> Username: pdimov  
> Created at: 2019-01-22 16:40:38 UTC  
> Updated at: 2019-03-07 21:29:27 UTC  
> Closed at: 2019-03-07 21:29:27 UTC  
> Url: https://github.com/boostorg/test/issues/198  

The CMake config files that `b2 install` installs automatically define `BOOST_${LIBNAME}_DYN_LINK=1` when the user links to the dynamic library $LIBNAME, by means of linking to the Boost::$libname target. This works most of the time, because the target/library name and the macro match, but doesn't for `Boost::unit_test_framework`, `Boost::test_exec_monitor` and `Boost::prg_exec_monitor`, because these use the macro `BOOST_TEST_DYN_LINK` and this doesn't match the target names.  
  
So, would it be possible to make the libraries recognize their individual macros in addition to `BOOST_TEST_DYN_LINK`?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-03-03 19:42:57 UTC  
> Url: https://github.com/boostorg/test/issues/198#issuecomment-469056532  

Currently building on the branch `topic/GH-198-support-boost-cmake-in-b2`. Would be good if you can give a try.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-03-05 15:27:04 UTC  
> Url: https://github.com/boostorg/test/issues/198#issuecomment-469722994  

I can confirm that without the fix, building https://github.com/boostorg/boost_install/tree/develop/test/test with `BUILD_SHARED_LIBS=ON` fails with link errors for missing `main`, but with the fix, it succeeds.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-03-05 20:53:21 UTC  
> Url: https://github.com/boostorg/test/issues/198#issuecomment-469852794  

Very good, thanks for the feedback. Let's try to push that for 1.70.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-03-07 21:29:27 UTC  
> Url: https://github.com/boostorg/test/issues/198#issuecomment-470702059  

In master, should be in 1.70.
