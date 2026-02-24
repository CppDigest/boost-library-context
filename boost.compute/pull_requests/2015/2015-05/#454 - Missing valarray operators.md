# #454 Missing valarray operators [Merged]

> Username: jszuppe  
> Created at: 2015-05-10 21:18:52 UTC  
> Updated at: 2015-05-15 16:57:40 UTC  
> Merged at: 2015-05-15 16:41:39 UTC  
> Closed at: 2015-05-15 16:41:39 UTC  
> Url: https://github.com/boostorg/compute/pull/454  

This addresses issue https://github.com/boostorg/compute/issues/381.  
  
I've implemented all missing valarray operators (see [valarray operators](http://www.cplusplus.com/reference/valarray/valarray/operators/)) and added tests for them.  
  
Implemented operators work only for default context (queue) and are asynchronous.  
  
Note that in OpenCL it's impossible to create memory buffer with bool type and because of that `valarray<char>` is used  as a return type for comparison and logical operators instead of `valarray<bool>`.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-05-11 18:04:47 UTC  
> Updated_at: 2015-05-11 20:36:22 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101000961  

I fixed some errors I did not encountered in my environment:  
- empty macro argument (I did not run into this problem, cause by mistake I was compiling without `-pedantic -Werror` options.)  
- 'bind' function call error (Somehow bind call works for me correctly without ::boost::compute::)  
  
Now it seems that my valarray tests are too long :/ They work perfectly for me with g++. I will try with clang. Maybe I did sth the wrong way and when compiling with clang some tests freeze.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-05-12 21:53:06 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101432461  

Travis build failed with `No output has been received in the last 10 minutes, this potentially indicates a stalled build or something wrong with the build itself.` error.  
  
On my system all 37 valarray tests work perfectly, both with g++4.8.2 and clang 3.4. I would be grateful if someone would confirm that.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-05-12 22:33:15 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101444670  

I can confirm it passes locally on my laptop (Mac OSX, NVIDIA GPU, clang). The failure on Travis is strange, I wish the output had more information.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-05-12 22:34:29 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101445061  

But it is good to see how comprehensive your tests are :-).

---

## Comment 5

> Username: jszuppe  
> Created_at: 2015-05-12 22:51:49 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101450441  

Thanks :-) I try to test all code I commit to Boost Compute.  
  
My best guess now is that Travis was busy and somehow valarray tests were not completed before mentioned ten minutes passed.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2015-05-12 23:05:19 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101454534  

I checked my code again and I saw I did not add static assert  in unary operators checking if valarray T type is a fundamental type. I will fix it tomorrow morning. (Of course it's not connected to Travis failure.)

---

## Comment 7

> Username: jszuppe  
> Created_at: 2015-05-13 10:45:30 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101621269  

I will check if the failure is caused by too many tests or some specific test freezes. I think running valarray tests with --log_level=all option would help find the problem, I'll try this when I get home.   
  
The only difference between my environment and that on Travis (apart from OpenCL drivers) is boost version - I have 1.55, Travis uses 1.48. Sadly I have some odd problems building this version, so I can't recreate exact same environment on my computer.

---

## Comment 8

> Username: jszuppe  
> Created_at: 2015-05-13 17:44:51 UTC  
> Updated_at: 2015-05-14 10:35:29 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-101756466  

I located the problem - remainder binary operator test. This is [build with remainder binary operator test](https://travis-ci.org/boostorg/compute/jobs/62433080), which fails to complete valarray tests due to `fatal error in "modulus_binary_operator": signal: integer divide by zero; address of failing instruction: 0x7f82a29a841b`, and this is [build without it](https://travis-ci.org/boostorg/compute/jobs/62435729), that works perfectly (I've temporally modified .travis.yml so it builds only valarray tests - it's faster that way).   
  
Still, I don't know why this problem occurs. I think test data is correct and IMO this test should work.  
  
Update: I've fixed links to builds with and without remainder binary operator test.

---

## Comment 9

> Username: kylelutz  
> Created_at: 2015-05-15 16:41:47 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-102455678  

Looks great! Merged!

---

## Comment 10

> Username: jszuppe  
> Created_at: 2015-05-15 16:57:40 UTC  
> Url: https://github.com/boostorg/compute/pull/454#issuecomment-102458462  

Thanks!

---
