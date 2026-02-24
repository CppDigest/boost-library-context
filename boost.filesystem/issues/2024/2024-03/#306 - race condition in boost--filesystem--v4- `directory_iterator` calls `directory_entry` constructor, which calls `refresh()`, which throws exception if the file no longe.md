# #306 - race condition in boost::filesystem::v4: `directory_iterator` calls `directory_entry` constructor, which calls `refresh()`, which throws exception if the file no longer exists [Closed]

> Username: gh-user-2022  
> Created at: 2024-03-18 08:39:06 UTC  
> Updated at: 2024-03-18 14:39:22 UTC  
> Closed at: 2024-03-18 10:39:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/306  

According to documentation,   
  
> explicit directory_entry(const path& p);  
> directory_entry(const path& p, system::error_code& ec); // v4-only  
> Effects:  
>   
> v3: Initializes m_path from p and default-constructs m_status and m_symlink_status.  
>   
> [Note: The cached file statuses will be updated when queried by the caller or by an explicit call to refresh. —end note]  
>   
> v4: Initializes m_path from p and calls refresh() or refresh(ec), respectively.  
>   
> Postcondition: path() == p if no error occurs, otherwise path().empty() == true.  
  
There is a race condition when following happens:  
1. The `boost::filesystem::directory_iterator` gets a next file in directory.  
2. The file gets deleted or renamed.  
3. The `boost::filesystem::directory_iterator` passes the file name to construct `boost::filesystem::directory_entry`, which calls `boost::filesystem::path::refresh()` on non-existent path, which throws exception.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-03-18 10:39:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/306#issuecomment-2003548110  

Boost.Filesystem (and std::filesystem) is generally not protected against filesystem races. The behavior is undefined if the filesystem is modified concurrently with Boost.Filesystem operations. This is by design.  
  
That said, Boost.Filesystem takes some measures to protect itself from filesystem races, when possible. The amount and robustness of such protection is not guaranteed and should not be relied upon (i.e. it is a matter of QoI). In particular, `directory_iterator` does not construct `directory_entry` in the normal way, and [initializes its cached status and symlink status](https://github.com/boostorg/filesystem/blob/6f174ba14369143d28ae72f4c697660c65cecae6/src/directory.cpp#L1224), when this information is made available by the OS during iteration. In this case, the race you describe should not happen because `refresh()` won't be implicitly called.

---

## Comment 2

> Username: gh-user-2022  
> Created at: 2024-03-18 13:12:04 UTC  
> Updated at: 2024-03-18 13:12:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/306#issuecomment-2003871553  

I would ask you to reconsider, due to following reason: there is no easy way for calling code to add workaround in this case, because the race condition happens inside the Boost.Filesystem itself. So it is worth to add a workaround inside the library.  
  
In Boost.Filesystem **v3** the exception would happen not inside the increment of the iterator, but when calling code would attempt to do anything with the `directory_entry`. And in such case the workaround can be implemented in calling code.  
  
> In this case, the race you describe should not happen because refresh() won't be implicitly called.  
  
Well, the documentation explicitly says that for Boost.Filesystem **v4** the `refresh()` is called.  
So is there a contradiction between the documentation and the implementation?  
  
Also, debugging shows that `refresh()` is indeed called during increment of the iterator.  
(Let me know if you need a sample code to reproduce the issue.)  
  
Another consideration is: calling `refresh()` always for each `directory_entry` is a performance penalty (additional syscall) in both Linux and Windows.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-03-18 14:31:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/306#issuecomment-2004078031  

> I would ask you to reconsider, due to following reason: there is no easy way for calling code to add workaround in this case, because the race condition happens inside the Boost.Filesystem itself. So it is worth to add a workaround inside the library.  
  
Preserving status obtained during iteration *is* the workaround. If this information is not provided by the OS, there is no other way the library could obtain status information other than to query the filesystem, which may result in a filesystem race.  
  
And I'll reiterate, whether the OS provides file status on iteration or not, user's code must be prepared that incrementing or dereferencing a directory iterator can fail (with an exception or an error code) for a multitude of reasons, filesystem race being just one of them.  
  
> So is there a contradiction between the documentation and the implementation?  
  
No, the effects described in the documentation are maintained, provided that there is no filesystem race. As I said, with a filesystem race, the behavior is undefined.  
  
> Also, debugging shows that `refresh()` is indeed called during increment of the iterator.  
> (Let me know if you need a sample code to reproduce the issue.)  
  
Does this reproduce on the current `develop`? On what OS and filesystem? If this happens on `develop`, I would like a reproducer, please.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-03-18 14:39:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/306#issuecomment-2004099662  

> Well, the documentation explicitly says that for Boost.Filesystem **v4** the `refresh()` is called.  
  
Also, I'll reiterate that the documentation doesn't say this. The `directory_entry` constructor from path calls `refresh()`, yes, but `directory_iterator::operator++()` doesn't call that constructor.
