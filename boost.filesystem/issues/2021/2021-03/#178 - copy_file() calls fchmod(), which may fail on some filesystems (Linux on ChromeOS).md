# #178 - copy_file() calls fchmod(), which may fail on some filesystems (Linux on ChromeOS) [Closed]

> Username: bubnikv  
> Created at: 2021-03-15 09:56:42 UTC  
> Updated at: 2021-03-15 18:19:37 UTC  
> Closed at: 2021-03-15 18:19:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/178  

boost::filesystem::copy_file() when used to copy a file on ChromeBook in a virtualized Linux (this is now a Beta feature delivered by Google) to a removable media mounted through 9p network file system fails. The issue is that Google does not allow calling fchmod() on that mounted file system. Thus the file gets copied, but copy_file() reports a non-descriptive "operation failed" error.  
  
I am not quite sure (we cannot reproduce ourselves), but the following ticket on our software may also be triggered by the same fchmod() operation. Here a file gets copied to WebDAV network file system.  
https://github.com/prusa3d/PrusaSlicer/issues/2437

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-15 15:47:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/178#issuecomment-799526769  

Does ChromeOS support file mode and permissions, at all? If not, then all the related parts of Boost.Filesystem need to be disabled. If this is just a limitation of a particular filesystem then this is a different story.  
  
Copying the input file mode is part of the contract of `copy_file`, so if the target system does not support it then it does not support `copy_file`. That the operation fails in this case is expected behavior. If `fchmod` is not supported at all, on any filesystem, the error code of `ENOSYS` would be more preferable. In that case, if you can tell me how to detect ChromeOS on preprocessor level, I can add an early check to return `ENOSYS` on that platform. Check the predefined macros defined by the compiler or system headers. If `fchmod` only fails in some cases then the current behavior is correct.  
  
Additionally, I could add a flag in `copy_options` to skip copying file mode. In this case, the copied file mode and permissions would be unspecified. (As an implementation detail, `copy_file` may have to enable writing permission for the newly created file for the duration of the data copying; that permission has to be revoked with `fchmod` if it was not present in the original file.)

---

## Comment 2

> Username: bubnikv  
> Created at: 2021-03-15 17:49:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/178#issuecomment-799620070  

> Does ChromeOS support file mode and permissions, at all? If not, then all the related parts of Boost.Filesystem need to be disabled. If this is just a limitation of a particular filesystem then this is a different story.  
  
It is an issue of a particular way of how the host file system (ChromeOS) directories are mounted into the virtual machine. Namely, when I plug an USB flash drive into Chrome OS and I allow that flash drive to be shared with the virtualized Linux, ChromeOS passes the flash drive through a 9p file system to the virtualized Linux, where it pops up at /mnt/chromeos/removable/the_removable_media. The whole /mnt/chromeos is mounted using the 9p filesystem.  
  
I am not sure about the WebDAV system I mentioned, we were not able to reproduce customer's issue.  
  
> if you can tell me how to detect ChromeOS on preprocessor level  
  
The best thing I was able to do is to read /proc/version and guesstimate the ChromeOS from the build information of that particular Linux kernel.  
  
> Additionally, I could add a flag in copy_options to skip copying file mode. In this case, the copied file mode and permissions would be unspecified. (As an implementation detail, copy_file may have to enable writing permission for the newly created file for the duration of the data copying; that permission has to be revoked with fchmod if it was not present in the original file.)  
  
I solved our problem by copying the detail::copy_file() into our code base and making the failure of fchmod to just log errors (generic Linux) or warnings (Chrome OS detected).   
  
> I could add a flag in copy_options to skip copying file mode.  
  
Such an option certainly makes sense. For us though it is more suitable to have an option to ignore the fchmod error.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-03-15 18:19:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/178#issuecomment-799643047  

If `fchmod` failure is dependent on the filesystem then the current behavior is correct.  
  
I will think about a `copy_options` flag.
