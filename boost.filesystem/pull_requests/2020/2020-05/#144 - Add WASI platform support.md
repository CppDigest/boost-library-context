# #144 Add WASI platform support [Merged]

> Username: whitequark  
> Created at: 2020-05-23 11:00:37 UTC  
> Updated at: 2020-05-23 14:19:04 UTC  
> Merged at: 2020-05-23 14:17:32 UTC  
> Closed at: 2020-05-23 14:17:32 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144  

[WASI](https://wasi.dev/) is a POSIX-like platform that has a few important differences:  
  * There is no concept of a mutable current directory. However, WASI host environment can provide a directory called `.` in the filesystem namespace that fulfills a similar role.  
  * There is no concept of a file mode, and no `chmod` call.  
  * There is no concept of a filesystem, and no `statfs` call.  
  * There is no `readdir_r` call because there are no threads.

---

## Review 1 [Commented]

> Username: marcusmueller  
> Created_at: 2020-05-23 11:06:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/144#pullrequestreview-417270410  

📁 src/operations.cpp

```diff
 101 | #   include <sys/stat.h>
 102 |- #   if !defined(__APPLE__) && !defined(__OpenBSD__) && !defined(__ANDROID__) && !defined(__VXWORKS__)
 102 |+ #   if defined(__wasm)
```

> Username: marcusmueller  
> Created_at: 2020-05-23 11:06:29 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429536467  

This would be nicer than the existing code base, but I'd honestly think that future readers would enjoy a comment here, especially if WASI should ever be extended to cover things like `stat`.

> Username: whitequark  
> Created_at: 2020-05-23 11:12:08 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429536771  

You're right, of course--I lost the comment while porting the diff to `develop` from 1.72.0. Fixed now.

> Username: whitequark  
> Created_at: 2020-05-23 11:12:51 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429536832  

Note that this is specifically `statfs` and `statvfs`, not `stat` (it does have `stat`). `statfs` gets filesystem statistics, not file statistics.


---

## Review 2 [Commented]

> Username: nabijaczleweli  
> Created_at: 2020-05-23 11:34:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/144#pullrequestreview-417271930  

📁 src/operations.cpp

```diff
2200 |+   {}
2201 |+ 
2202 | # ifdef BOOST_POSIX_API
```

> Username: nabijaczleweli  
> Created_at: 2020-05-23 11:34:29 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429538188  

`#elif defined(BOOST_POSIX_API)` here as well?

> Username: whitequark  
> Created_at: 2020-05-23 11:54:35 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429539377  

Thanks, good catch


---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2020-05-23 11:38:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/144#pullrequestreview-417272144  

📁 src/operations.cpp

```diff
1567 |- # ifdef BOOST_POSIX_API
1569 |+ # if defined(__wasm)
1570 |+   return ".";
```

> Username: Lastique  
> Created_at: 2020-05-23 11:38:13 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429538436  

This is not right, `current_path` must return an absolute path (see C++20 [fs.op.current.path]). For example, `copy` implementation relies on that, and probably not only that.  
  
If the platform does not support `current_path`, it should fail with `BOOST_ERROR_NOT_SUPPORTED` (`ENOSYS`), but then I'm not sure how useful the rest of the library would be as it is used in implementation of quite a few operations.

> Username: Lastique  
> Created_at: 2020-05-23 11:51:58 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429539231  

As an alternative, you could assume that the current path is always the root directory. I'm not sure how valid is that in WASI (i.e. is `equivalent(".", "/") == true`).

> Username: Lastique  
> Created_at: 2020-05-23 11:55:40 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429539471  

Also, see if there is a way to obtain path from a file descriptor or from the result of `stat` on WASI.

> Username: whitequark  
> Created_at: 2020-05-23 11:58:45 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429539685  

Understood. I'll check whether this assumption holds (the precise WASI semantics around `.` are not entirely clear to me) and see what can be done.

> Username: whitequark  
> Created_at: 2020-05-23 12:00:27 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429539795  

> Also, see if there is a way to obtain path from a file descriptor or from the result of `stat` on WASI.  
  
That's what the [`fd_prestat_dir_name` syscall](https://github.com/WebAssembly/WASI/blob/master/phases/snapshot/docs.md#-fd_prestat_dir_namefd-fd-path-pointeru8-path_len-size---errno) is for.

> Username: Lastique  
> Created_at: 2020-05-23 13:00:33 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429544229  

Can you use it to obtain the full path to '.'?

> Username: whitequark  
> Created_at: 2020-05-23 13:14:39 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429545290  

> If the platform does not support `current_path`, it should fail with `BOOST_ERROR_NOT_SUPPORTED` (`ENOSYS`), but then I'm not sure how useful the rest of the library would be as it is used in implementation of quite a few operations.  
  
I've changed the code to do that. It turns out that the application I'm porting is only using `boost::filesystem::path`, so it's still useful for that. Failing with `BOOST_ERROR_NOT_SUPPORTED` seems to be a safe choice that can be always revisited later, perhaps with input from WASI folks.

> Username: whitequark  
> Created_at: 2020-05-23 13:16:40 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429545460  

> Can you use it to obtain the full path to '.'?  
  
No. There is no concept of an "absolute path" in WASI, since it eliminates ambient authority and uses capabilities for everything; the `/<something>` directory, `.` directory, and `..` directory behave effectively like separate mounts, akin to the way `.` and `..` work on FAT, if you've seen that. There might not be any other name by which the application can access `.`.  
  
WASI is really designed to use exclusively `openat`, and it only provides `open` as a shim over `openat` to aid in porting applications. That's why the behavior is so strange.


---

## Review 4 [Commented]

> Username: Lastique  
> Created_at: 2020-05-23 14:09:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/144#pullrequestreview-417281659  

📁 src/operations.cpp

```diff
1570 |+   error(BOOST_ERROR_NOT_SUPPORTED, ec,
1571 |+     "boost::filesystem::current_path");
1572 |+   return "";
```

> Username: Lastique  
> Created_at: 2020-05-23 14:09:30 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429549321  

`return path()` please.

> Username: whitequark  
> Created_at: 2020-05-23 14:10:47 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429549413  

Done


---

## Review 5 [Changes requested]

> Username: Lastique  
> Created_at: 2020-05-23 14:12:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/filesystem/pull/144#pullrequestreview-417281785  

📁 src/operations.cpp

```diff
2198 |+ # if defined(__wasm)
2199 |+ 
2200 |+   if (!error(BOOST_ERROR_NOT_SUPPORTED,
```

> Username: Lastique  
> Created_at: 2020-05-23 14:11:46 UTC  
> Updated_at: 2020-05-23 14:13:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429549496  

Use `emit_error` here, with no condition.

> Username: whitequark  
> Created_at: 2020-05-23 14:13:54 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#discussion_r429549660  

Done


---

## Comment 6

> Username: Lastique  
> Created_at: 2020-05-23 14:17:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#issuecomment-633062531  

Thanks.

---

## Comment 7

> Username: whitequark  
> Created_at: 2020-05-23 14:19:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/144#issuecomment-633062802  

Thanks for the quick review!

---
