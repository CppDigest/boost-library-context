# #101 - Forward CFLAGS options to nested bzip2/zlib builds [Open]

> Username: k15tfu  
> Created at: 2019-10-09 09:53:00 UTC  
> Updated at: 2019-10-09 09:53:00 UTC  
> Url: https://github.com/boostorg/iostreams/issues/101  

Hi!  
  
I use CFLAGS/CXXFLAGS="-isysroot <...>" flag to pass system headers location to the compiler. b2 works fine with this and almost all boost libraries are compiled succesfully, except iostreams. When building iostreams with ZLIB/BZIP2, these nested builds fail due to missing isysroot option. How to fix zlib.jam/bzip2.jam to get it work with CFLAGS?
