# #624 - compute::vector<custom_type> and resize [Closed]

> Username: dPavelDev  
> Created at: 2016-07-03 18:50:52 UTC  
> Updated at: 2016-07-25 13:49:09 UTC  
> Closed at: 2016-07-25 13:49:07 UTC  
> Url: https://github.com/boostorg/compute/issues/624  

When I want to create vector with custom type and resize it, I get compilation error:  
  
> C:\boost_gcc\include\boost-1_61/boost/compute/type_traits/type_name.hpp:98:46: error: incomplete type 'boost::compute::detail::type_name_trait<custom_type>' used in nested name specifier  
>      return detail::type_name_trait<T>::value();  
  
Code example:  
  
``` cpp  
#include <boost/compute.hpp>  
  
using namespace std;  
  
struct custom_type {  
    boost::compute::int_ someInt;  
    boost::compute::float2_ somePoint;  
};  
  
int main() {  
    boost::compute::vector<custom_type> v;  
    v.resize(10);  
  
    return 0;  
}  
  
```  
  
Please, fix it.

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-07-03 18:57:28 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230169224  

When you want to use `boost::compute::vector` with your custom type you need to register that type (that struct) using `BOOST_COMPUTE_ADAPT_STRUCT()`. See [boost/compute/types/struct.hpp#L88](https://github.com/boostorg/compute/blob/38ba1c54479869c6ad6d2ea1f828e6d113bc5ab9/include/boost/compute/types/struct.hpp#L88) and examples: [test/test_struct.cpp](https://github.com/boostorg/compute/blob/38ba1c54479869c6ad6d2ea1f828e6d113bc5ab9/test/test_struct.cpp), [test/test_user_defined_types.cpp](https://github.com/boostorg/compute/blob/38ba1c54479869c6ad6d2ea1f828e6d113bc5ab9/test/test_user_defined_types.cpp).

---

## Comment 2

> Username: dPavelDev  
> Created at: 2016-07-03 19:24:31 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230170541  

Good, when I added BOOST_COMPUTE_ADAPT_STRUCT(custom_type, custom_type, (someInt, somePoint)), it works.  
  
But when I change boost::compute::float2_ to cl_float2, it doesn't work because it is union. In this case, I can add BOOST_COMPUTE_ADAPT_STRUCT(cl_float2, float2, (s));  
  
But if my union consists only of anonymouse structures, how to add such unions? And is it right way?

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-07-03 20:15:19 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230172908  

> But when I change boost::compute::float2_ to cl_float2, it doesn't work because it is union. In this case, I can add BOOST_COMPUTE_ADAPT_STRUCT(cl_float2, float2, (s));  
  
The thing is that any type used with Boost.Compute needs to be registered, so we know how to translate them when building (writing) OpenCL kernels.   
  
All `boost::compute::<type>N_` (like `boost::compute::uint_` or `boost::compute::float2_`) are registered and I recommend using them as they are well-defined in terms of their size: `float_` is always 4 bytes long, `uint_` - 4 bytes (32 bits), `ushort_` - 2 bytes etc. The size makes difference in terms of performance.  `uchar_`,`uint_`, `int_` etc. are in fact just a typedefs for `cl_uchar`, `cl_uint`, `cl_int` etc., but  `uchar2_` is not  `cl_uchar2`. IIRC (but I'm not sure now) "normal C/C++ types" like `int`, `float`, `char` are registered only because in most cases they and `cl_` types are the same.   
  
So...Yeah, you need to adapt cl_float2 if you want to use it.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-07-03 20:40:19 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230174127  

Where it comes to unnamed structs/unions, for example:  
  
``` cpp  
struct Foo {  
   struct {  
      int a;  
      int b;  
   } bar;  
};  
```  
  
Looking at the code I think `BOOST_COMPUTE_ADAPT_STRUCT()` does not support those. The only  workaround I see is manually doing what `BOOST_COMPUTE_ADAPT_STRUCT()` does ([boost/compute/types/struct.hpp](https://github.com/boostorg/compute/blob/38ba1c54479869c6ad6d2ea1f828e6d113bc5ab9/include/boost/compute/types/struct.hpp#L132)): registering type name with BOOST_COMPUTE_TYPE_NAME(), adding explicit specializations for `std::string type_definition<type>()`,  `struct inject_type_impl<type>` and `meta_kernel& operator<<(meta_kernel &k, type s)`.

---

## Comment 5

> Username: dPavelDev  
> Created at: 2016-07-03 20:51:27 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230174649  

I'm using cl_float\* unions in my project because custom structures with this types **have same aligment and sizeof()** that in OpenCL kernel (on GNU GCC Compiler). It is important.  
  
But cl_float2 is aligment union and I don't use it in custom structure under this library:  
error: static assertion failed: BOOST_COMPUTE_ADAPT_STRUCT() does not support structs with internal padding  
  
code:  
  
``` cpp  
#include <boost/compute.hpp>  
  
using namespace std;  
  
struct custom_type {  
    boost::compute::int_ someInt;  
    boost::compute::float2_ somePoint;  
};  
  
struct same_custom_type {  
    int someInt;  
    cl_float2 somePoint;  
};  
  
BOOST_COMPUTE_ADAPT_STRUCT(cl_float2, float2, (s));  
BOOST_COMPUTE_ADAPT_STRUCT(same_custom_type, same_custom_type, (someInt, somePoint));  
  
int main() {  
    cout << sizeof(custom_type) << " " << sizeof(same_custom_type) << endl; // ! different sizeof under GCC  
  
    boost::compute::vector<same_custom_type> v;  
    v.resize(10);  
  
    return 0;  
}  
  
```  
  
And I have a question: Should I pay attension to  identical size on the custom structures  on the host and in the kernel using this library ?

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-07-03 21:14:47 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230175783  

They have different sizes? I'm not sure why. Hmm... Do you work on 64-bit system? If you are `int` in `same_custom_type` is 8-bytes long and `boost::compute::int_` is only 4-bytes long. Maybe that's the problem.  
  
And, yes, sizes should be identical since copying to/from host is just copying bytes (if types host and device vectors match).

---

## Comment 7

> Username: dPavelDev  
> Created at: 2016-07-03 21:31:50 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230176635  

I tested it in Code Blocks 16.01 under Windows 10 x64 on GCC Compiler  
  
> -------------- Build: Debug in test (compiler: GNU GCC Compiler)---------------  
>   
> mingw32-g++.exe -Wall -std=c++11 -fexceptions -g  -c D:\cpp_projects\test\main.cpp -o obj\Debug\main.o  
> mingw32-g++.exe  -o bin\Debug\test.exe obj\Debug\main.o   -lOpenCL  
> Output file is bin\Debug\test.exe with size 1.09 MB  
> Process terminated with status 0 (0 minute(s), 4 second(s))  
> 0 error(s), 0 warning(s) (0 minute(s), 4 second(s))  
  
``` cpp  
#include <boost/compute.hpp>  
  
using namespace std;  
  
struct custom_type {  
    boost::compute::int_ someInt;  
    boost::compute::float2_ somePoint;  
};  
  
struct same_custom_type2 {  
    int someInt;  
    cl_float2 somePoint;  
};  
  
struct same_custom_type3 {  
    boost::compute::int_ someInt;  
    cl_float2 somePoint;  
};  
  
struct same_custom_type4 {  
    int someInt;  
    boost::compute::float2_ somePoint;  
};  
  
int main() {  
    cout << sizeof(custom_type) <<  endl; // result: 12  
    cout << sizeof(same_custom_type2) <<  endl; // result: 16  
    cout << sizeof(same_custom_type3) <<  endl; // result: 16  
    cout << sizeof(same_custom_type4) <<  endl; // result: 12  
  
    cout << sizeof(int) << endl; // result: 4  
    cout << sizeof(cl_float2) << endl; // result: 8  
  
    return 0;  
}  
```

---

## Comment 8

> Username: jszuppe  
> Created at: 2016-07-03 22:15:05 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230178675  

Ahhhh, ok. `cl_float2` is aligned to 8 bytes and that makes the difference. You're right. The question is what is the size of the struct in OpenCL. I get it now. Unfortunately, I don't have time now. I'll test it tomorrow.  
  
Q: Does   
  
``` cpp  
struct custom_type {  
    boost::compute::int_ someInt;  
    boost::compute::float2_ somePoint;  
};  
BOOST_COMPUTE_ADAPT_STRUCT(custom_type, custom_type, (someInt, somePoint))  
```  
  
work correctly? Does it work if you copy `std::vector<custom_type>` to the `boost::compute::vector<custom_type>` and back to the `std::vector<custom_type>`? Are the values correct?

---

## Comment 9

> Username: dPavelDev  
> Created at: 2016-07-04 14:42:54 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230305720  

Yes, it works correctly.  
  
``` cpp  
#include <boost/compute/system.hpp>  
#include <boost/compute/algorithm.hpp>  
  
using namespace boost;  
  
struct custom_type {  
    compute::int_ someInt;  
    compute::float2_ somePoint;  
  
    custom_type(int someInt = 0, const std::pair<float, float> &somePoint = std::make_pair(0, 0)) : someInt(someInt), somePoint({somePoint.first, somePoint.second}) {}  
};  
  
BOOST_COMPUTE_ADAPT_STRUCT(custom_type, custom_type, (someInt, somePoint))  
  
BOOST_COMPUTE_FUNCTION(int, getSizeOfCustomType, (custom_type x),  
   {  
       return sizeof(custom_type);  
   }  
);  
  
int main() {  
    compute::device device = compute::system::default_device();  
    std::cout << device.name() << std::endl; // my default device is Intel(R) HD Graphics 530  
    std::cout << device.driver_version() << std::endl;  
  
    //! create context and cmd queue  
    compute::context context = compute::context(device);  
    compute::command_queue commandQueue = compute::command_queue(context, device);  
  
    //! create vector with custom type  
    compute::vector<custom_type> vectorWithCustomType(context);  
    vectorWithCustomType.push_back(custom_type(1, std::make_pair(2, 3)), commandQueue);  
  
    custom_type retValue;  
    compute::copy_n(vectorWithCustomType.begin(), 1, &retValue, commandQueue);  
    //! show result (copy are correct with boost::compute types)  
    std::cout << retValue.someInt << " " << retValue.somePoint << std::endl;  
  
    //! check size of this structure in kernel  
    compute::vector<int> retSizeofType(1, context);  
    compute::transform(vectorWithCustomType.begin(), vectorWithCustomType.end(), retSizeofType.begin(), getSizeOfCustomType, commandQueue);  
  
    int sz;  
    compute::copy_n(retSizeofType.begin(), 1, &sz, commandQueue);  
    std::cout << sz << std::endl; // result: 12  
    cl_float2;  
  
    return 0;  
}  
```  
  
But with OpenCL types (cl_*n) it doesn't work (I can't compile this). Also, BOOST_COMPUTE_ADAPT_STRUCT doesn't support classes with private/protected variables and getter/setter members, for example, QVector4D from Qt.  
  
I can use boost::compute types in my project in this form but then I must rewrite some kernel code (set alignment to 1 in all structures but it reduce performance) and host code (write wrappers because boost::compute::float2_, for example, doesn't have x, y members).  
  
Also I don't understand, why copy data beatween two device buffers need kernel program if exist clEnqueueCopyBuffer function?

---

## Comment 10

> Username: jszuppe  
> Created at: 2016-07-04 19:50:28 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230347192  

> Yes, it works correctly.  
  
OK. I tested it myself and now I know why it works. It works because all structs passed to OpenCL kernels that are built in Boost.Compute are packed. That's why when `custom_type` is registered everything works (props for @kylelutz). `float2` in custom_type loses its 8-byte alignment. Without `__attribute__((packed))` size of `custom_type` on OpenCL would be 16.   
  
I believe it was done this way because alignement/padding on host may vary depending on the compiler.  
  
``` cpp  
// custom_type in C++  
struct custom_type {  
    compute::int_ someInt;  
    compute::float2_ somePoint;  
};  
// custom_type in OpenCL  
struct custom_type __attribute__((packed)) {  
    int someInt;  
    float2 somePoint;  
};  
```  
  
> But with OpenCL types (cl_*n) it doesn't work (I can't compile this). Also, BOOST_COMPUTE_ADAPT_STRUCT doesn't support classes with private/protected variables and getter/setter members, for example, QVector4D from Qt.  
  
Yes, it does not support classes. It tries to translate struct to OpenCL and in OpenCL there are no classes, no methods. It's just a basic support for structs and by basic I mean most things work ok, you just don't have unnamed structs and your struct must be packed. However, you're welcome to work on more advanced support for structs.  
  
> I can use boost::compute types in my project in this form but then I must rewrite some kernel code (set alignment to 1 in all structures but it reduce performance) and host code (write wrappers because boost::compute::float2_, for example, doesn't have x, y members).  
  
OK, so you want structs without `__attribute__((packed))`. I modified adapt struct macro so it does not force struct to be packed: [`BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED()`](https://gist.github.com/haahh/a199697c53865bc6574eb547b73ede8a). With this you can use `cl_float2`. However, I don't know if it will be added to Boost.Compute. @kylelutz, what do you think?  
  
``` cpp  
struct custom_type {  
    compute::int_ someInt;  
    cl_float2 somePoint;  
};  
  
BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED(cl_float2, float2, (s))  
BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED(custom_type, custom_type, (someInt, somePoint))  
```

---

## Comment 11

> Username: jszuppe  
> Created at: 2016-07-04 19:58:17 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230347852  

> Also I don't understand, why copy data beatween two device buffers need kernel program if exist clEnqueueCopyBuffer function?  
  
You're right. Copying between host and device vectors and between two device vectors of the _same_ type does not require kernel, it uses those OpenCL functions for copying, reading, writing buffer. I was just curious if it works when you copy to and from the device, to check if there is any alignment on device side. However, my "test idea" does not work, sorry for that. It should be copying, modifying on device (with transform) and copying back to host to see if there is any alignment. Nevermind this now.

---

## Comment 12

> Username: dPavelDev  
> Created at: 2016-07-05 16:10:46 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230524980  

Ohhh, when I wrote BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED(cl_uchar4, uchar4, (s)) , I got error:  
  
``` c  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
typedef struct {  
    uchar s[4];  
} uchar4;  
  
  
__kernel void copy(uint count, __global uchar4* _buf0, __global uchar4* _buf1)  
{  
uint index = get_local_id(0) + (512 * get_group_id(0));  
for(uint i = 0; i < 4; i++){  
    if(index < count){  
_buf0[index]=_buf1[index];  
        index += 128;  
    }  
}  
  
}  
  
--- build log ---  
<kernel>:7:3: error: typedef redefinition with different types ('struct uchar4' vs 'unsigned char __attribute__((ext_vector_type(4)))')  
} uchar4;  
  ^  
cl_kernel.h:107:1: note: previous definition is here  
__NV_DECLARE_VECT_TYPES(unsigned char,   uchar)  
^  
cl_kernel.h:89:55: note: expanded from macro '__NV_DECLARE_VECT_TYPES'  
typedef __attribute__(( ext_vector_type(4)  ))  CTYPE CLTYPE##4;  \  
                                                      ^  
<scratch space>:14:1: note: expanded from here  
uchar4  
^  
  
```  
  
BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED(cl_uchar4, **cl_uchar4**, (s)) is working. Also it is working with my custom structures. Thanks! My project is working with your library!  
  
Ohhhh, when I rewrote some kernel code to support packed structures in my project, I decrease performace in 3-4 times! (from 40 fps to 12 fps on my machine). When I am using aligment I have better performance (under GCC) but MSVC compiler doesn't support **attribute** ((aligned(some_value))).  
  
 In cl_platform.h from NVIDIA GPU Toolkit:  
  
``` cpp  
/* Define alignment keys */  
#if defined( __GNUC__ )  
    #define CL_ALIGNED(_x)          __attribute__ ((aligned(_x)))  
#elif defined( _WIN32) && (_MSC_VER)  
    /* Alignment keys neutered on windows because MSVC can't swallow function arguments with alignment requirements     */  
    /* http://msdn.microsoft.com/en-us/library/373ak2y1%28VS.71%29.aspx                                                 */  
    /* #include <crtdefs.h>                                                                                             */  
    /* #define CL_ALIGNED(_x)          _CRT_ALIGN(_x)                                                                   */  
    #define CL_ALIGNED(_x)  
#else  
   #warning  Need to implement some method to align data here  
   #define  CL_ALIGNED(_x)  
#endif  
```  
  
CL_ALIGNED() doesn't work in MSVC! How to solve this problem using your library? In ideal case, I shouldn't use cl_*n structures in project, only boost::compute types. Would do you think about aligned types in boost::compute?

---

## Comment 13

> Username: jszuppe  
> Created at: 2016-07-05 18:02:33 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230554873  

> Ohhh, when I wrote BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED(cl_uchar4, uchar4, (s)) , I got error:  
  
Yeah... I didn't predicted that. Well.. [This](https://gist.github.com/haahh/a199697c53865bc6574eb547b73ede8a) should fix the problem.  
  
> BOOST_COMPUTE_ADAPT_STRUCT_NOT_PACKED(cl_uchar4, cl_uchar4, (s)) is working. Also it is working with my custom structures. Thanks! My project is working with your library!  
  
This works because it defines `cl_uchar4` in OpenCL, so you're not using builtin `uchar4`, but struct `cl_uchar4`. [This](https://gist.github.com/haahh/a199697c53865bc6574eb547b73ede8a) fixes that, so `cl_uchar4` will become `uchar4` in OpenCL code.  
  
> Ohhhh, when I rewrote some kernel code to support packed structures in my project, I decrease performace in 3-4 times! (from 40 fps to 12 fps on my machine).   
  
I depends on your code and kernels. Packed structs are smaller and sometimes that's a big factor.  
  
> CL_ALIGNED() doesn't work in MSVC!   
> Indeed, it does not work in MSVC. Maybe you can create your own struct with variable you won't use to force `float2` to be aligned to 8 bytes.  
  
``` cpp  
struct custom_type {  
    compute::int_ someInt;  
    compute::int_ dummy; // dummy 4 bytes  
    compute::float2_ somePoint; // this may be aligned to 8-bytes, alignment/padding is tricky in C++  
};  
```

---

## Comment 14

> Username: jszuppe  
> Created at: 2016-07-05 18:13:35 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230557873  

> Would do you think about aligned types in boost::compute?  
  
IIRC alignment/padding is tricky. I think it's possible with C++11 and [alignas specifier](http://en.cppreference.com/w/cpp/language/alignas).

---

## Comment 15

> Username: dPavelDev  
> Created at: 2016-07-05 20:34:22 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230594586  

In [this](https://gist.github.com/haahh/a199697c53865bc6574eb547b73ede8a) gist at 46 and 56 I see return. Is it correct?  
  
> Packed structs are smaller and sometimes that's a big factor.  
  
Memory bandwidth is bottleneck of performance in my project. However, I didn't expect that so much.

---

## Comment 16

> Username: jszuppe  
> Created at: 2016-07-05 20:40:10 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-230596154  

It's fixed now. However, I haven't tested `inline meta_kernel& operator<<(meta_kernel &k, type x)` operator as I couldn't remember any place it's used, but it looks ok.

---

## Comment 17

> Username: jszuppe  
> Created at: 2016-07-10 11:53:11 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-231585118  

@kylelutz Do you think we should adapt `cl_typeN` structs into Boost.Compute, so user can use them out-of-the-box?

---

## Comment 18

> Username: jszuppe  
> Created at: 2016-07-10 11:54:58 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-231585197  

@dPavelDev Have you tried using alignas specifier introduced in C++11? MSVC should support it.

---

## Comment 19

> Username: kylelutz  
> Created at: 2016-07-12 05:17:10 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-231939612  

@haahh Yeah, I think that's a good idea. We should make it easy for users to use the `cl_` wrapper types with Boost.Compute.

---

## Comment 20

> Username: dPavelDev  
> Created at: 2016-07-16 17:56:40 UTC  
> Updated at: 2016-07-16 18:01:03 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-233142792  

> Have you tried using alignas specifier introduced in C++11? MSVC should support it.  
  
Yes, it works good for float4 and lower types, but MSVC doesn't support 64 byte and higher aligment. double4 doesn't work using this compiler. I don't use this type in my project now, but in the future may be.  
  
@kylelutz , would you like to add to boost::compute::type\* such fields as x, y, z, s0, s1, s[dim] like in cl_type*? And, if it is possible, such operators as + - \* / and dot and cross product and others. Is it a good idea?

---

## Comment 21

> Username: jszuppe  
> Created at: 2016-07-17 10:01:11 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-233174366  

> Yes, it works good for float4 and lower types, but MSVC doesn't support 64 byte and higher aligment. double4 doesn't work using this compiler. I don't use this type in my project now, but in the future may be.  
  
Hmm. Yeah, it's said that `alignas` supports up to 16-bytes alignment. Check [__declspec(align(#)) ](https://msdn.microsoft.com/en-us/library/83ythb65%28v=vs.140%29.aspx). It's only for MSVC but should support up to 8192-bytes alignment.

---

## Comment 22

> Username: dPavelDev  
> Created at: 2016-07-25 13:49:07 UTC  
> Url: https://github.com/boostorg/compute/issues/624#issuecomment-234958060  

Thank you! This directive works.
