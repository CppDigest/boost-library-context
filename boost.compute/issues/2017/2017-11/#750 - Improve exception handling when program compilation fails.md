# #750 - Improve exception handling when program compilation fails [Closed]

> Username: Kristian-Popov  
> Created at: 2017-11-05 19:12:49 UTC  
> Updated at: 2017-11-29 17:23:02 UTC  
> Closed at: 2017-11-29 17:22:42 UTC  
> Url: https://github.com/boostorg/compute/issues/750  

Good day!  
At first I would like to say thank you for a great library that has a lot of already implemented algorithms and data structures plus works as a great OpenCL wrapper for C++ (IMO better than official wrapper).  
  
This is more of a feature request but also an annoyance. All methods that build programs from source like boost::compute::program::build_with_source() are static, so in an event of program build failure, build log is lost. In my program I try to handle these cases, so I was basically forced to reimplement boost::compute::program::build_with_source() in my code and store build log.  
  
This behavior can be improved: instead of throwing generic boost::compute::opencl_error, a new exception class can be added (e.g. "opencl_program_build_failure"). It should inherit opencl_error (this ensures backward compatibility) but it can also contain a reference to a program that failed to build or just a build log.  
  
What do you think about this idea? I have some free time to contribute by adding this, just want to discuss the viability of this approach.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-11-06 11:19:54 UTC  
> Url: https://github.com/boostorg/compute/issues/750#issuecomment-342119937  

It's a good idea. I think that's the best approach if we want to keep that log. You can always define `BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION` to make sure log is printed to the standard output if `clBuildProgram` fails.

---

## Comment 2

> Username: Kristian-Popov  
> Created at: 2017-11-06 18:26:04 UTC  
> Url: https://github.com/boostorg/compute/issues/750#issuecomment-342240106  

Thank you for a fast answer. I'm aware of `BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION` but I find it a little difficult to use. It writes the log to standard error output and can't be trivially separated from other output, plus this is a compile-time setting, so there's no easy way to turn this print on or off after compilation (an example of situation where IMO it can be important is when something weird goes on end-user system). It is a good tool, but rather for a case when code is still on a developer's computer.  
I will try to implement this solution and post a pull request (it may take few days)

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-11-29 17:22:42 UTC  
> Url: https://github.com/boostorg/compute/issues/750#issuecomment-347932898  

Fixed by #752. Thanks for the PR!
