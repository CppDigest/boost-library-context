# #166 - unique_path errors out with "function not implemented" on RHEL 7.2 [Closed]

> Username: mistafunk  
> Created at: 2020-11-19 13:36:55 UTC  
> Updated at: 2021-05-12 17:07:20 UTC  
> Closed at: 2020-11-19 14:17:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/166  

While updating our product from boost 1.71 to 1.74 I saw `unique_path` error out with "function not implemented". Our build and unit test machines are running RHEL 7.2 (glibc 2.17, kernel 3.10).  
  
I believe this is happening because of this new linux specific checks in unique_path.cpp:  
````  
#      include <sys/syscall.h>  
#      if defined(SYS_getrandom)  
#          define BOOST_FILESYSTEM_HAS_SYS_GETRANDOM  
#      endif // defined(SYS_getrandom)  
````  
  
My theory: this detects the define `SYS_getrandom` but does not check if the kernel is new enough (AFAIK, `SYS_getrandom` is only available from RHEL 7.4, kernel equivalent 3.14 backported or similar).

---

## Comment 1

> Username: Lastique  
> Created at: 2020-11-19 14:17:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/166#issuecomment-730404763  

Duplicates https://github.com/boostorg/filesystem/issues/164.

---

## Comment 2

> Username: acmorrow  
> Created at: 2021-05-12 15:12:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/166#issuecomment-839853383  

@Lastique -   
  
I'm not sure this is actually a duplicate of #164. In that case, if I understand correctly, they were building on a newer kernel/glibc combination, and then trying to run on older. It wouldn't surprise me that that would cause problems.  
  
However, I just encountered this issue building and running on the *same* host, which is RHEL 7.0.  
  
It appears that on this system `SYS_getrandom` is defined:  
  
```  
$ cat > testme.cpp  
#include <sys/syscall.h>  
#if defined(SYS_getrandom)  
#error IT IS DEFINED  
#endif  
  
$ gcc ./testme.cpp  
./testme.cpp:3:2: error: #error IT IS DEFINED  
 #error IT IS DEFINED  
  ^  
```  
  
However, the syscall doesn't work:  
  
```  
$ cat ./testme.cpp  
#include <sys/syscall.h>  
#include <unistd.h>  
#include <stdio.h>  
  
int main() {  
    char buf[1024];  
    int n;  
    n = syscall(SYS_getrandom, &buf, sizeof(buf), 0u);  
    if (n < 0) {  
        printf("SYSCALL FAILED\n");  
    }  
    return 0;  
}  
  
$ gcc ./testme.cpp && ./a.out  
SYSCALL FAILED  
```  
  
All of this on RHEL 7.0, glibc 2.17, kernel 3.10:  
  
```  
$ cat /etc/redhat-release  
Red Hat Enterprise Linux Server release 7.0 (Maipo)  
  
$ uname -a  
Linux ... 3.10.0-327.22.2.el7.x86_64 #1 SMP Thu Jun 9 10:09:10 EDT 2016 x86_64 x86_64 x86_64 GNU/Linux  
  
$ ldd --version  
ldd (GNU libc) 2.17  
```  
  
We encountered this issue while upgrading the copy of boost vendored with MongoDB from 1.70.0 to 1.76.0. It appears that relying on `SYS_getrandom` being defined is not sufficient to know that the syscall is actually present on older RHEL 7 systems. We are intentionally building on RHEL 7.0 here in order to build on oldest so we can support all subsequent RHEL 7 versions.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-05-12 16:44:35 UTC  
> Updated at: 2021-05-12 16:45:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/166#issuecomment-839932338  

If you have `SYS_getrandom` defined but the syscall not implemented in the kernel then your headers don't match the kernel. This is the same problem as in https://github.com/boostorg/filesystem/issues/164.  
  
If your distro ships newer kernel headers without the matching kernel then please report a bug to your distro maintainers. Otherwise you need to install matching headers and kernel image.

---

## Comment 4

> Username: acmorrow  
> Created at: 2021-05-12 17:07:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/166#issuecomment-839946891  

We observed this on stock RHEL 7.0 in CI. We have not modified or updated glibc, the kernel headers, or the kernel version from what RedHat provided.  
  
I agree that RedHat may have made a mistake here if the headers indicate the presence of a syscall that the kernel doesn't offer, but I'd imagine there is no way to get it fixed upstream, since they aren't going to issue patches for RHEL 7.0. As I mentioned, we explicitly build on RHEL 7.0 to ensure that the resulting binaries work on all later RHEL 7.x releases (assuming RHEL doesn't break ABI within the RHEL 7 series).  
  
RHEL 7 is a very common platform, and more than one user of this library has encountered difficulty with the new syscall based implementation here. Is making the direct syscall so much better than just falling back to `/dev/urandom` if the `__GLIBC_PREREQ(2, 25)` check fails that it is worth the apparent real world portability issues?  
  
In any event, it sounds like you are not particularly interested in fixing this, which is fine. We can easily comment out that section that looks for the syscall.
