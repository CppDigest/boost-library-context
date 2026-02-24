# #184 - detail::copy_file_data_copy_file_range fails in some file systems [Closed]

> Username: Hagb  
> Created at: 2021-04-21 18:12:27 UTC  
> Updated at: 2021-05-20 07:16:16 UTC  
> Closed at: 2021-05-18 21:48:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184  

If the output file of `copy_file` is in [eCryptfs](https://www.ecryptfs.org/) and the input file is not empty, an `filesystem_error` `Invalid argument` will be throwed, leaving an empty output file.  
  
It seems caused by `copy_file_range` syscall in `detail::copy_file_data_copy_file_range`, which fails in some file systems such as eCryptfs.  
  
See also https://github.com/golang/go/issues/44273, https://github.com/prusa3d/PrusaSlicer/issues/4716#issuecomment-787460724  
  
<details>  
<summary>My Environment</summary>  
  
- Debian testing  
- g++ 10.2.1  
- boost_filesystem: https://github.com/boostorg/filesystem/commit/83429c9bfd9c305fa35dbf15bbcd127d6f280c5f (the latest commit in `develop` branch when this issue is submitted)  
  
```  
$ uname -a  
Linux hagb-thinkpad 5.10.0-6-amd64 #1 SMP Debian 5.10.28-1 (2021-04-09) x86_64 GNU/Linux  
  
$ cat /etc/os-release   
PRETTY_NAME="Debian GNU/Linux bullseye/sid"  
NAME="Debian GNU/Linux"  
ID=debian  
HOME_URL="https://www.debian.org/"  
SUPPORT_URL="https://www.debian.org/support"  
BUG_REPORT_URL="https://bugs.debian.org/"  
  
$ g++ --version  
g++ (Debian 10.2.1-6) 10.2.1 20210110  
Copyright (C) 2020 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
</details>  
  
  
<details>  
<summary>How to reproduce</summary>  
  
### Source code to reproduce  
  
```c++  
#include <boost/filesystem.hpp>  
namespace fs = boost::filesystem;  
  
int main(void){  
        fs::copy_file("src_file", "dist_file", fs::copy_options::overwrite_existing);  
        return 0;  
}  
```  
  
### Operation  
`ecryptfs-utils` should be installed.  
  
```bash  
# In tmpfs it is ok  
$ cd /tmp  
$ mkdir test_noecryptfs  
$ cd test_noecryptfs  
$ echo content > src_file  
$ /path/to/the/exec/compiled/from/the/above/c++/code # successful  
$ cat dist_file  
content  
  
# In eCryptfs  
$ cd /tmp  
$ mkdir test_ecryptfs  
$ sudo mount -t ecryptfs test_ecryptfs test_ecryptfs  
Select key type to use for newly created files:   
 1) tspi  
 2) passphrase  
Selection: 2  
Passphrase:   
...  
...  
Mounted eCryptfs  
$ cd test_ecryptfs  
$ echo content > src_file  
$ /path/to/the/exec/compiled/from/the/above/c++/code # throw an error  
terminate called after throwing an instance of 'boost::filesystem::filesystem_error'  
  what():  boost::filesystem::copy_file: Invalid argument: "src_file", "dist_file"  
Aborted  
$ cat dist_file # the file exists and is empty  
  
  
```  
</details>

---

## Comment 1

> Username: Lastique  
> Created at: 2021-04-21 18:50:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-824282131  

I think this should be handled either in the kernel or eCryptfs. As far as I can tell, `copy_file_range` API is used correctly in Boost.Filesystem.

---

## Comment 2

> Username: Hagb  
> Created at: 2021-04-21 19:07:38 UTC  
> Updated at: 2021-04-21 19:24:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-824291528  

Hi, thanks for reply.  
  
There is a mailing list discussion about it: https://lore.kernel.org/linux-fsdevel/20210126233840.GG4626@dread.disaster.area/T/#m05753578c7f7882f6e9ffe01f981bc223edef2b0, in which it was said that   
> Libraries using copy_file_range() must be prepared for it to fail  
and fall back to normal copy mechanisms. Of course, with these  
special zero length files that contain ephemeral data, userspace can't  
actually tell that they contain data from userspace using stat(). So  
as far as userspace is concerned, copy_file_range() correctly  
returned zero bytes copied from a zero byte long file and there's  
nothing more to do. (Dave Chinner)  
  
and  
> Go should probably not use copy_file_range in a common library  
function, as I don't see any easy way to detect this scenario  
currently (detect 0 size? sure, but that won't work with the example  
you provide above). And the man page should document this behaviour  
more explicitly to prevent further incorrect usage. (Nicolas Boichat)  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2021-04-21 21:51:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-824381819  

That discussion was revolving around a different use case, where sysfs/procfs files appear to have zero size. I'm not convinced it is reasonable to expect `copy_file` to create non-zero sized files, but it should not fail with an error. And I believe, it doesn't.  
  
In your case, the syscall fails with an error code, and the error code is not enlightening to boot. So I believe, this is a different use case that should still probably be addressed in the kernel.  
  
What confuses me is that Al Viro makes a comment that using `copy_file_range` in `cp(1)` is wrong, which makes me wonder what's the use of this syscall then. Given that `copy_file` is intended to be that `cp(1)` in C++, his comment also applies to `copy_file`.  
  
Can you test if the `sendfile` implementation works with eCryptfs? Also, does `copy_file_range` only fail if it is done cross-filesystem or does it also fail within the same filesystem?

---

## Comment 4

> Username: Hagb  
> Created at: 2021-04-22 01:22:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-824467839  

In my test, when the destination file is in eCryptfs, both `sendfile` and `copy_file_range` will fail, whether the source file and the destination file are cross-filesystem or not.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-04-23 10:36:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-825567862  

Does `sendfile` also return `EINVAL`? Do `sendfile` and `copy_file_range` also fail when both files are on the same eCryptfs filesystem?  
  
I'm trying to work out a solution where I could still benefit from `sendfile` and `copy_file_range`, with a fallback to a `read`/`write` loop for a well defined subset of cases.

---

## Comment 6

> Username: Hagb  
> Created at: 2021-04-24 03:02:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-826024168  

> Does `sendfile` also return `EINVAL`?  
  
Yes, it does.  
  
> Do `sendfile` and `copy_file_range` also fail when both files are on the same eCryptfs filesystem?  
  
Yes, they do.

---

## Comment 7

> Username: Lastique  
> Created at: 2021-05-18 21:49:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-843587785  

Thanks for the report. Could you test if the fixed version works in your setup?

---

## Comment 8

> Username: Hagb  
> Created at: 2021-05-20 07:16:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/184#issuecomment-844783399  

Thanks a lot! It works well now.
