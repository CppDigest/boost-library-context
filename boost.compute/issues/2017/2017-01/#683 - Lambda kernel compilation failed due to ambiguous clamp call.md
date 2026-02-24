# #683 - Lambda kernel compilation failed due to ambiguous clamp call [Closed]

> Username: donniet  
> Created at: 2017-01-13 19:00:09 UTC  
> Updated at: 2017-03-21 03:08:35 UTC  
> Closed at: 2017-03-21 03:08:34 UTC  
> Url: https://github.com/boostorg/compute/issues/683  

The following example compiles but fails at run time with the error below.  
  
```  
#include <boost/compute.hpp>  
#include <iostream>  
  
namespace compute = boost::compute;  
  
int main() {  
  // get default device and setup context  
  compute::device device = compute::system::default_device();  
  compute::context context(device);  
  compute::command_queue queue(context, device);  
  
  // create data array on host  
  unsigned int host_data[] = { 1, 3, 5, 7, 9 };  
  
  // create vector on device  
  compute::vector<unsigned int> device_vector(5, context);  
  
  // copy from host to device  
  compute::copy(  
    host_data, host_data + 5, device_vector.begin(), queue  
  );  
  
  using compute::lambda::clamp;  
  using compute::lambda::_1;  
  compute::uint_ zero = 0, five = 5;  
  
  compute::transform(  
    device_vector.begin(), device_vector.end(), device_vector.begin(), clamp(_1, zero, five), queue  
  );  
  
  // create vector on host  
  std::vector<unsigned int> host_vector(5);  
  
  // copy data back to host  
  compute::copy(  
    device_vector.begin(), device_vector.end(), host_vector.begin(), queue  
  );  
  
  return 0;  
}  
```  
  
I compiled this on a Mac with this command:    
`$ clang++ -framework OpenCL -DBOOST_COMPUTE_DEBUG_KERNEL_COMPILATION main.cpp`  
  
And when running I get the following error:  
```  
$ ./a.out   
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
  
  
__kernel void copy(__global uint* _buf0, const uint count)  
{  
uint index = get_local_id(0) + (512 * get_group_id(0));  
for(uint i = 0; i < 4; i++){  
    if(index < count){  
_buf0[index]=clamp(_buf0[index], 0, 5);  
       index += 128;  
    }  
}  
  
}  
  
--- build log ---  
<program source>:12:14: error: call to 'clamp' is ambiguous  
_buf0[index]=clamp(_buf0[index], 0, 5);  
             ^~~~~  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4867:18: note: candidate function  
int __OVERLOAD__ clamp( int x, int min, int max );  
                 ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4871:19: note: candidate function  
uint __OVERLOAD__ clamp( uint x, uint min, uint max );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4933:19: note: candidate function  
int2 __OVERLOAD__ clamp( int2 x,  int y, int z );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4934:19: note: candidate function  
int3 __OVERLOAD__ clamp( int3 x,  int y, int z );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4935:19: note: candidate function  
int4 __OVERLOAD__ clamp( int4 x,  int y, int z );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4936:19: note: candidate function  
int8 __OVERLOAD__ clamp( int8 x,  int y, int z );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4937:20: note: candidate function  
int16 __OVERLOAD__ clamp( int16 x, int y, int z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4839:20: note: candidate function  
float __OVERLOAD__ clamp(float x, float minval, float maxval);  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4840:21: note: candidate function  
float2 __OVERLOAD__ clamp(float2 x, float2 minval, float2 maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4841:21: note: candidate function  
float3 __OVERLOAD__ clamp(float3 x, float3 minval, float3 maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4842:21: note: candidate function  
float4 __OVERLOAD__ clamp(float4 x, float4 minval, float4 maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4843:21: note: candidate function  
float8 __OVERLOAD__ clamp( float8 x, float8 minval, float8 maxval );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4844:22: note: candidate function  
float16 __OVERLOAD__ clamp( float16 x, float16 minval, float16 maxval );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4865:19: note: candidate function  
char __OVERLOAD__ clamp( char x, char min, char max );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4866:20: note: candidate function  
short __OVERLOAD__ clamp( short x, short min, short max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4868:19: note: candidate function  
long __OVERLOAD__ clamp( long x, long min, long max );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4869:20: note: candidate function  
uchar __OVERLOAD__ clamp( uchar x, uchar min, uchar max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4870:21: note: candidate function  
ushort __OVERLOAD__ clamp( ushort x, ushort min, ushort max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4872:20: note: candidate function  
ulong __OVERLOAD__ clamp( ulong x, ulong min, ulong max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4873:20: note: candidate function  
char2 __OVERLOAD__ clamp( char2 x, char2 min, char2 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4874:21: note: candidate function  
short2 __OVERLOAD__ clamp( short2 x, short2 min, short2 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4875:19: note: candidate function  
int2 __OVERLOAD__ clamp( int2 x, int2 min, int2 max );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4876:20: note: candidate function  
long2 __OVERLOAD__ clamp( long2 x, long2 min, long2 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4877:21: note: candidate function  
uchar2 __OVERLOAD__ clamp( uchar2 x, uchar2 min, uchar2 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4878:22: note: candidate function  
ushort2 __OVERLOAD__ clamp( ushort2 x, ushort2 min, ushort2 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4879:20: note: candidate function  
uint2 __OVERLOAD__ clamp( uint2 x, uint2 min, uint2 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4880:21: note: candidate function  
ulong2 __OVERLOAD__ clamp( ulong2 x, ulong2 min, ulong2 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4881:20: note: candidate function  
char3 __OVERLOAD__ clamp( char3 x, char3 min, char3 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4882:21: note: candidate function  
short3 __OVERLOAD__ clamp( short3 x, short3 min, short3 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4883:19: note: candidate function  
int3 __OVERLOAD__ clamp( int3 x3, int3 min3, int3 max3 );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4884:20: note: candidate function  
long3 __OVERLOAD__ clamp( long3 x3, long3 min3, long3 max3 );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4885:21: note: candidate function  
uchar3 __OVERLOAD__ clamp( uchar3 x, uchar3 min, uchar3 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4886:22: note: candidate function  
ushort3 __OVERLOAD__ clamp( ushort3 x, ushort3 min, ushort3 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4887:20: note: candidate function  
uint3 __OVERLOAD__ clamp( uint3 x3, uint3 min3, uint3 max3 );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4888:21: note: candidate function  
ulong3 __OVERLOAD__ clamp( ulong3 x3, ulong3 min3, ulong3 max3 );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4889:20: note: candidate function  
char4 __OVERLOAD__ clamp( char4 x, char4 min, char4 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4890:21: note: candidate function  
short4 __OVERLOAD__ clamp( short4 x, short4 min, short4 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4891:19: note: candidate function  
int4 __OVERLOAD__ clamp( int4 x, int4 min, int4 max );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4892:20: note: candidate function  
long4 __OVERLOAD__ clamp( long4 x, long4 min, long4 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4893:21: note: candidate function  
uchar4 __OVERLOAD__ clamp( uchar4 x, uchar4 min, uchar4 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4894:22: note: candidate function  
ushort4 __OVERLOAD__ clamp( ushort4 x, ushort4 min, ushort4 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4895:20: note: candidate function  
uint4 __OVERLOAD__ clamp( uint4 x, uint4 min, uint4 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4896:21: note: candidate function  
ulong4 __OVERLOAD__ clamp( ulong4 x, ulong4 min, ulong4 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4897:20: note: candidate function  
char8 __OVERLOAD__ clamp( char8 x, char8 min, char8 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4898:21: note: candidate function  
short8 __OVERLOAD__ clamp( short8 x, short8 min, short8 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4899:19: note: candidate function  
int8 __OVERLOAD__ clamp( int8 x, int8 min, int8 max );  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4900:20: note: candidate function  
long8 __OVERLOAD__ clamp( long8 x, long8 min, long8 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4901:21: note: candidate function  
uchar8 __OVERLOAD__ clamp( uchar8 x, uchar8 min, uchar8 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4902:22: note: candidate function  
ushort8 __OVERLOAD__ clamp( ushort8 x, ushort8 min, ushort8 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4903:20: note: candidate function  
uint8 __OVERLOAD__ clamp( uint8 x, uint8 min, uint8 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4904:21: note: candidate function  
ulong8 __OVERLOAD__ clamp( ulong8 x, ulong8 min, ulong8 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4905:21: note: candidate function  
char16 __OVERLOAD__ clamp( char16 x, char16 min, char16 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4906:22: note: candidate function  
short16 __OVERLOAD__ clamp( short16 x, short16 min, short16 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4907:20: note: candidate function  
int16 __OVERLOAD__ clamp( int16 x, int16 min, int16 max );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4908:21: note: candidate function  
long16 __OVERLOAD__ clamp( long16 x, long16 min, long16 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4909:23: note: candidate function  
ushort16 __OVERLOAD__ clamp( ushort16 x, ushort16 min, ushort16 max );  
                      ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4910:22: note: candidate function  
uchar16 __OVERLOAD__ clamp( uchar16 x, uchar16 min, uchar16 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4911:21: note: candidate function  
uint16 __OVERLOAD__ clamp( uint16 x, uint16 min, uint16 max );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4912:22: note: candidate function  
ulong16 __OVERLOAD__ clamp( ulong16 x, ulong16 min, ulong16 max );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4913:20: note: candidate function  
char2 __OVERLOAD__ clamp( char2 x,  char y, char z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4914:20: note: candidate function  
char3 __OVERLOAD__ clamp( char3 x,  char y, char z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4915:20: note: candidate function  
char4 __OVERLOAD__ clamp( char4 x,  char y, char z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4916:20: note: candidate function  
char8 __OVERLOAD__ clamp( char8 x,  char y, char z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4917:21: note: candidate function  
char16 __OVERLOAD__ clamp( char16 x, char y, char z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4918:21: note: candidate function  
uchar2 __OVERLOAD__ clamp( uchar2 x,  uchar y, uchar z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4919:21: note: candidate function  
uchar3 __OVERLOAD__ clamp( uchar3 x,  uchar y, uchar z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4920:21: note: candidate function  
uchar4 __OVERLOAD__ clamp( uchar4 x,  uchar y, uchar z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4921:21: note: candidate function  
uchar8 __OVERLOAD__ clamp( uchar8 x,  uchar y, uchar z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4922:22: note: candidate function  
uchar16 __OVERLOAD__ clamp( uchar16 x, uchar y, uchar z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4923:21: note: candidate function  
short2 __OVERLOAD__ clamp( short2 x,  short y, short z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4924:21: note: candidate function  
short3 __OVERLOAD__ clamp( short3 x,  short y, short z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4925:21: note: candidate function  
short4 __OVERLOAD__ clamp( short4 x,  short y, short z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4926:21: note: candidate function  
short8 __OVERLOAD__ clamp( short8 x,  short y, short z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4927:22: note: candidate function  
short16 __OVERLOAD__ clamp( short16 x, short y, short z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4928:22: note: candidate function  
ushort2 __OVERLOAD__ clamp( ushort2 x,  ushort y, ushort z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4929:22: note: candidate function  
ushort3 __OVERLOAD__ clamp( ushort3 x,  ushort y, ushort z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4930:22: note: candidate function  
ushort4 __OVERLOAD__ clamp( ushort4 x,  ushort y, ushort z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4931:22: note: candidate function  
ushort8 __OVERLOAD__ clamp( ushort8 x,  ushort y, ushort z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4932:23: note: candidate function  
ushort16 __OVERLOAD__ clamp( ushort16 x, ushort y, ushort z );  
                      ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4938:20: note: candidate function  
uint2 __OVERLOAD__ clamp( uint2 x,  uint y, uint z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4939:20: note: candidate function  
uint3 __OVERLOAD__ clamp( uint3 x,  uint y, uint z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4940:20: note: candidate function  
uint4 __OVERLOAD__ clamp( uint4 x,  uint y, uint z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4941:20: note: candidate function  
uint8 __OVERLOAD__ clamp( uint8 x,  uint y, uint z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4942:21: note: candidate function  
uint16 __OVERLOAD__ clamp( uint16 x, uint y, uint z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4943:20: note: candidate function  
long2 __OVERLOAD__ clamp( long2 x,  long y, long z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4944:20: note: candidate function  
long3 __OVERLOAD__ clamp( long3 x,  long y, long z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4945:20: note: candidate function  
long4 __OVERLOAD__ clamp( long4 x,  long y, long z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4946:20: note: candidate function  
long8 __OVERLOAD__ clamp( long8 x,  long y, long z );  
                   ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4947:21: note: candidate function  
long16 __OVERLOAD__ clamp( long16 x, long y, long z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4948:21: note: candidate function  
ulong2 __OVERLOAD__ clamp( ulong2 x,  ulong y, ulong z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4949:21: note: candidate function  
ulong3 __OVERLOAD__ clamp( ulong3 x,  ulong y, ulong z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4950:21: note: candidate function  
ulong4 __OVERLOAD__ clamp( ulong4 x,  ulong y, ulong z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4951:21: note: candidate function  
ulong8 __OVERLOAD__ clamp( ulong8 x,  ulong y, ulong z );  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4952:22: note: candidate function  
ulong16 __OVERLOAD__ clamp( ulong16 x, ulong y, ulong z );  
                     ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4953:21: note: candidate function  
float2 __OVERLOAD__ clamp(float2 x, float minval, float maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4954:21: note: candidate function  
float3 __OVERLOAD__ clamp(float3 x, float minval, float maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4955:21: note: candidate function  
float4 __OVERLOAD__ clamp(float4 x, float minval, float maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4956:21: note: candidate function  
float8 __OVERLOAD__ clamp(float8 x, float minval, float maxval);  
                    ^  
/System/Library/Frameworks/OpenCL.framework/Versions/A/lib/clang/3.2/include/cl_kernel.h:4957:22: note: candidate function  
float16 __OVERLOAD__ clamp(float16 x, float minval, float maxval);  
                     ^  
  
libc++abi.dylib: terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >: Build Program Failure  
Abort trap: 6  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-03-16 23:11:48 UTC  
> Url: https://github.com/boostorg/compute/issues/683#issuecomment-287220288  

I've found a way to resolve that issue. I'll make a fix soon.

---

## Comment 2

> Username: kylelutz  
> Created at: 2017-03-21 03:08:34 UTC  
> Url: https://github.com/boostorg/compute/issues/683#issuecomment-287963902  

Thanks for the report! Should be fixed by #693. Please re-open if the issue persists.
