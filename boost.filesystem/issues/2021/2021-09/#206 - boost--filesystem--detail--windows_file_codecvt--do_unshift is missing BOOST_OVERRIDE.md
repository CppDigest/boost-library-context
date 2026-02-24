# #206 - boost::filesystem::detail::windows_file_codecvt::do_unshift is missing BOOST_OVERRIDE [Closed]

> Username: saurik  
> Created at: 2021-09-03 09:14:04 UTC  
> Updated at: 2021-09-03 10:33:11 UTC  
> Closed at: 2021-09-03 10:24:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/206  

In boost::filesystem::detail::windows_file_codecvt, do_unshift is overriding a virtual function but is missing BOOST_OVERRIDE.  
  
https://github.com/boostorg/filesystem/blob/9794725bda2e612f1fdcadd98cdce5b7bfb548f1/src/windows_file_codecvt.hpp#L52  
  
```  
vpn/p2p/boost/libs/filesystem/src/windows_file_codecvt.hpp:52:31: error: 'do_unshift' overrides a member function but is not marked 'override' [-Werror,-Winconsistent-missing-override]  
    std::codecvt_base::result do_unshift(std::mbstate_t&, char* /*from*/, char* /*to*/, char*& /*next*/) const { return ok; }  
                              ^  
/home/runner/work/orchid/orchid/cli-shared/./env/libcxx/include/__locale:1058:20: note: overridden virtual function is here  
    virtual result do_unshift(state_type& __st,  
                   ^  
1 error generated.  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-09-03 10:24:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/206#issuecomment-912432687  

There is no `do_unshift` member in C++03 standard libraries, so it cannot be marked with `BOOST_OVERRIDE`. `windows_file_codecvt.hpp` is part of the library sources, and library building scripts do not enable `-Werror`. If you're building the library with your own build system, make sure it works equivalently to the official build scripts.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-09-03 10:33:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/206#issuecomment-912437722  

Oh, sorry, I misread the standard. `do_unshift` is indeed present in C++03. Will add `BOOST_OVERRIDE` then. Thanks.
