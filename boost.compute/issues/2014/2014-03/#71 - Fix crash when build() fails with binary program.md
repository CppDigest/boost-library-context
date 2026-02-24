# #71 - Fix crash when build() fails with binary program [Closed]

> Username: kylelutz  
> Created at: 2014-03-19 02:06:25 UTC  
> Updated at: 2014-08-03 04:58:59 UTC  
> Closed at: 2014-08-03 04:58:59 UTC  
> Url: https://github.com/boostorg/compute/issues/71  

When compiling with `BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION`, calls to `build()` which fail will cause the program source and build log to be printed to stderr.   
  
However, when the program was loaded from a binary no source exists and thus the call to `source()` made by the debugging output will result in an exception being thrown. This case should be checked for and reported instead of crashing with the exception.

---

## Comment 1

> Username: f-koehler  
> Created at: 2014-07-30 17:45:56 UTC  
> Updated at: 2014-07-30 18:03:11 UTC  
> Url: https://github.com/boostorg/compute/issues/71#issuecomment-50652694  

I have a question about this issue. Is crashing not what the library should do? If building with a binary fails this indicates that something went wrong, for example that the binary was not compatible, etc. Is there a scenario where we want to continue the execution of the host program? Or do you mean that we don't want the exception to be thrown by `get_info<std::string>(CL_PROGRAM_SOURCE)` which fails to fetch the source code that is not available? In that case one could just catch exceptions of `source()` and then throw a new one indicating that no source code is available, like [here](https://github.com/f-koehler/compute/blob/issue71/include/boost/compute/program.hpp#L266) (something went wrong with my code indentation, I read the rules).  
  
Maybe you need to clarify what you mean.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-08-03 04:58:55 UTC  
> Url: https://github.com/boostorg/compute/issues/71#issuecomment-50982195  

Thanks for looking into this!  
  
If `program::create_with_binary()` or `program::build()` fails, we should throw an exception (and definitely never crash). I think all that would be needed is to try to call `program::source()` and not print out anything if it's a binary program. And perhaps `program::source()` should be robust to binary programs and return an empty string in that case.  
  
However, I only saw this once haven't been able to reproduce it since (could have just been something weird going on with my system). I'll close this for now, but if you (or anyone) is able to reproduce this, please re-open with more information about the crash.
