# #95 - Use library as header only? [Open]

> Username: correaa  
> Created at: 2020-04-23 09:23:22 UTC  
> Updated at: 2022-09-03 21:34:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/95  

Is it possible to use the library as header only for example by defining a macro?  
Would it be in principle be possible? (e.g. by systematically moving code from the cpp files to the hpp files).  
  
Is a header only version of the library being considered?

---

## Comment 1

> Username: anthonyaue  
> Created at: 2021-01-15 03:45:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/95#issuecomment-760626307  

This would definitely make the library easy to use. If the maintainers support it I'd be happy to take a crack.

---

## Comment 2

> Username: mattgruenke  
> Created at: 2022-09-03 21:33:39 UTC  
> Updated at: 2022-09-03 21:34:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/95#issuecomment-1236201597  

Build times for files simply including `program_options.hpp` are _already_ quite long.  The whole reason I'm browsing the issues is to see if anyone filed a bug or pull request to further optimize the build times of users.  If not, I'll probably take a swing at it, which will include moving a few more of the unnecessary inlines to .cpp files.  
  
If support is added for using it header-only, I just hope that it's done in a way that doesn't hurt build times for the rest of us.
