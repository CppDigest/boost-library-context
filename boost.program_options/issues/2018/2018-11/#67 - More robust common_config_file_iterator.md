# #67 - More robust common_config_file_iterator [Open]

> Username: DesWurstes  
> Created at: 2018-11-22 13:01:02 UTC  
> Updated at: 2020-10-23 21:27:24 UTC  
> Url: https://github.com/boostorg/program_options/issues/67  

common_config_file_iterator::common_config_file_iterator has a little problem: One of the args is std::set, which is incompatible between libc++ and libstdc++. As a result, the function, if compiled with {GCC, Clang}, is incompatible with {Clang, GCC). This can be fixed without breaking anything by moving it to the header part and declaring it static. What are your opinions about it?

---

## Comment 1

> Username: xloem  
> Created at: 2020-10-23 21:27:24 UTC  
> Url: https://github.com/boostorg/program_options/issues/67#issuecomment-715597957  

Could try a PR, see if that stimulates a reply.  If not it might be up to those interested in the change to advocate for addressing it.
