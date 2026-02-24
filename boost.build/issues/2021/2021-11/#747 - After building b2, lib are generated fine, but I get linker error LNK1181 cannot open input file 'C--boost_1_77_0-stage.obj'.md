# #747 - After building b2, lib are generated fine, but I get linker error LNK1181 cannot open input file 'C:\boost_1_77_0\stage.obj' [Closed]

> Username: hule11  
> Created at: 2021-11-17 20:35:02 UTC  
> Updated at: 2021-11-17 20:35:17 UTC  
> Closed at: 2021-11-17 20:35:16 UTC  
> Url: https://github.com/boostorg/build/issues/747  

After building bootstrap, it generated b2 but it did not generate bjam(I guess that should be fine because b2 is the new name and bjam is the old one). All the libraries are generated fine in c/boost/Stage/lib. But I get this error now LNK1181. looks like that stage.obj file is not generated, not sure why.  
  
Is there any way I can Ignore this error? This code was written in 1997 maybe in VS2013, we don't even use boost anymore. But there are so many dependencies, I have to use it. FYI same code works fine in VS2003.

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-11-17 20:35:16 UTC  
> Url: https://github.com/boostorg/build/issues/747#issuecomment-971975668  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
