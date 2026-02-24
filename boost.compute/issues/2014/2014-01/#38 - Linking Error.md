# #38 - Linking Error [Closed]

> Username: aditya4d  
> Created at: 2014-01-06 02:45:19 UTC  
> Updated at: 2014-01-07 20:23:49 UTC  
> Closed at: 2014-01-07 20:22:47 UTC  
> Url: https://github.com/boostorg/compute/issues/38  

Hi,  
When I tried `g++ -I/usr/local/cuda-5.5/include -I/home/frost/Documents/compute/include test_system.cpp -lOpenCL`. I am getting the error as in the screenshot.  
  
![screenshot from 2014-01-05 21 43 12](https://f.cloud.github.com/assets/2188479/1848137/836e4532-767c-11e3-8405-813d3052d408.png)

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-01-06 02:52:10 UTC  
> Updated at: 2014-01-07 16:21:43 UTC  
> Url: https://github.com/boostorg/compute/issues/38#issuecomment-31624211  

Add the `-lboost_unit_test_framework` and `-DBOOST_TEST_DYN_LINK` options to your command.  
  
For reference, here's what I used:  
`g++ -DBOOST_TEST_DYN_LINK -I /usr/include/nvidia-319/ -I /home/kyle/dev/compute/include/ test_system.cpp -lOpenCL -lboost_unit_test_framework`

---

## Comment 2

> Username: aditya4d  
> Created at: 2014-01-07 12:41:01 UTC  
> Url: https://github.com/boostorg/compute/issues/38#issuecomment-31734110  

Hi Kyle,  
It did work. I have two questions.  
1. What exactly does the 2 flags do?  
2. Why I don't need them while compiling the files not in the test folder? (eg. `sort.cpp` in the example)

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-01-07 16:24:32 UTC  
> Updated at: 2014-01-07 20:23:49 UTC  
> Url: https://github.com/boostorg/compute/issues/38#issuecomment-31752259  

The source files under `test/` are Boost.Compute unit tests and require the Boost.Test library. The flags I gave above are required to link to the Boost.Test library.  
  
Other files, such as the examples under the `example/` directory, are not unit tests and don't require the Boost.Test library and thus don't need those flags. You really shouldn't be compiling the unit tests by hand, the CMake build system takes care of that. If you're looking for small, compilable sample code check out the `example/` directory.

---

## Comment 4

> Username: aditya4d  
> Created at: 2014-01-07 20:22:47 UTC  
> Url: https://github.com/boostorg/compute/issues/38#issuecomment-31775494  

Hi Kyle,  
That's helpful. Thank you.
