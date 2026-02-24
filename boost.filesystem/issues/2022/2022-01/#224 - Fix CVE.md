# #224 - Fix CVE [Closed]

> Username: vinniefalco  
> Created at: 2022-01-20 18:01:06 UTC  
> Updated at: 2022-07-24 08:36:03 UTC  
> Closed at: 2022-07-24 08:36:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224  

https://blog.rust-lang.org/2022/01/20/cve-2022-21658.html  
  
Related:  
https://www.reddit.com/r/rust/comments/s8h1kr/security_advisory_for_the_standard_library/hth7go5/

---

## Comment 1

> Username: Lastique  
> Created at: 2022-02-14 15:10:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224#issuecomment-1039200124  

Thanks for the report.

---

## Comment 2

> Username: jwakely  
> Created at: 2022-07-13 22:18:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224#issuecomment-1183738097  

I don't think this is properly fixed. If all you did is use `O_NOFOLLOW` when opening the top of the directory tree to remove, that isn't enough. Consider `remove_all("foo")` which is expected to remove "foo/bar/baz" and "foo/bar" below it. You have made it so that "foo" is checked for a symlink when first starting to iterate over that directory. But it could be replaced with a symlink later, after you've already done the `open` call with `O_NOFOLLOW`. It looks like `remove_impl` will do `unlink("foo/bar/baz")` and `rmdir("foo/bar")` but if `"foo"` has been replaced with a symlink between calling `open` and those calls to `unlink` and `rmdir` then you still remove the wrong files.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-07-14 08:32:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224#issuecomment-1184158364  

Yes, looks like you're right.

---

## Comment 4

> Username: jwakely  
> Created at: 2022-07-14 09:18:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224#issuecomment-1184204451  

The way to fix this for POSIX is to use `openat` (with `O_NOFOLLOW`) when recursing into each sub-directory and `unlinkat` when removing files/directories.  
  
Using `open("foo", opts|O_NOFOLLOW)` to initially open the directory for "foo" ensures it's not a symlink. Then you use `fdopendir` to get a `DIR` directory stream for "foo" (which is safe from symlinks because you already have the fd to "foo"). Now even if "foo" gets replaced by a symlink later, you don't care because all operations can be done relative to the fd, without ever resolving the path "foo" again. To recurse into "foo/bar" you use `openat` with the fd for "foo", without resolving the path "foo/bar". Then to remove "foo/bar/baz" you use `unlinkat` with the fd for "bar", and to remove "foo/bar" itself you use `unlinkat` with the fd for "foo".  
  
(It's not necessary for the directory iterator to store the fd directly, because you can always obtain the fd from the `DIR*` using `dirfd`.)  
  
Basically, to avoid a TOCTTOU race each directory path must be resolved only once, when calling `open` or `openat`. All subsequent operations working below that directory should be relative to an open file descriptor, not resolving any path containing "foo/..." again.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-07-14 09:42:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224#issuecomment-1184229008  

Thanks, I was thinking of something like that. There are portability worries, of course, as I'm not sure how portable `*at` APIs and `dirfd` are. So far I have been avoiding these, as they are problematic on some platforms (e.g. see [here](https://github.com/boostorg/filesystem/blob/945c2ecf11dff68e4bac32053281bc9eb68bb846/src/operations.cpp#L3379-L3400)).

---

## Comment 6

> Username: jwakely  
> Created at: 2022-07-14 10:31:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/224#issuecomment-1184277033  

Yes, they're defined in POSIX.1-2008 and in Glibc long before that, but they are definitely not available universally.   
  
Libstdc++ uses them if present, but on older systems (and e.g. RTEMS)  `filesystem::remove_all` is still vulnerable to the symlink race. Those APIs exist because solving this problem is almost impossible otherwise, so there's only so much we can do for systems that don't provide the new APIs :shrug:
