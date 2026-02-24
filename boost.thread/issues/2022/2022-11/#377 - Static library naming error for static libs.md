# #377 - Static library naming error for static libs [Open]

> Username: ElDuderinoBerlin  
> Created at: 2022-11-14 23:36:02 UTC  
> Updated at: 2022-11-15 01:12:55 UTC  
> Url: https://github.com/boostorg/thread/issues/377  

For static compilation i should get i.e.:  
  
libboost_thread-vc143-mt-sgd-x64-1_80.lib  
  
but i get:  
  
libboost_thread-vc143-mt-gd-x64-1_80.lib  
  
when using b2.  
  
's' forgotten for -sgd-.  
  
Error: fatal error LNK1104: cannot open file 'libboost_thread-vc143-mt-sgd-x64-1_80.lib'
