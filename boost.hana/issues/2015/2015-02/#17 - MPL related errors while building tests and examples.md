# #17 - MPL related errors while building tests and examples [Closed]

> Username: calincru  
> Created at: 2015-02-27 06:59:38 UTC  
> Updated at: 2015-03-24 17:59:06 UTC  
> Closed at: 2015-03-24 17:59:06 UTC  
> Url: https://github.com/boostorg/hana/issues/17  

Hi!  
  
I was trying to build the tests and the examples and I came across these errors and I cannot figure out whether it is because of my version of boost libs or something else causes them.  
  
I am on Ubuntu 14.04 x64, with clang 3.7 and libcxx (both) trunk - updated yesterday, boost trunk as well. Therefore, the only explanation I find for these errors is that you may have an unofficial/patched version of boost headers.  
  
Firstly, in **example/tutorial/type_computations.cpp**, the following piece of code does not compile:  
  
``` c++  
static_assert(mpl::equal<                                                          
       apply_to_all<types, mpl::quote1<std::add_pointer>>::type,                      
       mpl::vector<                                                                   
           mpl::vector<int*, char*>,                                                  
           mpl::vector<void*, std::string*, double*>                                  
       >,                                                                             
  
       // mpl::equal was seemingly not designed for deep comparisons, so we           
       // need this tricky line for it to do what we want.                            
       mpl::equal<mpl::_1, mpl::_2, mpl::quote2<std::is_same>>                        
   >{}, "");  
```  
  
Replacing the instantiation`>{}` with `>::type::value` makes it compile.  
  
Secondly, I am getting the following errors [[0]](http://pastebin.com/ECRa16gW) [[1]](http://pastebin.com/th2BrPpU) when further building the tests and the examples (the output is too lengthy).  
  
What am I missing? Thanks!  
  
P.S.: Sorry if it is some mistake from my side, but I really want to compile them successfully.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-02-27 17:56:11 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76440561  

> Firstly, in example/tutorial/type_computations.cpp, the following piece of code does not compile:  
  
I find this strange, as it compiles fine for me. My clang is a couple of days old though, so I'll update it and check again. Could you please pastebin the error you're getting?  
  
> Secondly, I am getting the following errors [0] [1] when further building the tests and the examples (the output is too lengthy).  
  
For [0], make sure you are using the latest Boost. What I think is happening is that you also have a system Boost installed, and it is that one that gets picked up. When setting up Hana with CMake, it should look like:  
  
```  
cmake .. -DCMAKE_CXX_COMPILER=path-to-clang-trunk -DBOOST_ROOT=path-to-boost -DBoost_NO_SYSTEM_PATHS  
```  
  
Note that Boost_NO_SYSTEM_PATHS is not required, but I suggest you still do it just to make sure system paths are not searched at all. With the latest Boost, all the tests in test/ext/fusion/\* should compile, but none should link. This is a bug in Clang. Also note that these tests are incredibly hungry on memory and time, so you might as well just skip them (you can just delete the files and regenerate CMake with `cmake build`). There's also the test.seq.monad_plus_mcd test that should not link; Clang bug too.  
  
For [1], it is a known problem that I was supposed to fix for a while. I just fixed it in 7e2bcdf; thanks for pushing me to finally do it :-).  
  
Please update your Hana and let me know if you still get errors.

---

## Comment 2

> Username: calincru  
> Created at: 2015-03-01 10:33:03 UTC  
> Updated at: 2015-03-01 12:58:19 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76591236  

Thanks for dealing with those errors. It finally works except for the first one I mentioned. However, I had to delete some tests which took too long to compile (everything in the `fusion/` dir and `test/seq/sequence.monad_plus_mcd.cpp` and `test/seq/sequence.prepend_mcd.cpp` - the last two may be because my machine's memory is only 4GB).  
  
> I find this strange, as it compiles fine for me. My clang is a couple of days old though, so I'll update it and check again. Could you please pastebin the error you're getting?  
  
The error I'm getting when building `example/tutorial/type_computations.cpp` is:  
  
``` c++  
home/calin/Workplace/hana/example/tutorial/type_computations.cpp:124:15: error:   
      static_assert expression is not an integral constant expression  
static_assert(mpl::equal<  
              ^~~~~~~~~~~  
/home/calin/Workplace/hana/example/tutorial/type_computations.cpp:124:15: note: non-constexpr  
      function 'operator bool' cannot be used in a constant expression  
/home/calin/Workplace/boost-trunk/boost/mpl/bool.hpp:29:5: note: declared here  
    operator bool() const { return this->value; }  
    ^  
```  
  
The error is pretty self-explanatory. The `operator bool()` in my version of boost libs (the trunk from two days ago) of `struct bool_` is not `constexpr`. However, I haven't understood all the underneath details, so it may be a not so obvious problem and the error clang gives may be misleading.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-03-01 22:33:21 UTC  
> Updated at: 2015-03-02 03:40:06 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76635400  

> Thanks for dealing with those errors. It finally works except for the first one I mentioned.   
  
See below.  
  
> However, I had to delete some tests which took too long to compile (everything in the fusion/ dir and test/seq/sequence.monad_plus_mcd.cpp and test/seq/sequence.prepend_mcd.cpp - the last two may be because my machine's memory is only 4GB).  
  
I know this is a pain. I'm currently working on splitting those tests into several files so they do not crash my own computer.  
  
> The error I'm getting when building example/tutorial/type_computations.cpp is:  
> [...]  
> The error is pretty self-explanatory. The operator bool() in my version of boost libs (the trunk from two days ago) of struct bool_ is not constexpr. However, I haven't understood all the underneath details, so it may be a not so obvious problem and the error clang gives may be misleading.  
  
Hmm, this is weird since in my version of Boost, MPL `bool_` is defined as  
  
```  
template< bool C_ > struct bool_  
{  
    BOOST_STATIC_CONSTANT(bool, value = C_);  
    typedef integral_c_tag tag;  
    typedef bool_ type;  
    typedef bool value_type;  
    BOOST_CONSTEXPR operator bool() const { return this->value; }  
};  
```  
  
`BOOST_CONSTEXPR` should expand to `constexpr` if you are in C++11. There might be a problem with Boost.Config and `BOOST_CONSTEXPR` isn't set properly on your platform? Can you   
1. make sure that your definition of bool_ matches what's above in your Boost trunk  
2. tell me whether the following code compiles for you:  
  
```  
#include <boost/config.hpp>  
BOOST_CONSTEXPR int i = 0;  
static_assert(i == 0, "this will fail if BOOST_CONSTEXPR does not expand to constexpr");  
int main() { }  
```  
  
Note that I could just change the example so it compiles for you, but I'd rather understand what's happening instead of blindly fixing the error.

---

## Comment 4

> Username: calincru  
> Created at: 2015-03-02 08:04:40 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76671705  

I tested the following code and the result is that the first assert passes while the second one does not.  
  
``` C++  
   #include <boost/config.hpp>                                                        
   #include <boost/mpl/equal.hpp>                                                     
  
  
   int main()                                                                         
   {                                                                                  
       BOOST_CONSTEXPR int i = 0;                                                     
       static_assert(i == 0, "");                                                     
  
       static_assert(boost::mpl::equal<int, int>{}, "");                              
       return 0;                                                                      
   }  
```  
  
The definition of `struct bool_` in my Boost.MPL is exactly the same as it seems to be in the trunk. Check out [this](http://svn.boost.org/svn/boost/trunk/boost/mpl/bool.hpp).  
  
It makes sense to be defined as `constexpr` when compiling with C++11 but I don't get why it is not (as you can see from the link from above).  
  
> Note that I could just change the example so it compiles for you, but I'd rather understand what's happening instead of blindly fixing the error.  
  
No need to do that, I also want to understand why it does not work. :)

---

## Comment 5

> Username: ldionne  
> Created at: 2015-03-02 12:57:11 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76707157  

I'm not sure, but is it possible that the SVN is not updated anymore and so it would not contain the latest changes in MPL? My Boost is a clone from the GitHub modularized version. The problem really seems to be that we are using different Boost versions.

---

## Comment 6

> Username: calincru  
> Created at: 2015-03-02 13:57:49 UTC  
> Updated at: 2015-03-02 14:00:09 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76715305  

You are right, the SVN repository seems to not be update anymore. I'm installing the modular boost from git right now and that example which was failing should build successfully this time.  
  
Even if it is not related to the errors I previously encountered, I had another problem while building Hana and I was wondering if the way I solved it may break the build process on other systems or not. Since I am on Ubuntu, clang links by default to **libstdc++**. Therefore, to be able to build the tests and examples, I had to make the following changes in the **CMakeLists.txt** file.  
  
``` patch  
diff --git a/CMakeLists.txt b/CMakeLists.txt  
index 1a03c6b..716c592 100644  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -123,6 +123,12 @@ function(boost_hana_add_executable target_name)  
         set_property(TARGET ${target_name}  
             APPEND PROPERTY INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIRS})  
     endif()  
+  
+    target_link_libraries(  
+        ${target_name}  
+        c++  
+        c++abi  
+    )  
 endfunction()  
  
 #   boost_hana_add_test(<source file> [<target name>])  
```  
  
So every time I'm compiling something using the _-std=c++14_ flag, I also have to use the _-lc++_ and _-lc++abi_ linker flags, since clang compiled code doesn't always successfully link to libstdc++ (or this library is a little bit behind with the C++14 features implementation).  
  
My question is, would making these changes break the build process on Mac OSs (I have read that on post-Mavericks Macs, the default std implementation chosen by ld is libc++)? I guess that is the OS you are using since you haven't had problems with that. If it wouldn't, I guess that this change would make possible the building of tests and examples from Hana on any Linux based distribution.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-03-03 16:06:26 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76974687  

Hmm. I'm having trouble linking to the right libc++ too on OSX. Basically, Hana tests are being linked to my system-provided libc++, but I would need them linked with my tip-of-trunk libc++ that comes with my tip-of-trunk clang. clang is passed `stdlib=libc++` (that's set by the CMakeLists), but it does not seem to affect the linker. I'll try to sort this out (but this is so booooring to do :-).

---

## Comment 8

> Username: calincru  
> Created at: 2015-03-03 17:11:46 UTC  
> Updated at: 2015-03-03 17:12:06 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-76988700  

> I'll try to sort this out (but this is so booooring to do :-).  
  
I understand :). However, I am glad I managed to build Hana and I cannot wait to start using it as well as delving into its implementation details as I am also interested in meta-programming and I think your work is impressive.  
  
Thanks for dealing with those building errors.

---

## Comment 9

> Username: ldionne  
> Created at: 2015-03-05 14:01:24 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-77367913  

I'm unable to run the examples on Ubuntu 14.10 because I get the following error:  
  
``` shell  
    error while loading shared libraries: libc++abi.so.1: cannot open shared object file: No such file or directory  
```  
  
I'm reopenning the issue as a reminder to fix this (and also your link errors).

---

## Comment 10

> Username: ldionne  
> Created at: 2015-03-24 17:59:06 UTC  
> Url: https://github.com/boostorg/hana/issues/17#issuecomment-85622016  

I'm now able to compile and run all the examples on ubuntu 14.04, and the Travis VMs (ubuntu 12.xx) as well. I now link with libc++ when I create the example and test executables. I guess that'll have to be changed once we support other standard libraries, but for now I guess it's good enough. Closing.
