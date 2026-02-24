# #512 - -Wdeprecated-literal-operator from clang++-20 for 'operator"" _c' (i.e. with space) [Open]

> Username: MichaelChirico  
> Created at: 2025-03-12 17:12:11 UTC  
> Updated at: 2025-03-12 17:12:11 UTC  
> Url: https://github.com/boostorg/config/issues/512  

https://github.com/boostorg/config/blob/404c7831d92646cdee4080050f5348743a05a568/test/boost_no_cxx11_user_lit.ipp#L50-L55  
  
The fix _can_ be trivial, but I think there's some back-compatibility considerations I lack the context to adjudicate, see sister issues e.g.  
  
https://github.com/boostorg/hana/pull/521
