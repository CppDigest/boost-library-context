# #120 - How to add tests for PNG grayalpha fix in #118 [Closed]

> Username: mloskot  
> Created at: 2018-07-23 12:13:32 UTC  
> Updated at: 2019-08-07 20:35:37 UTC  
> Closed at: 2019-08-07 20:33:21 UTC  
> Url: https://github.com/boostorg/gil/issues/120  

@adrianbroher I've created this issue as task to address and your #118  questions:  
  
> Regarding the test cases:  
> I tried to run a cmake build, which failed during the linking stage of the gil_test_ext_io_tiff test  
> executable because of a undefined symbol boost::system::generic_category() so almost  
> all of the io-based test executables are missing and I can't run ctest.  
  
You should be aware, CMake configuration is still an experimental build configuration for Boost.GIL.  
The [CONTRIBUTING.md](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md) tries to explain all necessary steps, but some may be missing. I will look into your issues.  
  
Meanwhile, you may want to try `b2` which is the Boost official build system.  
  
> Also could someone give me advise about writing a test a proper test for the linked issue?  
> I used Boost Test a few times, but the existing io tests don't seem to assert anything about  
> correctly loading an image and I can't think of any proper test assertions, maybe sampling  
> some pixel for the expected values?  
  
I'd try to add a simple test case similar to this one, accompanied with a small test `.png` file:  
  
https://github.com/boostorg/gil/blob/ed67f9a57152624e126d6086f3aa4e6b0f097196/io/test/png_read_test.cpp#L93-L116  
  
Is this helpful?

---

## Comment 1

> Username: adrianbroher  
> Created at: 2018-07-23 21:24:24 UTC  
> Url: https://github.com/boostorg/gil/issues/120#issuecomment-407205922  

> I'd try to add a simple test case similar to this one, accompanied with a small test .png file:  
> Is this helpful?  
  
Yes, that reference is very helpful, thanks.  I'm already working on a test case.  
  
> I will look into your issues.  
  
I would guess that either gil itself or boost filesystem doesn't pull in the Boost::system component into it's target_link_libraries, but adding Boost::system as those (and as a find_package component of course) only changed the missing symbol to `boost::system::detail::generic_category_ncx()` so I didn't investigate further.

---

## Comment 2

> Username: adrianbroher  
> Created at: 2018-07-24 22:20:19 UTC  
> Url: https://github.com/boostorg/gil/issues/120#issuecomment-407570837  

During changes in the project membership will not work on pushing a test case anymore.  Sorry for causing any inconvenience. Feel free to revert #118 as you seem fit.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-07-24 22:40:48 UTC  
> Url: https://github.com/boostorg/gil/issues/120#issuecomment-407575217  

@adrianbroher I'm not clear what you mean. However, I'm going to work on test case and fix the linker issues anyway. Thanks for your help so far.

---

## Comment 4

> Username: mloskot  
> Created at: 2018-07-31 16:20:53 UTC  
> Url: https://github.com/boostorg/gil/issues/120#issuecomment-409281041  

AFAICT, for the test file `io/test_images/png/grayalpha-with-tRNS-chunk.png` added in #118, this call does not return the requested flag, but returns `0` - the data is missing  
  
```cpp  
png_get_valid( this->get_struct(), this->get_info(), PNG_INFO_tRNS )  
```  
  
It looks to me the `grayalpha-with-tRNS-chunk.png` is not quite a valid test image for the #118 fix.
