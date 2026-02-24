# #26 filesystem: getting file_type from a directory_entry without causing a system call on linux [Closed]

> Username: taruti  
> Created at: 2016-01-29 11:14:06 UTC  
> Updated at: 2019-04-17 19:45:43 UTC  
> Closed at: 2019-04-16 09:00:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/26  

Trac https://svn.boost.org/trac/boost/ticket/11947  
  
When iterating over a directory with directory_iterator the directory_entries are created with the file_type component of the m_symlink_status defined if the filesystem supports it on Linux due to BOOST_FILESYSTEM_STATUS_CACHE.  
  
However the directory iteration does not produce the permission component of the file status. Thus if using somedirectoryentry.symlink_status().type() the library performs a superfluous lstat system call because status_known does not succeed due to permissions_present not succeeding.  
  
This is possible to fix e.g. by adding a symlink_type method to the directory_entry class, a patch implementing it is linked below:

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-16 09:00:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/26#issuecomment-483574374  

You can already obtain this information by calling `symlink_status` (member of `directory_entry` or non-member) and then `type` on the returned `file_status`. I don't think a dedicated shortcut function is warranted here.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-04-16 09:10:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/26#issuecomment-483577559  

On the second thought, you are correct, I overlooked the permissions part. I might add the shortcut function then, but it should probably be universal (i.e. not specific to `directory_entry`). Also, the PR had conflicts, so I'll leave it closed.

---

## Comment 3

> Username: taruti  
> Created_at: 2019-04-17 19:45:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/26#issuecomment-484234238  

The PR was from 3 years ago, thus the conflicts. Ended up just using the C API for this few years ago.

---
