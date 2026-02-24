# #157 - Set sticky bit on shared interprocess folder [Closed]

> Username: MarcusAckermann  
> Created at: 2021-10-21 11:41:57 UTC  
> Updated at: 2021-11-07 20:12:20 UTC  
> Closed at: 2021-11-07 20:11:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/157  

It is considered as a CVE to have a world-writable directory without the sticky bit set on Unix filesystems.  
  
The shared interprocess folder (/tmp/boost_interprocess) which is created by `create_shared_dir_and_clean_old` in `boost/interprocess/detail/shared_dir_helpers.hpp` explicitly gets the permissions `0777` which makes it a world-writable directory without the sticky bit.  
  
This folder should be created with `1777` instead to have the sticky bit set. The `/tmp` folder has the sticky bit set.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-11-07 20:12:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/157#issuecomment-962673227  

Many thanks for the report!
