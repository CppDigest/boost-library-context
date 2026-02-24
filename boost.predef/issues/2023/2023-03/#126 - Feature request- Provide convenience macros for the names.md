# #126 - Feature request: Provide convenience macros for the names [Open]

> Username: stertingen  
> Created at: 2023-03-03 14:41:41 UTC  
> Updated at: 2023-04-02 05:04:01 UTC  
> Url: https://github.com/boostorg/predef/issues/126  

I would like to propose providing the name of a detected component in a universal macro.  
  
- `BOOST_LANG_NAME` shall be defined as detected language name  
- `BOOST_ARCH_NAME` shall be defined as detected architecture name  
and so on for `BOOST_COMP_NAME`, `BOOST_LIB_C_NAME`, `BOOST_LIB_STD_NAME`, `BOOST_LIB_OS_NAME`, maybe for `BOOST_PLAT_NAME`.  
  
The primary use case of this proposal is conveniently printing some build information to the user, allowing better bug reports.  
  
I understand that some of those definitions are ambiguous, for example `BOOST_ARCH_NAME` could be defined to either `BOOST_ARCH_X86_NAME` or `BOOST_ARCH_X86_64_NAME` on x86-64.  
In these cases, I would propose using either the 'leaf' names which are the most specific or the first ones detected.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2023-04-02 05:03:58 UTC  
> Url: https://github.com/boostorg/predef/issues/126#issuecomment-1493224945  

I like the idea. I'll see what I can come up with.
