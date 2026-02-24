# #119 - Regression in recursive_directory_iterator [Closed]

> Username: laudrup  
> Created at: 2019-09-17 12:43:02 UTC  
> Updated at: 2019-09-18 08:01:44 UTC  
> Closed at: 2019-09-18 08:01:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/119  

There seems to be a regression introduced in `recursive_directory_iterator` with the release of boost 1.70.  
  
It can be fairly easily reproduced by running `tut6b.cpp` in the examples directory, which crashes for me with the following backtrace:  
  
```  
#0  0x000055555555caa0 in boost::intrusive_ptr<boost::filesystem::detail::dir_itr_imp>::get (this=0xfffffffffffffff8) at thirdparty/boost/boost_external-prefix/include/boost/smart_ptr/intrusive_ptr.hpp:181  
#1  0x000055555555ba2c in boost::filesystem::directory_iterator::dereference (this=0xfffffffffffffff8) at thirdparty/boost/boost_external-prefix/include/boost/filesystem/operations.hpp:1014  
#2  0x000055555555dd00 in boost::iterators::iterator_core_access::dereference<boost::filesystem::directory_iterator> (f=...) at thirdparty/boost/boost_external-prefix/include/boost/iterator/iterator_facade.hpp:550  
#3  0x000055555555ce88 in boost::iterators::detail::iterator_facade_base<boost::filesystem::directory_iterator, boost::filesystem::directory_entry, boost::iterators::single_pass_traversal_tag, boost::filesystem::directory_entry&, long, false, false>::operator* (this=0xfffffffffffffff8)  
    at thirdparty/boost/boost_external-prefix/include/boost/iterator/iterator_facade.hpp:656  
#4  0x00005555555683b8 in boost::iterators::detail::iterator_facade_base<boost::filesystem::directory_iterator, boost::filesystem::directory_entry, boost::iterators::single_pass_traversal_tag, boost::filesystem::directory_entry&, long, false, false>::operator-> (this=0xfffffffffffffff8)  
    at ./boost/iterator/iterator_facade.hpp:661  
#5  0x00005555555666e3 in boost::filesystem::detail::recur_dir_itr_imp::push_directory (this=0x55555558be70, ec=...) at libs/filesystem/src/operations.cpp:2571  
#6  0x00005555555668f4 in boost::filesystem::detail::recur_dir_itr_imp::increment (this=0x55555558be70, ec=0x0) at libs/filesystem/src/operations.cpp:2609  
#7  0x000055555555be40 in boost::filesystem::recursive_directory_iterator::increment (this=0x7fffffffddd8) at thirdparty/boost/boost_external-prefix/include/boost/filesystem/operations.hpp:1345  
#8  0x000055555555ddf2 in boost::iterators::iterator_core_access::increment<boost::filesystem::recursive_directory_iterator> (f=...) at thirdparty/boost/boost_external-prefix/include/boost/iterator/iterator_facade.hpp:556  
#9  0x000055555555cfe6 in boost::iterators::detail::iterator_facade_base<boost::filesystem::recursive_directory_iterator, boost::filesystem::directory_entry, boost::iterators::single_pass_traversal_tag, boost::filesystem::directory_entry&, long, false, false>::operator++ (this=0x7fffffffddd8)  
    at thirdparty/boost/boost_external-prefix/include/boost/iterator/iterator_facade.hpp:666  
#10 0x0000555555559993 in main (argc=2, argv=0x7fffffffdf18) at ../tut6b.cpp:34  
```  
  
The same code works just fine with boost-1.69. The same issue is present in boost 1.71 as well as the master branch. The issue is not present on Windows.  
  
It seems like commit 3a9069438307f560822c44f3bada1fa9a78fc589 introduced the regression.  
  
I have tried to see if I could find the exact cause of the bug without luck.  
  
Please let me know if you need any more information or if there's something more I should try.  
  
Thanks a lot.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-09-17 13:19:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/119#issuecomment-532216988  

I cannot reproduce the crash neither with the current develop nor with 1.70. Commit 3a9069438307f560822c44f3bada1fa9a78fc589 did not have any functional changes to the code, just moving the implementation into the library and enum/constexpr cleanup.  
  
What compiler are you using? What directory structure are you running the test on? Does it reproduce with develop?

---

## Comment 2

> Username: laudrup  
> Created at: 2019-09-17 14:22:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/119#issuecomment-532243600  

This is weird indeed.  
  
We are building boost and filesystem using our own CMake rules and everything has been working just fine, apart from this single issue (which only showed up after the code cleanup commit), so I was absolutely sure there was some kind of regression.  
  
If I checkout the boost sources and build things the official way, I am not able to reproduce this either, so it must be some flag we are giving the compiler (gcc or clang, doesn't matter) or linker that causes this.  
  
I should of course have made sure that I could reproduce this without these additional potential error sources (our build system), it just never occurred to me.  
  
I will figure out what is going on and if this has any relevance for boost::filesystem I'll let you know. Otherwise I'll close this issue ASAP.  
  
Sorry for the confusion and thanks a lot for you great work.

---

## Comment 3

> Username: laudrup  
> Created at: 2019-09-18 08:01:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/119#issuecomment-532569480  

I figured out what was wrong.  
  
I turned out that when building our code in debug mode, we add the `_GLIBCXX_DEBUG` flag when building our own code, but not when building boost. When using that macro, [it should be enabled for all translation units](https://gcc.gnu.org/onlinedocs/libstdc%2B%2B/manual/debug_mode_using.html) which was not the case for us.  
  
That would explain why this refactoring commit triggered the issue (it moved some code from a header to a compiled library) and I guess it was just poor luck (good or bad) that we hadn't hit this issue before.  
  
So in short, nothing to do with `boost::filesystem`, just thought I owed you an explanation.  
  
Sorry for the inconvenience and once again thanks for your great work with C++.
