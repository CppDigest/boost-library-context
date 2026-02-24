# #259 - Quickbook test suite path failures under Windows [Closed]

> Username: pdimov  
> Created at: 2022-09-04 15:11:32 UTC  
> Updated at: 2022-09-05 10:05:55 UTC  
> Closed at: 2022-09-05 10:05:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/259  

I'm looking into the Windows failures in https://github.com/boostorg/quickbook/actions/runs/2967254151, specifically  
```  
====== BEGIN OUTPUT ======  
test\unit\path_test.cpp(37): test 'std::string("file://?/a:/x") == file_path_to_url(path("\\\\?\\a:\\x"))' ('file://?/a:/x' == 'file://?/a%3a/x') failed in function 'void __cdecl file_path_to_url_tests(void)'  
test\unit\path_test.cpp(45): test 'std::string("file://localhost/c:/foo/bar") == file_path_to_url(path("\\\\localhost\\c:\\foo\\bar"))' ('file://localhost/c:/foo/bar' == 'file://localhost/c:foo/bar') failed in function 'void __cdecl file_path_to_url_tests(void)'  
test\unit\path_test.cpp(78): test 'std::string("file://?/a:/x/") == dir_path_to_url(path("\\\\?\\a:\\x"))' ('file://?/a:/x/' == 'file://?/a%3a/x/') failed in function 'void __cdecl dir_path_to_url_tests(void)'  
test\unit\path_test.cpp(86): test 'std::string("file://localhost/c:/foo/bar/") == dir_path_to_url(path("\\\\localhost\\c:\\foo\\bar"))' ('file://localhost/c:/foo/bar/' == 'file://localhost/c:foo/bar/') failed in function 'void __cdecl dir_path_to_url_tests(void)'  
"..\quickbook"  
4 errors detected.  
  
EXIT STATUS: 4  
====== END OUTPUT ======  
```  
Looking at the logic in https://github.com/boostorg/quickbook/blob/071a58488d265c8a3f4d539196ee16c8db7e2769/src/path.cpp#L124, it looks like the decomposition of Windows network paths has changed. Seems like Quickbook expects `\\?\a:\x` to iterate over `\\?`, `a:`, `x`, but it now iterates over `\\?\a:` and `x`; and similarly, it appears that it expects `\\localhost\c:\x` to iterate over `\\localhost`, `c:`, `\`, `x`, but it now iterates over `\\localhost`, `c:`, `x`.  
  
Is my understanding correct that there has been a behavior change here?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-09-04 22:42:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/259#issuecomment-1236428464  

The `\\?` and other similar prefixes are indeed now considered part of the root name. That is `path("\\\\?\\C:\\foo").root_name() == "\\\\?\\C:"`. This change was made in 1.77. Previously these prefixes were not really supported, so `\\?` could indeed be considered a root name.  
  
Iteration was and is (or should, anyway) happen over these path components: root name, root directory, the rest path elements. There is a difference between v3 and v4 on how the trailing directory separator is interpreted - v3 would produce "." as the last element while v4 - "". So iterating over `\\?\C:\foo` should produce `\\?\C:`, `\` and `foo`.  
  
UNC paths are also supported the same way. Iterating over `\\localhost\c:\x` should produce `\\localhost`, `\`, `c:` and `x`. As far as I'm aware, this interpretation should not have changed recently.  
  
There are [iteration tests](https://github.com/boostorg/filesystem/blob/9613ccfa4a2c47bbc7059bf61dd52aec11e53893/test/path_test.cpp#L273) that check various cases, including Windows-specific prefixes and UNC paths. You can see the expected behavior there.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-09-05 00:11:12 UTC  
> Updated at: 2022-09-05 00:12:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/259#issuecomment-1236443528  

> Iterating over `\\localhost\c:\x` should produce `\\localhost`, `\`, `c:` and `x`.  
  
That's indeed what it does. But looking at the Quickbook code, it seems to assume that it would produce `\\localhost`, `c:`, `\`, and `x`.  
  
Since the `\` usually comes after `c:`, perhaps this path should actually return `\\localhost\c:` as the root name, by analogy with the `\\?` case. Otherwise the elements of `\\localhost\c:\foo\bar` and `\\localhost\c:foo\bar` would be identical if I'm not mistaken. (Actually no, the latter gives `\\localhost`, `/`, `c:foo`, `bar`. This doesn't seem correct either.)

---

## Comment 3

> Username: Lastique  
> Created at: 2022-09-05 01:01:27 UTC  
> Updated at: 2022-09-05 01:07:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/259#issuecomment-1236455633  

It is possible that it used to interpret `\\localhost\c:` as the root name, it's kind of difficult to tell now as there were multiple changes to `path` implementation. Anyway, if that was the case then the old behavior was incorrect since clearly the root name should be `\\localhost`. I'm pretty sure QuickBook was simply written to work with whatever Boost.Filesystem behavior was there at the time, even if it was incorrect or by accident (for the cases that were not really supported or tested).  
  
> Since the `\` usually comes after `c:`, perhaps this path should actually return `\\localhost\c:` as the root name  
  
No, root name is, well, the name of the root in the filesystem hierarchy, and that is clearly `\\localhost` as there can be multiple shares under `\\localhost`, `c:` being one of them. The colon has no special meaning beyond the device name on the local filesystem, and it is not the definitive delimiter for the end of the root name.  
  
> Actually no, the latter gives `\\localhost`, `/`, `c:foo`, `bar`. This doesn't seem correct either.  
  
It is correct. The `c:foo` path on the local filesystem is a relative path that resolves to `foo` in the current directory on drive `c:`. It is notably different from `\\localhost\c:foo` because this path is absolute (as it has both root name and root directory) and therefore `c:foo` is a single filename (or, in Windows terms, a share name). So it is conceptually similar to `\\localhost\share`. There is no way to represent a relative UNC path, and in particular there is no way to refer to the current directory on whatever drive in a UNC path.  
  
PS: Yes, I was not quite correct earlier about the root directory being `\` - it should be `/`, the generic directory separator.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-09-05 10:05:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/259#issuecomment-1236799552  

OK, thanks.
