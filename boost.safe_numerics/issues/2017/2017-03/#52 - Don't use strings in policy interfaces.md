# #52 - Don't use strings in policy interfaces [Closed]

> Username: akrzemi1  
> Created at: 2017-03-30 08:01:02 UTC  
> Updated at: 2018-09-23 22:26:37 UTC  
> Closed at: 2018-08-14 22:12:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/52  

Currently, `safe<>` communicates with exception policies by selecting one of the functions, like `EP::overflow_error` and passing it a `const char *` argument. These text arguments represent conditions, like `"multiplication overflow"`.  
  
Instead of passing string literals, provide a set of error conditions encoded in an `enum` or in the form similar to `std::error_code` (the tutorial can be found [here](http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-1.html)). And pass these abstract conditions to exception policy instead. This way user-provided policies are not confined to your choice of text messages, and can be used for more detailed error reporting.  
  
(This has been suggested by Tomasz Kaminski during the review.)

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-10 20:29:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/52#issuecomment-412197266  

I've thought about this some.  
  
First of all, I've completely redesigned the exception mechanism to be much more correct.  Feel free to take a look at it.  
  
I envisioned the string argument as a description of the particular error. I know that Adding another layer is suggested from time to time in other contexts as well as this one.  This is often to better support handling language issues.  Another way to do this (which I would prefer) is to replace the string with a code and use the code to lookup the string it corresponds to - thus addressing the language portability issue.  Of course that begins to look like your solution.  
  
One thing that is holding me up here is that I haven't found the strings especially useful in my numerous examples and tests.  Originally they were meant to help find the exact line of code where the exception was thrown.  But I'm not so sure as the utility of doing this.   I've actually considered eliminating the string entirely.   This is especially true as I've broken out the error types better - undefined, implementation defined, etc.   
  
I'm still thinking about this.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-14 22:12:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/52#issuecomment-413032949  

for now, I'm not going to do this.
