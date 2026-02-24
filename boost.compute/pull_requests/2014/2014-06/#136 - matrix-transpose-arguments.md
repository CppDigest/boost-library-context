# #136 matrix-transpose-arguments [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-10 11:19:36 UTC  
> Updated at: 2014-06-13 03:28:59 UTC  
> Closed at: 2014-06-13 03:28:45 UTC  
> Url: https://github.com/boostorg/compute/pull/136  

Hi,  
changes to matrix_transpose to allow specifying the matrix size on the command line.  
  
using  
example/CmakeLists.txt from previous pull-request for program-options.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-06-11 01:32:36 UTC  
> Url: https://github.com/boostorg/compute/pull/136#discussion_r13629858  

Why are these in multiples of `1024`? It would be better just to let the user specify what size they actually want. Also, I think it would be more intuitive to name the arguments `"rows"` and `"cols"` (or just `"size"` if the matrix is required to be square).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-11 01:35:16 UTC  
> Url: https://github.com/boostorg/compute/pull/136#discussion_r13629905  

I don't think this should be wrapped in a huge `try`/`catch` block. If something goes wrong, the default unhandled-exception handler will be executed and it will print out the error string for us (making the catch down below unnecessary).

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-06-11 03:13:00 UTC  
> Updated_at: 2014-06-11 03:15:17 UTC  
> Url: https://github.com/boostorg/compute/pull/136#issuecomment-45697299  

I've updated the `example/CMakeLists.txt` file to link all the examples with Boost program_options so you shouldn't get any more errors building on Travis-CI. Just rebase your changes on top of the current master branch and it should work.  
  
The commit is here 5f406314aec225e1598b0a6a3dab12b67c70c179 if you want to take a look.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-06-13 03:28:45 UTC  
> Updated_at: 2014-06-13 03:28:59 UTC  
> Url: https://github.com/boostorg/compute/pull/136#issuecomment-45972487  

I went ahead and implemented this. See the commit here c1d867ba48b85ebba8c7a855e022dd52ebea2b00.  
  
I think something similar could be done for the opencv_optical_flow example (which could then be combined with the opencv_optical_flow_camera example and switched using command line arguments).

---
