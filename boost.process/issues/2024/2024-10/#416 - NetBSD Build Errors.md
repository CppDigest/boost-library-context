# #416 - NetBSD Build Errors [Open]

> Username: ghost  
> Created at: 2024-10-29 07:52:25 UTC  
> Updated at: 2025-01-26 21:10:00 UTC  
> Url: https://github.com/boostorg/process/issues/416  

Got someone else online to test boost process for me on their NetBSD machine and these are the errors they got:  
  
```  
In file included from ../../../../libs/filesystem/src/directory.cpp:38:  
../../../../libs/filesystem/src/directory.cpp: In function ‘int boost::filesystem::detail::{anonymous}::dir_itr_fd(const boost::filesystem::detail::dir_itr_imp&, boost::system::error_code&)’:  
../../../../libs/filesystem/src/directory.cpp:273:16: error: expected id-expression before ‘(’ token  
  273 |     int fd = ::dirfd(static_cast< DIR* >(imp.handle));  
      |                ^~~~~  
```  
```  
In file included from ../../src/posix/close_handles.cpp:88:  
../../src/posix/close_handles.cpp: In function ‘void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)’:  
../../src/posix/close_handles.cpp:188:21: error: expected id-expression before ‘(’ token  
  188 |     auto dir_fd = ::dirfd(dir.get());  
      |                     ^~~~~  
```  
  
Note, the above errors should be all that's left hopefully once [my recent pull request](https://github.com/boostorg/process/pull/415) gets merged.  
  
Special thanks to @NishiOwO for testing boost process on NetBSD using my pull request's branch.

---

## Comment 1

> Username: barracuda156  
> Created at: 2025-01-26 21:09:58 UTC  
> Url: https://github.com/boostorg/process/issues/416#issuecomment-2614592432  

@time-killer-games The same error on macOS: https://github.com/boostorg/process/issues/461
