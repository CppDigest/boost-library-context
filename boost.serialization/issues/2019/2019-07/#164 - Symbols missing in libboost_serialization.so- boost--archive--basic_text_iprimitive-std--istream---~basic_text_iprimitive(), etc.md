# #164 - Symbols missing in libboost_serialization.so: boost::archive::basic_text_iprimitive<std::istream>::~basic_text_iprimitive(), etc. [Closed]

> Username: yurivict  
> Created at: 2019-07-16 08:35:43 UTC  
> Updated at: 2021-07-22 16:56:25 UTC  
> Closed at: 2019-10-19 16:32:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/164  

While building dakota-6.10.0 on FreeBSD 12 with gcc8 and boost-libs-1.70.0_1, I got these missing symbols:  
```  
[ 79%] Linking CXX executable surfpack  
cd /usr/ports/math/dakota/work/.build/packages/surfpack/interface && /usr/local/bin/cmake -E cmake_link_script CMakeFiles/surfpack_exe.dir/link.txt --verbose=1  
/usr/local/bin/g++8  -O2 -pipe -fno-omit-frame-pointer  -fstack-protector-strong -Wl,-rpath=/usr/local/lib/gcc8 -fno-omit-frame-pointer  -Wl,-rpath=/usr/local/lib/gcc8 -O2 -pipe -fno-omit-frame-pointer  -fstack-protector-strong -Wl,-rpath=/usr/local/lib/gcc8 -fno-omit-frame-pointer  -Wl,-rpath=/usr/local/lib/gcc8   -lexecinfo -Wl,-rpath=/usr/local/lib/gcc8  -L/usr/local/lib/gcc8 -B/usr/local/bin -fstack-protector-strong -Wl,-rpath=/usr/local/lib/gcc8 -L/usr/local/lib/gcc8 CMakeFiles/surfpack_exe.dir/main.cpp.o  -o surfpack  -L/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/remainder/src  -L/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/core/src  -L/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/numerics/src  -L/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/comm/src  -L/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/parameterlist/src  -L/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/parser/src  -L/usr/ports/math/dakota/work/.build/packages/surfpack/src/interpreter -Wl,-rpath,/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/remainder/src:/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/core/src:/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/numerics/src:/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/comm/src:/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/parameterlist/src:/usr/ports/math/dakota/work/.build/packages/external/trilinos/packages/teuchos/parser/src:/usr/ports/math/dakota/work/.build/packages/surfpack/src/interpreter:/usr/ports/math/dakota/work/.build/packages/surfpack/src:/usr/ports/math/dakota/work/.build/packages/external/NCSUOpt:/usr/ports/math/dakota/work/.build/packages/external/CONMIN/src:/usr/ports/math/dakota/work/.build/packages/pecos/src:/usr/ports/math/dakota/work/.build/packages/external/LHS/lib:/usr/ports/math/dakota/work/.build/packages/external/dfftpack:/usr/ports/math/dakota/work/.build/packages/external/VPISparseGrid/src:/usr/local/lib:/usr/ports/math/dakota/work/.build/packages/pecos/util/src: ../src/interpreter/libsurfpack_interpreter.so ../src/libsurfpack.so ../src/libsurfpack_fortran.so ../../external/NCSUOpt/libncsuopt.so ../../external/CONMIN/src/libconmin.so ../../pecos/src/libpecos_src.so ../../external/LHS/lib/liblhs.so ../../external/LHS/lib/liblhs_mods.so ../../external/LHS/lib/liblhs_mod.so ../../external/dfftpack/libdfftpack.so ../../external/VPISparseGrid/src/libsparsegrid.so /usr/local/lib/libboost_serialization.so /usr/local/lib/liblapack.so /usr/local/lib/libblas.so ../../pecos/util/src/libpecos_util.so ../../external/trilinos/packages/teuchos/remainder/src/libteuchosremainder.so.12.13 ../../external/trilinos/packages/teuchos/numerics/src/libteuchosnumerics.so.12.13 ../../external/trilinos/packages/teuchos/comm/src/libteuchoscomm.so.12.13 ../../external/trilinos/packages/teuchos/parameterlist/src/libteuchosparameterlist.so.12.13 ../../external/trilinos/packages/teuchos/parser/src/libteuchosparser.so.12.13 ../../external/trilinos/packages/teuchos/core/src/libteuchoscore.so.12.13 /usr/local/lib/liblapack.so /usr/local/lib/libblas.so   
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_iprimitive<boost::archive::binary_iarchive, char, std::char_traits<char> >::load(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_oprimitive<boost::archive::binary_oarchive, char, std::char_traits<char> >::~basic_binary_oprimitive()'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::text_oarchive_impl<boost::archive::text_oarchive>::text_oarchive_impl(std::ostream&, unsigned int)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_text_iprimitive<std::istream>::~basic_text_iprimitive()'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_oprimitive<boost::archive::binary_oarchive, char, std::char_traits<char> >::basic_binary_oprimitive(std::basic_streambuf<char, std::char_traits<char> >&, bool)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_iprimitive<boost::archive::binary_iarchive, char, std::char_traits<char> >::basic_binary_iprimitive(std::basic_streambuf<char, std::char_traits<char> >&, bool)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::text_iarchive_impl<boost::archive::text_iarchive>::text_iarchive_impl(std::istream&, unsigned int)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_iprimitive<boost::archive::binary_iarchive, char, std::char_traits<char> >::init()'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::text_oarchive_impl<boost::archive::text_oarchive>::save(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_oprimitive<boost::archive::binary_oarchive, char, std::char_traits<char> >::save(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::text_iarchive_impl<boost::archive::text_iarchive>::load(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&)'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_iprimitive<boost::archive::binary_iarchive, char, std::char_traits<char> >::~basic_binary_iprimitive()'  
/usr/local/bin/ld: ../src/libsurfpack.so: undefined reference to `boost::archive::basic_binary_oprimitive<boost::archive::binary_oarchive, char, std::char_traits<char> >::init()'  
```  
  
The RDKit project has the open issue with closely related symptoms: https://github.com/rdkit/rdkit/issues/1182  
  
Why are these symbols missing in libboost_serialization.so?

---

## Comment 1

> Username: robertramey  
> Created at: 2019-10-19 16:32:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-544169090  

This is almost surely an issue related to symbol visibility.  Recently, support of symbol visibility was implemented for gcc and clang compilers.  This makes static libraries link much faster and makes shared libraries load much faster at runtime.  Unfortunately the rules for setting the visibility attributes for msvc, clang, and gcc vary in a way I've never been able to really understand so I had to do a lot of trial and error.  I don't even know if the rules vary depending on the OS.  Since I don't have FreeBSD I haven't been able to experiment this might be wrong in this environment.  
  
Boost does test on gcc8 on linux so I would think that this would work on BSD as well.  Since it doesn't seem to, someone will have to spend some time experimenting.  If you do that and come up with a fix, I'll take a look at it.  
  
Another fix for your case would be to disable visibility entirely.  This is by altering the command line switches used in building the library.  If you do this with bjam it's pretty simple - though I forget how to do it.    
  
I don't have much more to suggest at this point, and don't see anything I in particular can do.  So I'm going to close this issue.  Feel free to re-open it if you get more information.

---

## Comment 2

> Username: ptosco  
> Created at: 2019-11-13 19:33:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-553563150  

@yurivict @robertramey Sorry to revive an old thread, but I have just run into the same issue and I found the reason. I was using `g++` 8 (CentOS 8) for the first time to build RDKit, and I was attempting to link against Boost 1.66 build on GCC 5.3.0 (CentOS 8 devtoolset-4). Apparently GCC 5.3.0 in devtoolset-4 defaulted to `_GLIBCXX_USE_CXX11_ABI=0`, whereas GCC 8 defaults to `_GLIBCXX_USE_CXX11_ABI=1`. As name mangling for `boost::archive::text_oarchive_impl` symbols is quite different in the two ABIs, this is why linking fails.  
Adding a  
```  
add_definitions("-D_GLIBCXX_USE_CXX11_ABI=0")  
```  
in the root `CMakeLists.txt` file of the project allows building and linking `g++-8` compiled objects against `g++-5` Boost without problems.

---

## Comment 3

> Username: robertramey  
> Created at: 2019-12-18 03:48:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-566855110  

Hmmm - then shouldn't we maintain compatibility with the latest versions of gcc in preference to older ones?  That is, shouldn't we use `add_definitions("-D_GLIBCXX_USE_CXX11_ABI=1")`

---

## Comment 4

> Username: ptosco  
> Created at: 2019-12-18 09:23:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-566948280  

I wouldn't take any action. The problem only arises when you attempt to link Boost libraries to object files compiled with different versions of the compiler. This is not recommended in general. My comment was intended to help people landing on this page with a similar problem, rather than to suggest any action at the Boost level.

---

## Comment 5

> Username: calvincramer  
> Created at: 2021-07-16 19:39:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-881675217  

I had this issue compiling for VxWorks, with linking errors like:  
```  
../bin.v2/libs/serialization/test/test_array_text_archive.test/clang-vxworks-11.1.0.1/debug/target-os-vxworks/test_array.o: in function `~text_oarchive_impl':  
.../boost/archive/text_oarchive.hpp:89: undefined reference to `boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()'  
```  
These linking errors occurred when the serialization test executables link against libboost_serialization.so (curiously building statically did not have this problem).  
Disabling / removing the lines that provide `-fvisibility=hidden` and `-fvisibility-inlines-hidden` in **libs/serialization/build/Jamfile.v2** solved these linking errors for me.  
  
I noticed that the "good" libboost_serialization.so" build targeting linux has the `boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive` symbol as a _weak_ (W) symbol (seen from `nm`), but in VxWorks it is a _text_ (t) symbol. I'm not entirely sure what insight this might have. After removing the `-fvisibility` option, the symbol is weak like when build for linux.  
  
Note, compiling on linux using gcc 7.5.0, for VxWorks using llvm clang 11.1.0.1 (plus our own linker scripts and other things that might have caused this issue for me)  
  
So at least for me, it's a symbol visibility issue, not using different versions of the compiler.

---

## Comment 6

> Username: robertramey  
> Created at: 2021-07-17 16:23:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-881922251  

Ahhhh - infamous visibility issue.   
  
"compiling for VxWorks" - does this mean compiling the app with a pre-made serialization library? If so who builds that?  If it means compiling the library you're probably in better shape.  There are a number of "visibility" macros and switches which control this.  Further they different for windows and unix.  You're using Vx works means that the ones I plugged in there might not be quite right.  Sorry about that.  
  
I can suggest that you look over all the information in the documentation, macros, switches and the source code #if macros and see if you and tweak them to make them perfect.  This would give a perfect product and help me include this information into the code so we have the issue fixed once and for all.  It's a way you can contribute to the library in an important way with relatively little effort.  
  
You might want to try not using limited visibility at all.  
  
Thanks for your efforts on this.  I'm leaving this open for now.  
  
Robert Ramey

---

## Comment 7

> Username: robertramey  
> Created at: 2021-07-17 16:26:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-881922538  

I just re-read the whole thread so now I'm not so confident of my advice.  I would like to get a once and for-all solution.

---

## Comment 8

> Username: calvincramer  
> Created at: 2021-07-20 02:13:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-882998139  

>>> "compiling for VxWorks" - does this mean compiling the app with a pre-made serialization library? If so who builds that?  
  
There are no pre-made boost libraries for VxWorks. When building the VxWorks OS, any boost library that has build component is built from source with a: `b2 install --with-serialization --with-atomic ...`  
  
>>> There are a number of "visibility" macros and switches which control this. Further they different for windows and unix.  
  
We use clang and gcc depending on the target architecture, which is also what I see serialization tested on: https://www.boost.org/development/tests/master/developer/serialization.html  
  
I've been using clang to compile targeting intel architecture. I see the same error for arm (also using clang). Unfortunately I can't compare with gcc currently (I am working to get shared library support for PowerPC, which we do use gcc for).  
  
  
I think the only macro that matters for this is `BOOST_ARCHIVE_OR_WARCHIVE_DECL => BOOST_SYMBOL_EXPORT => __attribute__((__visibility__("default")))`  
from the destructor here: https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_text_oprimitive.hpp#L185  
Both Linux and VxWorks resolve these symbols to "__attribute__((__visibility__("default")))". I tested this by printing out the value of the macro with a pragma warning right above the destructor.  
  
#### Other tests  
  
With `-fvisibility=hidden`, I noticed that the individual object files comprising libboost_serialization.so with the destructor symbol that is 'missing' are the same symbol types for Linux and VxWorks:  
  
    * basic_text_oprimitive.o  
    0000000000000000 W boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
    0000000000000000 W boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
    0000000000000000 n boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
    * text_oarchive.o  
                     U boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
    * xml_oarchive.o  
                     U boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
  
But, after linking `libboost_serialization.so`, the destructor becomes **'t'** in VxWorks, and remains **'W'** in Linux:  
  
    nm -C libboost_serialization.so.1.75.0 | grep "boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive\(\)"  
  
    # VxWorks:  
    000000000003b800 t boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
    000000000003b800 t boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
  
    # Linux:  
    000000000002e530 W boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
    000000000002e530 W boost::archive::basic_text_oprimitive<std::ostream>::~basic_text_oprimitive()  
  
So it seems like basic_text_oprimitive.o from Linux and VxWorks match, but there is a change after linking. **Is it the case that if basic_text_oprimitive.o shows this destructor symbol as Weak in both object files, they have the same visibility? In other words, does weak / text imply visibility?**  
  
I compared the commands to link libboost_serialization.so, and tried to have VxWorks match linux as close as possible, but was unable to make the symbol remain Weak.  
  
I have the feeling that nothing in serialization is wrong, and it is our tooling. (I ran into a nasty error from our tools with comdat sections being deleted before!)  
  
Other than this analysis, I'm going to ask for some help from someone who knows compilers better. Also I'm going to try make a simple test case that shows this error.

---

## Comment 9

> Username: robertramey  
> Created at: 2021-07-20 17:13:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-883557467  

Thanks for your efforts in this regard.   This is tremendously important work and it's work like this which continues to make the boost serialization library the goto solution for C++ serialization after 17 years!!!   I've worked hard to incorporate all the workarounds and quirkarounds discovered by users.  So, assuming you are able to narrow this down to something we can address, know that you efforts will not be wasted and future Vx users will benefit.  
  
Thanks for participating in Boost Serialization maintenance.  
  
FYI - I had be considering phasing out support for the library given a number reasons: not header only, not "modern" C++, fixing a couple of design mistakes, etc.  But looking out upon the landscape, I don't see any real replacement.  In fact, I don't even see a standards proposal - not a single one as of 2021.  (but of course we can't live without three-way compare!).   So I've decided to spend time in the future upgrading the documentation so it looks more like that of safe numerics:  Better usage of XML/Boost Book, More formally defined, More examples (composition with other libraries for compression, encryption, etc., better example/tutorial on creating one's own archive class, interesting corner cases like using BS to stream data back/forth between other threads/async tasks, etc.  You work encourages me to more seriously consider undertaking that pretty large task.

---

## Comment 10

> Username: calvincramer  
> Created at: 2021-07-21 19:03:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-884423558  

Well, at least I narrowed it down to "turn visibility off makes it work" and "turn visibility on makes it fail". I still haven't figured out the **real** reason why. In the mean time, I am jumping to a new project, so I won't be able to spend much (if any) time on it :'(.   
  
I'm glad you're considering those improvements. I'm a huge fan of refactoring and breaking backwards-compatibility (for the sake of a better design).

---

## Comment 11

> Username: robertramey  
> Created at: 2021-07-22 01:22:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-884598695  

that's actually good progress.  Now the precedure would be to turn visibility on half headers at a time, then half of half etc.  This might narrow down to the offender.  
  
I don't consider this breaking backward compatibility but rather bug fixing.  (of course bug fixing DOES break backward compatibility.)  I hope you can find more time to invest in this in the future.

---

## Comment 12

> Username: calvincramer  
> Created at: 2021-07-22 16:56:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/164#issuecomment-885065751  

After trying various combinations of functions, templated classes, specialization types for these classes, and including `__attribute__((__visibility__("default")))` or not, the issue is probably coming from the visibility of our std::ostream. Our standard library is based on Dinkumware.  
   
I'm confident boost serialization has no fault here, so we can close the issue. For future explorers with a not-widely-used-or-tested standard library, check if your standard library is exporting it's symbols correctly. To check if your standard library is compliant, use this test:  
```c++  
#include <iostream>  
  
#define VISIBLE __attribute__((visibility("default")))  
  
class TestClassHidden {};  
class VISIBLE TestClassVisible {};  
  
template <typename T> class TestTemplate3Hidden {  
 protected:  
            void func_hidden()     {}  
    VISIBLE void func_visible()    {}  
    VISIBLE TestTemplate3Hidden()  {}  
    VISIBLE ~TestTemplate3Hidden() {}  
};  
template <typename T> class VISIBLE TestTemplate4Visible {  
 protected:  
            void func_hidden()      {}  
    VISIBLE void func_visible()     {}  
    VISIBLE TestTemplate4Visible()  {}  
    VISIBLE ~TestTemplate4Visible() {}  
};  
  
template class TestTemplate3Hidden<int>;  
template class TestTemplate3Hidden<TestClassHidden>;  
template class TestTemplate3Hidden<TestClassVisible>;  
template class TestTemplate3Hidden<std::ostream>;  
  
template class TestTemplate4Visible<int>;  
template class TestTemplate4Visible<TestClassHidden>;  
template class TestTemplate4Visible<TestClassVisible>;  
template class TestTemplate4Visible<std::ostream>;  
```  
Compile this with `-fvisibility=hidden -fvisibility-inlines-hidden -ftemplate-depth-255 -fno-inline -fPIC -O0` into an object file, and link into a shared library. Do this for a ground-truth C++ stdlib (I used gcc on Ubuntu) and for your failing platform (clang for VxWorks for me). Compare the symbol types with `nm` between the shared libraries:  
```sh  
nm -C libtest1.so | cut -c 18- | grep Test | grep -v "^n"  
nm -C libtest2.so | cut -c 18- | grep Test | grep -v "^n"  
```  
Symbols that are lowercase are local (hidden from further linking), and symbols that are uppercase are visible to further linking. For me, there were mismatches in the `TestExportedTemplateClass<std::ostream>::TestExportedTemplateClass()` constructor symbol, and the destructor as well.
