# #297 - Clang-CUDA is not being distinguished from NVCC [Closed]

> Username: psalz  
> Created at: 2019-09-11 13:16:12 UTC  
> Updated at: 2021-09-27 13:24:44 UTC  
> Closed at: 2021-09-26 18:48:07 UTC  
> Url: https://github.com/boostorg/config/issues/297  

I've recently (as of Boost 1.71) run into two problems related to [compilation of CUDA code using Clang](http://llvm.org/docs/CompileCudaWithLLVM.html):  
  
1. Boost.Optional is broken since 1.69. For example, compilation of the following simple program will fail with `error: use of overloaded operator '=' is ambiguous (with operand types 'boost::optional<int>' and 'int')`:  
   ```cpp  
       #include <boost/optional.hpp>  
  
       int main() {  
           boost::optional<int> maybe_int;  
           maybe_int = 42;   
           return 0;  
       }  
   ```  
   I believe the change that introduced this regression is the fix for #237. What happens is that Boost assumes NVCC is being used to compile CUDA, and tries to [determine its version](https://github.com/boostorg/config/blob/cda275e884ddde3d621421ca08718a165955d550/include/boost/config/compiler/nvcc.hpp#L14). Since that is not set however, it defaults to an older version that does not support variadic templates (`BOOST_NO_CXX11_VARIADIC_TEMPLATES`). Additionally the compiler is also detected to be `__clang__`, which ultimately causes Boost.Optional to define two versions of an assignment operator, causing this ambiguity.  
  
2. Boost.PP's variadic macros are also broken since 1.69, due to a separate change but conceptually in the same vein. I've opened a [dedicated issue](https://github.com/boostorg/preprocessor/issues/24) there.  
  
To remedy this, I suggest to detect NVCC based on `__NVCC__` instead of `__CUDA__` or `__CUDACC__`. See [here](http://llvm.org/docs/CompileCudaWithLLVM.html#detecting-clang-vs-nvcc-from-code) for more information on how to distinguish Clang from NVCC when compiling CUDA. Care has to be taken however to ensure that the correct branches are being taken when Clang is being used as NVCC's host compiler.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-10-28 18:27:52 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-547083921  

I think we do need to include nvcc.hpp to set up the device/host macros, but disable the nvcc specific macros when clang is being used.  There are actually 3 use cases:  
  
1) Clang is doing everything.  
2) NVCC is calling clang as the host compiler and/or preprocessor.  
3) It really is NVCC.  
  
I assume that (2) still need the workarounds (in which case Boost.Optional may still be broken if it's behaving differently for clang), but I don't have a CUDO setup here to test on any more.  
  
If we encase the block:  
  
```  
// A bug in version 7.0 of CUDA prevents use of variadic templates in some occasions  
// https://svn.boost.org/trac/boost/ticket/11897  
// This is fixed in 7.5. As the following version macro was introduced in 7.5 an existance  
// check is enough to detect versions < 7.5  
#if BOOST_CUDA_VERSION < 7050000  
#   define BOOST_NO_CXX11_VARIADIC_TEMPLATES  
#endif  
// The same bug is back again in 8.0:  
#if (BOOST_CUDA_VERSION > 8000000) && (BOOST_CUDA_VERSION < 8010000)  
#   define BOOST_NO_CXX11_VARIADIC_TEMPLATES  
#endif  
// CUDA (8.0) has no constexpr support in msvc mode:  
#if defined(_MSC_VER) && (BOOST_CUDA_VERSION < 9000000)  
#  define BOOST_NO_CXX11_CONSTEXPR  
#endif  
```  
In a  
```  
# !defined(__clang__) || defined(__NVCC__)  
```  
block, does that fix things for you?

---

## Comment 2

> Username: psalz  
> Created at: 2019-10-30 14:47:52 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-547942727  

Yes, that would indeed fix it!  
  
(Note: Coming back to this I initially had some trouble to replicate the bug with the snippet I provided above, as variadic templates require compilation with `std=c++11` or newer - which I forgot).

---

## Comment 3

> Username: bernhardmgruber  
> Created at: 2021-09-16 22:05:05 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-921286448  

I just ran into this as well, because `BOOST_METAPARSE_STRING` fails to compile with clang in CUDA mode: https://cuda.godbolt.org/z/nzsGEGTn9  
The reason is again because `BOOST_NO_CXX11_VARIADIC_TEMPLATES` is wrongly defined by Boost.Config.

---

## Comment 4

> Username: bernhardmgruber  
> Created at: 2021-09-17 08:14:56 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-921600902  

@jzmaddock If I apply the above mentioned fix then I get compilation errors: `boost/type_traits/is_base_and_derived.hpp:142:25: error: invalid application of 'sizeof' to an incomplete type 'boost::in_place_factory_base' BOOST_STATIC_ASSERT(sizeof(B) != 0);`. This might be more involved to fix.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-09-17 18:31:58 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-921999407  

What compile line are you using with clang, for some reason MSys-mingw/clang-11 and  
  
```  
 clang -x cuda --cuda-gpu-arch=sm_75 -std=c++14 -I. -nocudalib -nocudainc t.cu  
```  
  
does NOT set `__CUDACC__` which is required to trigger the issue.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-09-17 18:36:18 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-922001731  

Ah, -nocudainc is the issue, but without that I get other issues.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2021-09-26 19:08:26 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-927355001  

OK reproduced on Ubuntu with clang-11 and CUDA 11 SDK.  The above fix addresses the original issue so this one is closing down now.  
  
@bernhardmgruber : your issue indicates that something has forward declared boost::in_place_factory_base and then used it in is_base_of: this is indeed an error, and should be so.  You will need to provide more information in a separate issue though as I'm unable to reproduce with current develop and either clang-10 or 11 and CUDA 11.  You could try including boost/utlity/in_place_factory.hpp at the start of your test file to see if this fixes things?  If so then it's a question of tracking down who the culprit is (ie which library is instantiating is_base_of on an incomplete type).

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-09-27 08:51:03 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-927661943  

@bernhardmgruber this should work for you also: https://cuda.godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:cuda,selection:(endColumn:1,endLineNumber:8,positionColumn:1,positionLineNumber:8,selectionStartColumn:1,selectionStartLineNumber:8,startColumn:1,startLineNumber:8),source:'%23include+%3Cboost/config.hpp%3E%0A%23undef+BOOST_NO_CXX11_VARIADIC_TEMPLATES%0A%23undef+BOOST_NO_VARIADIC_TEMPLATES%0A%0A%0A%23include+%3Cboost/metaparse/string.hpp%3E%0A%0A//+%23if+BOOST_CUDA_VERSION+%3C+7050000%0A//+%23error%0A//+%23endif%0A%0A//+%23if+defined+BOOST_NO_CXX11_VARIADIC_TEMPLATES%0A//+%23error%0A//+%23endif%0A%0A//+static_assert(BOOST_METAPARSE_STD+%3E%3D+2011,+%22%22)%3B%0A%0Ausing+T+%3D+BOOST_METAPARSE_STRING(%22asfd%22)%3B%0A'),l:'5',n:'0',o:'CUDA+C%2B%2B+source+%231',t:'0')),k:40.469973890339425,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:cuclang1100,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:cuda,libs:!(),options:'-std%3Dc%2B%2B14+-O3+-I/opt/compiler-explorer/libs/boost_1_77_0/',selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1,tree:'1'),l:'5',n:'0',o:'clang+11.0.0+sm_75+CUDA-10.2+(CUDA+C%2B%2B,+Editor+%231,+Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compiler:1,editor:1,fontScale:14,fontUsePx:'0',tree:'1',wrap:'1'),l:'5',n:'0',o:'Output+of+clang+11.0.0+sm_75+CUDA-10.2+(Compiler+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),k:59.530026109660575,l:'3',n:'0',o:'',t:'0')),l:'2',n:'0',o:'',t:'0')),version:4

---

## Comment 9

> Username: bernhardmgruber  
> Created at: 2021-09-27 13:24:43 UTC  
> Url: https://github.com/boostorg/config/issues/297#issuecomment-927872834  

@jzmaddock thank you! We have something similar in our code now. Unfortunately, the problem persists and now moved to Boost.Format. See: #406.
