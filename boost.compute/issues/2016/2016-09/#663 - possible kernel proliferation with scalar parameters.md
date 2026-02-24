# #663 - possible kernel proliferation with scalar parameters [Open]

> Username: Ulfgard  
> Created at: 2016-09-27 12:57:35 UTC  
> Updated at: 2019-07-11 14:03:25 UTC  
> Url: https://github.com/boostorg/compute/issues/663  

Consider the following function  
  
```  
namespace bc=boost::compute;  
void add_scalar(bc::vector<float>& v, float scalar){  
    auto unary = bc::bind(bc::plus<float>(),bc::placeholders::_1, scalar);//_1+scalar  
    bc::transform(v().begin(),v().end(), v().begin(), unary);  
}  
...  
bc::vector<float> v(10000,1.0);  
for(int i = 0; i != 1000; ++i)  
    add_scalar(v,0.5*i);  
```  
  
This will in every iteration generate a kernel and compile it on the device, leading to way more kernel compilations than required(essentially making kernel caching useless). The issue, as far as i understood the meta_kernel code is, that a function can only register buffers, but not constants. Thus the only way to generate the kernel is to stringify the scalar value. A solution would be to add the ability to register constants as additional kernel arguments the same way as it is done with buffers.  
  
An alternative solution that does not require deep changes to the internals would be to use an bc::array<float,1> for everything that is not a constant. But this is very tedious and error prone as the user must be very careful with scalar arguments, especially as for example bc::accumulate returns a float and not an array<float,1> (while the internal implementation actually uses an array<float,1>...).  
  
A way to make the latter solution more viable would be to implement a small wrapper class scalar<T> which is a bit of syntactic sugar around bc::array<float,1>   
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-09-28 14:50:07 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-250189575  

That's an interesting issue. I understand that that example is just to show where is the problem ;) I hope nobody would write something like this (the loop part).  
  
I think that:  
- The easiest and the fastest way to do this (except creating class that is only a syntactic sugar around `bc::array`) is modifying `constant_iterator<T>` class. Now the value is simply evaluated to the `detail::meta_kernel_literal<T>` and later to the string. After the change it would have to add an argument to the kernel, set the value of that argument, and place the name of that argument in the correct place.  
- Other way would be to reconstruct `detail::scalar<T>`, so it evaluates to the kernel argument (etc.) when used with `meta_kernel`, and promote it to the main namespace.   
- Making a class that is only a syntactic sugar around `bc::array` (or any other container, or directly around OpenCL buffer) introduces an overhead of creating a buffer and writing the value to it before the execution. Kernel argument should work better, however, it may be harder to implement.

---

## Comment 2

> Username: Ulfgard  
> Created at: 2016-11-09 09:25:44 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-259368782  

Hi,  
  
Is there any news on this?  
  
this issue becomes more severe for me as some unit tests currently generate > 10000 kernels instead of maybe 10 or so. I would vote for anything that can add a scalar argument to the kernel :). I prefer the scalar argument solution. The problem with scalar arguments however is, that we have to be more careful regarding the uniqueness of the argument, because if everytime we do  
  
k<<*(my_iter)  
  
my_iter adds a scalar argument, we might end up with many copies of the same argument. So my_iter would have to tore the index of the argument somehow(in the same way that buffer objects are used as unique identifiers, scalar arguments also need this unique identifier...). Also, we have to ensure that the value is correctly set after the kernel is built. currently adding a kernel arg will not set its value as is done with buffers.

---

## Comment 3

> Username: Ulfgard  
> Created at: 2016-11-09 10:11:36 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-259378314  

Oh i forgot my idea on how to solve the unique name problem. The best way to make the argument names unique is to tell the kernel which variables belong together. so we can tell the kernel "now we are going to use variables used by the group 1" and this gives the kernel the ability to tell whether a variable has been already registered or not (the first time we enter a group every request of a kernel variable needs to create it, in subsequent calls we know they are there).  
  
For this we might need to add the following kernel functions  
  
```  
//tell the kernel that we need to add a variable group  
std::size_t group_id = k.create_var_group();  
//tell the kernel that we are entering the group group_id  
k.enter_var_group(group_id);  
//create unique identifiers group<group_id>_var<id>,   
//where id is icremented with every call by one. A call to  
//enter_var_group sets id=0  
std::string const& unique_identifier = k.add_or_get_arg<T>(value);  
k<<unique_identifier1;//using it  
k.leave_var_group(group_id);//we are done in this group  
```  
  
With this, we could easily create a registered_iterator which wraps another iterator with the following semantics:  
Constructor registered_iterator(my_iter): calls k.create_var_group() and stores the group id. my_iter is stored internally.  
Dereference: returns an object with operator<<:  
  
```  
k.enter_var_group(group_index);  
k<<*my_iter;  
k.leave_var_group();  
```  
  
usage:  
  
```  
registered_iterator<MyIter> registered_iter(my_iter);  
//instead k<< *my_iter;  
k<<*registered_iter;  
```

---

## Comment 4

> Username: ddemidov  
> Created at: 2016-11-09 10:18:29 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-259379816  

I am sorry for the shameless plug, but you could use [vexcl](https://github.com/ddemidov/vexcl) with the boost.compute backend (see http://vexcl.readthedocs.io/en/latest/interop.html). This will solve the problem with scalar kernel arguments, since vexcl by default treats scalar variables as kernel parameters.  
  
``` cpp  
void add_scalar(bc::vector<float>& v, float scalar){  
    auto unary = bc::bind(bc::plus<float>(),bc::placeholders::_1, scalar);//_1+scalar  
    bc::transform(v().begin(),v().end(), v().begin(), unary);  
}  
...  
bc::vector<float> v(10000,1.0);  
for(int i = 0; i != 1000; ++i)  
    add_scalar(v,0.5*i);  
```  
  
becomes  
  
``` cpp  
for(int i = 0; i != 1000; ++i)  
    v += 0.5 * i;  
```  
  
which results in single kernel generated and applied many times. The problem with unique variables is solved by tagging the variables: http://vexcl.readthedocs.io/en/latest/expressions.html#tagged-terminals

---

## Comment 5

> Username: Ulfgard  
> Created at: 2017-05-13 12:53:49 UTC  
> Updated at: 2017-05-13 13:01:45 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-301246423  

Any news on this? This is a _real_ showstopper for me. The biggest issue I have is that all kinds of indexing into a buffer leads to recompilation of the kernel. E.g. when i use the row-major matrix layout, i index the buffer as `val(x,y)=buf[x*cols+y]`.   
And since i have no way to store cols uniquely as an argument, every time i use an algorithm on a matrix of different size, the corresponding kernel is recompiled. This also happens in all kinds of vector operations, e.g. strided_iterator (taking the row or column of a matrix) or buffer_iterator with index != 0 (taking a different row of a matrix as `index = x * cols`).  
  
This is not an easy issue to fix and my first few own-rolled attempts failed in the planing stage. The only solution i see is that when we get an iterator, we have to pre-register all its constants. This means that for example the strided iterator registers the index and gets a unique argument name returned which is then used instead of the constant when the kernel source is generated via op<<.  
  
In practice this would mean that we have a strided_iterator and a kernel_strided_iterator. The latter is constructed from the strided_iterator and the kernel and does all the registering and saving of the argument name in the constructor. We then only use the kernel_strided_iterator during generation of the kernel code.

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-05-13 13:42:51 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-301249187  

To be honest, I just don't have enough free time to think about this. Currently, I'm trying to hunt and fix remaining bugs and and missing wrappers for API calls introduced in 2.1 etc.  
  
>This is not an easy issue to fix and my first few own-rolled attempts failed in the planing stage. The only solution i see is that when we get an iterator, we have to pre-register all its constants. This means that for example the strided iterator registers the index and gets a unique argument name returned which is then used instead of the constant when the kernel source is generated via op<<.  
  
> In practice this would mean that we have a strided_iterator and a kernel_strided_iterator. The latter is constructed from the strided_iterator and the kernel and does all the registering and saving of the argument name in the constructor. We then only use the kernel_strided_iterator during generation of the kernel code.  
  
Yeah, I think for iterators it may be possible resolve it by changing how they are processed by `meta_kernel` in `<<` operator. We would also have to change iterators a little bit. However, I think we would not anything like `kernel_strided_iterator`. I may be wrong. Anyway, it's a lot of trying and testing, and doing it for Boost.Compute functions and lambdas would be even harder. I don't really have resources to do that.

---

## Comment 7

> Username: Ulfgard  
> Created at: 2017-05-16 07:41:00 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-301701692  

If we have a working proposal, i can invest the time to add and thest the changes in the meta_kernel as well as some of the iterators. I am stuck with this for some time, i can as well work on it.

---

## Comment 8

> Username: jszuppe  
> Created at: 2017-05-16 15:07:23 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-301812360  

>If we have a working proposal, i can invest the time to add and thest the changes in the meta_kernel as well as some of the iterators. I am stuck with this for some time, i can as well work on it.  
  
OK, I'll try to find time to work on this after I adjust Boost.Compute to changes introduced in OpenCL 2.1 and 2.2.

---

## Comment 9

> Username: Ulfgard  
> Created at: 2017-10-02 10:51:15 UTC  
> Updated at: 2017-10-02 10:51:32 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-333501317  

I worked myself a bit on a solution, see lines 581-607  
  
https://github.com/Shark-ML/Remora/blob/1b6aba317eaa381215e9c273247cbf73c8ce9f6a/include/remora/gpu/traits.hpp#L581  
  
This is a solution were functions with arguments are transformed into functions with a registered variable name. Line 609 gives an example for such a function with a bind_second.  Basically a call to bind_second(f,t) (where t is the argument) is replaced by bind_second(f, "variable_name_of_t") and t is registered as a kernel argument.

---

## Comment 10

> Username: rgreenblatt  
> Created at: 2019-07-11 14:03:25 UTC  
> Url: https://github.com/boostorg/compute/issues/663#issuecomment-510499267  

I have found a hacky solution. By using a permutation iterator combined with a constant iterator for the index, it is possible to have an array which is indexed at zero (aka a scalar). The performance for this should be fine, but this may be worse than an actual scalar parameter. Here is an example:  
```  
#include <boost/compute.hpp>  
  
namespace bc = boost::compute;  
  
template <class Element> struct ScalarRange {  
  inline auto begin() const {  
    return bc::make_permutation_iterator(holder.begin(),  
                                         bc::make_constant_iterator(0));  
  }  
  
  inline auto end() const {  
    // + size is required (but it really shouldn't be, this comes down to  
    // permutation_iterator)  
    return bc::make_permutation_iterator(holder.begin() + size,  
                                         bc::make_constant_iterator(0, size));  
  }  
  
  ScalarRange(Element value, size_t size,  
              bc::command_queue command_queue = bc::system::default_queue())  
      : size(size) {  
    holder.push_back(value, command_queue);  
  }  
  
private:  
  bc::vector<Element> holder;  
  size_t size;  
};  
  
int main(int argc, char *argv[]) {  
  bc::float4_ val = {1, 3, 38.3838, 3};  
  const unsigned size = 10;  
  ScalarRange<bc::float4_> scalar_range(val, size);  
  
  bc::vector<bc::float4_> out(size);  
  
  BOOST_COMPUTE_FUNCTION(bc::float4_, multiply, (bc::float4_ val_1, int val_2),  
                         { return val_1 + val_2; });  
  
  bc::transform(bc::make_zip_iterator(boost::make_tuple(  
                    scalar_range.begin(), bc::counting_iterator<int>(0))),  
                bc::make_zip_iterator(boost::make_tuple(  
                    scalar_range.end(), bc::counting_iterator<int>(size))),  
                out.begin(), bc::detail::unpack(multiply));  
  std::vector<bc::float4_> out_cpu(10);  
  bc::copy(out.begin(), out.end(), out_cpu.begin());  
  for (unsigned i = 0; i < size; ++i) {  
    std::cout << out_cpu[i] << "\n";  
  }  
  
  return 0;  
}  
```  
The kernel generated for this looks like:  
```  
inline float4 multiply(float4 val_1, int val_2){ return val_1 + val_2; }  
  
  
__kernel void copy(__global float4* _buf0, __global float4* _buf1, const uint count)  
{  
uint index = get_local_id(0) + (512 * get_group_id(0));  
for(uint i = 0; i < 4; i++){  
    if(index < count){  
_buf0[index]=multiply(_buf1[0], (0+index));  
       index += 128;  
    }  
}  
  
}  
```
