# #135 feature branch : matrix-multiply-example [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-10 10:22:32 UTC  
> Updated at: 2014-06-20 12:05:44 UTC  
> Closed at: 2014-06-20 06:44:18 UTC  
> Url: https://github.com/boostorg/compute/pull/135  

Hi,  
Review  
'matrix-multiply-example': adding the new multi-threaded  
matrix-multiply example.  
  
I think I am started to get this git stuff :)

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-06-11 01:36:20 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13629934  

can you change these indents to four spaces?

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-11 01:38:03 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13629973  

i don't think this is necessary for demonstrating matrix-matrix multiplication in OpenCL. perhaps it could be moved to a separate example (like `device_info`?).

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-06-11 01:39:15 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13630004  

should this return or rethrow the exception instead of continuing?

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-06-11 01:42:21 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13630089  

this will include a lot of code. it's usually better to just include the headers that you need. for just the wrapper classes you can include `boost/compute/core.hpp`.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-06-11 01:47:26 UTC  
> Url: https://github.com/boostorg/compute/pull/135#issuecomment-45693288  

Looking good! I left some comments inline.  
  
Also, I don't really see the need for multiple host threads here and it complicates the example. I'd rather just have this demonstrate matrix-multiplication on the GPU (perhaps just with the simple host version for verification).

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-06-11 03:14:36 UTC  
> Url: https://github.com/boostorg/compute/pull/135#issuecomment-45697370  

See my comment on PR #136 for info about fixing the build issue with Boost program_options.

---

## Comment 7

> Username: Mageswaran1989  
> Created_at: 2014-06-11 06:53:58 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13636045  

yep I added try block in the later part of the coding. I didn't think of that.  
I am adding return -1

---

## Comment 8

> Username: Mageswaran1989  
> Created_at: 2014-06-11 06:58:43 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13636154  

I removed the compute.hpp and added only the necessary headers from core.hpp.  
  
Can you add #include <boost/compute/source.hpp> to core.hpp for "BOOST_COMPUTE_STRINGIZE_SOURCE"

---

## Comment 9

> Username: Mageswaran1989  
> Created_at: 2014-06-11 07:03:40 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13636271  

Shall I will move it to device_info or hello_world?

---

## Comment 10

> Username: Mageswaran1989  
> Created_at: 2014-06-11 08:05:29 UTC  
> Updated_at: 2014-06-11 08:21:08 UTC  
> Url: https://github.com/boostorg/compute/pull/135#issuecomment-45712979  

I agree on multiple host threads part. However there are plenty of matrix multiplication code and tutorial out there for matrix multiplication, which I tried to cover as much as possible.  I want check how context and queue is getting shared across threads.  
  
The reason I want to bring host threads into this example, is to see how the output prints are behaving, which resembles the basic host thread example.   
  
And yeah if its only matrix multiplication, the example becomes more compact!   
  
What do you think?

---

## Comment 11

> Username: Mageswaran1989  
> Created_at: 2014-06-11 08:18:11 UTC  
> Url: https://github.com/boostorg/compute/pull/135#issuecomment-45714072  

I forgot mention about the other example visualize_kernel.cpp.  
  
Please have a look on that too.

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-06-11 15:31:37 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13655603  

I'd make a new example. `hello_world` is supposed to be very simple.

---

## Comment 13

> Username: kylelutz  
> Created_at: 2014-06-11 15:32:38 UTC  
> Updated_at: 2014-06-13 06:47:37 UTC  
> Url: https://github.com/boostorg/compute/pull/135#discussion_r13655656  

I don't want to add `source.hpp` to `core.hpp` as it is not one of the core wrapper headers. Just add `#include <boost/compute/source.hpp>` here after `#include <boost/compute/core.hpp>`.

---

## Comment 14

> Username: kylelutz  
> Created_at: 2014-06-11 15:37:04 UTC  
> Url: https://github.com/boostorg/compute/pull/135#issuecomment-45758255  

Yeah, I'd like to keep examples as simple as possible. Ideally, they would demonstrate one concept or feature clearly and concisely (which makes it easier for new users to follow along and reduces any extra "noise" in the code).  
  
We could look at adding another example demonstrating sharing boost.compute/opencl objects across multiple threads later (though note that there are still some issues with this, see bug #91).  
  
As for the `visualize_kernel.cpp`, I haven't really taken a look at that (could you give me an overview of what it does and how it relates to matrix-multiplication?). Maybe it would be better to review in a separate pull-request.

---

## Comment 15

> Username: Mageswaran1989  
> Created_at: 2014-06-12 13:21:40 UTC  
> Url: https://github.com/boostorg/compute/pull/135#issuecomment-45890010  

# $ ./matrix_multiplication --size 1024  
  
# Compute Matrix Multiplication example  
  
8x8 Matrix Multiplication via pointers  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
Kernel Names :  matrix_multiply_naive  
        matrix_multiply_block  
8x8 Matrix Multiplication via matrix_multiply_block kernel  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  8  8  8  8  8  8  8  8  
  
1024 x 1024 Matrix Multiplication with both the kernels  
Matrix Size: 1024x1024  
Grid Size  : 256x256 blocks  
Local Size : 4 x 4 threads  
Time taken with naive kernel   : 393277120 ns : 3932 ms : 0.393277 s  
Time taken with improved kernel: 330812896 ns : 3308 ms : 0.330813 s  
  
## visualize_kernel.cpp  
  
Prints the global thread id and local thread id, for ease of understanding.  
  
$ ./visualize_kernel --GlobalThreads_X=8 --GlobalThreads_Y=8 --LocalThreads_X=4 --LocalThreads_Y=4   
Number of groups created / grid size: 2 x 2  
Number of local threads / local size: 4 x 4  
Number of global threads: 8 x 8  
  
GlobalIdXY.LocalIdXY  
  
##     g_xg_y.lxl_y  
  
```  
0.00  100.10  200.20  300.30 |   400.00  500.10  600.20  700.30 |   
1.01  101.11  201.21  301.31 |   401.01  501.11  601.21  701.31 |   
2.02  102.12  202.22  302.32 |   402.02  502.12  602.22  702.32 |   
3.03  103.13  203.23  303.33 |   403.03  503.13  603.23  703.33 |   
  
4.00  104.10  204.20  304.30 |   404.00  504.10  604.20  704.30 |   
5.01  105.11  205.21  305.31 |   405.01  505.11  605.21  705.31 |   
6.02  106.12  206.22  306.32 |   406.02  506.12  606.22  706.32 |   
7.03  107.13  207.23  307.33 |   407.03  507.13  607.23  707.33 |   
```  
  
!!! Zeros pre to the number are ignored while printing !!!

---
