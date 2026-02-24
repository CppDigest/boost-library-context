# #79 - not work on ubuntu 16.04.1 LTS [Closed]

> Username: bajizhh  
> Created at: 2018-10-10 06:26:51 UTC  
> Updated at: 2018-10-10 12:43:16 UTC  
> Closed at: 2018-10-10 11:16:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/79  

**::syscall(SYS_getrandom, buf, size, flags) failed on ubuntu 16.04.1 LTS**  
  
location:  
uuid/detail/random_provider_getrandom.ipp:70  
  
  
throws an exception blow:  
boost::exception_detail::clone_impl<boost::exception_detail::error_info_injectorboost::uuids::entropy_error >  
  
system kernel info:  
Linux app2 3.13.0-32-generic #57-Ubuntu SMP Tue Jul 15 03:51:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux

---

## Comment 1

> Username: Lastique  
> Created at: 2018-10-10 09:41:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/79#issuecomment-428507292  

The `getrandom` syscall only appeared in Linux kernel 3.17, so in your kernel it is not present, which causes the error. The fact that `SYS_getrandom` is defined likely means that your installed Linux headers are from a different, newer kernel. Please, check that your linux-libc-dev package version on the build system matches the kernel you're running. If it doesn't then I'd say it's a configuration problem.  
  
Also, you can define `BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX` to disable `getrandom` backend and use /dev/urandom.

---

## Comment 2

> Username: bajizhh  
> Created at: 2018-10-10 11:16:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/79#issuecomment-428534103  

@Lastique thank you, I fixed it according to your suggestion.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-10-10 12:43:16 UTC  
> Url: https://github.com/boostorg/uuid/issues/79#issuecomment-428556772  

So before we close this, I'd like to know if you used the `BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX` workaround.  If we need to add a fix here I'd like to know.
