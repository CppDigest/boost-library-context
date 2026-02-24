# #701 - Cannot open boost lib file when building CLI dll [Open]

> Username: MajorMajor0  
> Created at: 2022-10-27 04:13:24 UTC  
> Updated at: 2022-10-27 05:12:20 UTC  
> Url: https://github.com/boostorg/boost/issues/701  

I am trying to build a CLR dll file in VS22 and dotnet 6.0 that references boost. When I build, I get the following error.  
LNK1104 cannot open file 'libboost_program_options-vc142-mt-x64-1_76.lib'  
  
- This file does exist.  
- I have restarted to make sure the file isn't locked.  
- I have tried building as 32 and 64 bit.  
- I have tried 3 different versions of boost  
  
I can build with boost in projects that are not CLI dlls.  
  
The problem is reproduced by this project: https://github.com/MajorMajor0/boostRepro.  
Any help would be much appreciated.

---

## Comment 1

> Username: mclow  
> Created at: 2022-10-27 04:17:56 UTC  
> Url: https://github.com/boostorg/boost/issues/701#issuecomment-1292948669  

A much better place to ask this is on the boost-users mailing list: [boost-users@lists.boost.org](mailto:boost-users@lists.boost.org). Many more people will see it there.

---

## Comment 2

> Username: MajorMajor0  
> Created at: 2022-10-27 05:12:20 UTC  
> Url: https://github.com/boostorg/boost/issues/701#issuecomment-1293006080  

> A much better place to ask this is on the boost-users mailing list: [boost-users@lists.boost.org](mailto:boost-users@lists.boost.org). Many more people will see it there.  
  
Done. Thanks.
