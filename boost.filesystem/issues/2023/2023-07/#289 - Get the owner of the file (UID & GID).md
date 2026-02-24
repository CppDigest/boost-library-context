# #289 - Get the owner of the file (UID & GID) [Closed]

> Username: SilverPlate3  
> Created at: 2023-07-09 05:41:53 UTC  
> Updated at: 2024-03-18 15:48:29 UTC  
> Closed at: 2024-03-18 15:48:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/289  

@Lastique   
An important functionality that can be added to the library is getting/setting the owner of a file/dir.  
In posix its uid & gid.  
I think it be a member of `boost::filesystem::file_status` just like `permissions()`  
If you want it to be for windows as well, we can get the sid.  
  
Would you like me to create a pull request for such functionality?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-07-09 08:22:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/289#issuecomment-1627645214  

I think the usefulness of this information would be rather limited, because operating on `uid_t`/`gid_t` is very different than on `SID`. This means that in order this API to be useful, it would have to expose the native id to the user, and the user would have to call system-specific APIs anyway. Also, I don't want `windows.h` to be included in public headers, so that's a problem.  
  
Do you have a use case where having this API in Boost.Filesystem would be useful compared to calling native APIs directly? That is, do you have a case where adding this API would allow the user's code to become portable without conditional compilation?  
  
As for the implementation, I don't like adding it to `file_status`. I think, bundling file type with permissions was a mistake to begin with, as there are places in the library where only file type is available and sufficient, and permissions get in the way. Also note that `status` is one of the most frequently called operations in the library, I don't want to make it more expensive. If this is to be added, it would have to be a separate API.

---

## Comment 2

> Username: SilverPlate3  
> Created at: 2023-07-10 04:26:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/289#issuecomment-1628131635  

Indeed we can always just use the native API.   
I had a need to get the uid:gid, check it, and then change it.  
This required the usage of a native struct + 2 native API calls.   
Just though it might be easier if there was a library that offered such functionality, in 2 lines of code. (2 lines of code from the user perspective)   
  
But I get your point.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-03-18 15:48:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/289#issuecomment-2004284769  

I'm closing this for now as out of scope. User/group information is very different between POSIX and Windows, and I don't see a use case for providing some portable wrapper.
