# #746 - Memory leak [Closed]

> Username: wincrash  
> Created at: 2017-10-11 07:59:08 UTC  
> Updated at: 2018-05-14 02:54:06 UTC  
> Closed at: 2018-05-13 20:14:42 UTC  
> Url: https://github.com/boostorg/compute/issues/746  

Hello,  
I have found that using sort or sort_by_key command multiple times i get memory leak (memory usage is growing). In follow example i get the memory leak, if i just comment the line "boost::compute::sort" memory leak disappears. Why that happening? Maybe i doing something wrong?   
  
  
```cpp  
#include <iostream>  
#include <boost/compute/core.hpp>  
#include <boost/compute.hpp>  
namespace compute = boost::compute;  
int main()  
{  
    auto device = boost::compute::system::devices().at(0);  
    auto context = boost::compute::context(device);  
    auto queue = boost::compute::command_queue(context, device);  
    std::cout << device.name() << "\n";  
      
    int count=1000000;  
    std::vector<float> a;  
    std::vector<float> b;  
    std::vector<float> c;  
    a.resize(count);  
    b.resize(count);  
    c.resize(count);  
      
    for(int i=0;i<count;i++){  
        a[i]=i*2+1;  
        b[i]=(i+1)*2;  
        c[i]=0;  
    }  
    compute::vector<float> buffer_a( context);  
    compute::vector<float> buffer_b( context);  
    compute::vector<float> buffer_c(context);  
    buffer_a.resize(count,queue);  
    buffer_b.resize(count,queue);  
    buffer_c.resize(count,queue);  
      
    boost::compute::copy(a.begin(),a.end(),buffer_a.begin(),queue);  
    boost::compute::copy(b.begin(),b.end(),buffer_b.begin(),queue);  
    boost::compute::copy(c.begin(),c.end(),buffer_c.begin(),queue);  
      
    // source code for the add kernel  
    const char source[] =  
            "__kernel void add(__global const float *a,"  
            "                  __global const float *b,"  
            "                  __global float *c)"  
            "{"  
            "    const uint i = get_global_id(0);"  
            "    c[i] = a[i] + b[i];"  
            "}";  
      
    // create the program with the source  
    compute::program program =  
            compute::program::create_with_source(source, context);  
      
    // compile the program  
    program.build();  
      
    // create the kernel  
    compute::kernel kernel(program, "add");  
      
    // set the kernel arguments  
    kernel.set_arg(0, buffer_a);  
    kernel.set_arg(1, buffer_b);  
    kernel.set_arg(2, buffer_c);  
      
    // run the add kernel  
    for(int i=0;i<100000;i++){  
        queue.enqueue_1d_range_kernel(kernel, 0, count, 0);  
        boost::compute::sort(buffer_c.begin(),buffer_c.end(),queue);  
        queue.finish();  
    }  
    return 0;  
}  
```

---

## Comment 1

> Username: illuhad  
> Created at: 2018-01-16 15:49:05 UTC  
> Updated at: 2018-01-16 19:53:42 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-358006292  

Hi,  
  
this issue is currently the major showstopper for my application, so I did some investigating. It turns out that the culprit is not sort() but exclusive_scan() which is called by boost::compute's radix sort implementation. In other words, replace your sort() calls with calls to exclusive_scan() and you will see the same behavior.  
  
I could further trace the problem to detail::scan_impl(). Curiously, the problem does not affect inclusive_scan, which also makes use of scan_impl(), and scan_impl() also works fine if the exclusive boolean parameter is set to false.  
  
The weird thing is that scan_impl() itself doesn't even seem to depend on the exclusive boolean parameter, it is simply forwarded to local_scan_kernel. However, as a meta kernel object, local_scan_kernel just builds kernel source code which is slightly different if exclusive==true. How could this leak memory? Or am I on the wrong track? I'm not very familiar with internals of boost::compute, so it could well be that I've missed something.  
  
Update: I've found that if I change the line 113 in scan_on_gpu.hpp (this is in the local_scan_kernel) from  
`op(first[expr<cl_uint>("gid")], var<T>("scratch[lid]"))`  
to  
`first[expr<cl_uint>("gid")] <<"+"<<var<T>("scratch[lid]"))`  
the problem disappears. It's still unclear to me how the call to the binary operator could cause such a massive memory leak. Since the call to `op()` with two `var<T>()`  arguments a few lines above doesn't cause any issues, I suspect it might be the combination of `op()` with `first[expr...]`?

---

## Comment 2

> Username: illuhad  
> Created at: 2018-01-16 20:27:16 UTC  
> Updated at: 2018-01-16 20:30:17 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-358094147  

It is indeed the combination of `op()` and `first[expr...]`. Replacing the code starting at line 111 of scan_on_gpu.hpp with  
```c++  
"if(lid == block_size - 1){\n" <<  
"    " << decl<T>("temp") << " = " << first[expr<cl_uint>("gid")] <<";\n"<<  
"    block_sums[get_group_id(0)] = " <<''  
               op(var<T>("temp"), var<T>("scratch[lid]")) <<  
                ";\n" <<  
"}\n";  
```  
gets rid of the memory leak. This could be a possible workaround until the root cause is found.

---

## Comment 3

> Username: ghavasi  
> Created at: 2018-03-05 15:58:27 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-370466309  

The compute::reduce() function also leaks (except if the binary function is compute::plus<> )  
See the following example:  
  
```  
BOOST_AUTO_TEST_CASE(reduce_twos)  
{  
    using compute::uint_;  
  
    compute::vector<uint_> vector(8, context);  
    const compute::buffer& buff = vector.get_buffer();  
    auto rc1 = buff.get_info<CL_MEM_REFERENCE_COUNT>();  
    {  
        compute::fill(vector.begin(), vector.end(), uint_(2), queue);  
  
        uint_ product;  
        compute::reduce(vector.begin(), vector.end(), &product, compute::multiplies<uint_>(), queue);  
        BOOST_CHECK_EQUAL(product, uint_(256));  
    }  
    auto rc2 = buff.get_info<CL_MEM_REFERENCE_COUNT>();  
    BOOST_CHECK_EQUAL(rc1, rc2);  
}  
  
```  
  
The two reference count should be equal.  
The problem comes from the instruction  
```  
size_t detail::reduce(InputIterator first, size_t count, OutputIterator result,  
              size_t block_size, BinaryFunction function, command_queue &queue)  
...  
    k <<   
        function(first[k.make_var<uint_>("gid*2+0")],  
                        first[k.make_var<uint_>("gid*2+1")]) << ";\n";  
  
```  
  
IMHO the   
```  
    ~buffer_iterator_index_expr()  
    {  
        // set buffer to null so that its reference count will  
        // not be decremented when its destructor is called  
        m_buffer.get() = 0;  
    }  
  
```  
leaves the extra buffer reference.

---

## Comment 4

> Username: antibes0415  
> Created at: 2018-05-11 09:14:53 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-388308954  

@jszuppe  Hi. I used exclusive_scan and reduce function. My program is also faced with memory leak. Could you check and modify the code ?

---

## Comment 5

> Username: jszuppe  
> Created at: 2018-05-12 09:33:43 UTC  
> Updated at: 2018-05-12 09:34:08 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-388542951  

> IMHO the  
```cpp  
    ~buffer_iterator_index_expr()  
    {  
        // set buffer to null so that its reference count will  
        // not be decremented when its destructor is called  
        m_buffer.get() = 0;  
    }  
```  
> leaves the extra buffer reference.  
  
It should not leave extra buffer reference since in the constructor:  
```cpp  
    buffer_iterator_index_expr(const buffer &buffer,  
                               size_t index,  
                               const memory_object::address_space address_space,  
                               const IndexExpr &expr)  
        : m_buffer(buffer.get(), false),  
         ...  
```  
it does not increase the reference counter of buffer. However, I'll check it.

---

## Comment 6

> Username: jszuppe  
> Created at: 2018-05-12 10:11:09 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-388544887  

It seems that default copy ctor is causing this problem. I'll check if that [adding it] fixes the problem in other  reported cases.

---

## Comment 7

> Username: jszuppe  
> Created at: 2018-05-13 20:13:57 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-388652867  

It seems that it fixes all reported issues reported in this thread.

---

## Comment 8

> Username: jszuppe  
> Created at: 2018-05-13 20:14:42 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-388652917  

Should be fixed by #780. Reopen if you still get this problem on `develop` branch.

---

## Comment 9

> Username: antibes0415  
> Created at: 2018-05-14 02:54:06 UTC  
> Url: https://github.com/boostorg/compute/issues/746#issuecomment-388682936  

Thanks. Seems memory leak issue solved.
