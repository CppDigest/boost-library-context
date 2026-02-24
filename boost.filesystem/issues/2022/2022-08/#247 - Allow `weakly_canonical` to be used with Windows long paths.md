# #247 - Allow `weakly_canonical` to be used with Windows long paths [Closed]

> Username: emmenlau  
> Created at: 2022-08-09 15:06:57 UTC  
> Updated at: 2023-01-13 04:22:06 UTC  
> Closed at: 2022-08-10 10:31:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247  

Currently I'm unable to call `weakly_canonical()` with a long path on Windows. Long path in this context means the Microsoft Windows extension described at https://docs.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation. A path can be prefixed by `\\?\` and then can have more than 260 characters (with a number of restrictions).  
  
I could trace the problem of using long paths in `weakly_canonical()` to the method `create_file_handle()` in  
https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/src/windows_tools.hpp#L259  
  
This is called via: https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/src/operations.cpp#L4437  
  
But the call fails for long paths. Since in `weakly_canonical()`, the handle is only needed for the root item, I could work around this by adding some extra handling to `create_file_handle()`. My added logic is:  
 - If the given path has a long-path prefix but is less than 260 characters long, remove the long path prefix and rely on Windows to do the right thing.  
  
Is that reasonable, and could something like this be added to the library?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-09 15:48:46 UTC  
> Updated at: 2022-08-09 15:49:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209563908  

If you need to operate on long paths on Windows, it is up to the application to prepend the path with `\\?\` prefix before passing to Boost.Filesystem. Boost.Filesystem does not do that for you, and I don't think I want to make it do so. Mostly because that would introduce unnecessary inefficiency and complicate implementation quite substantially.  
  
If you're on a recent enough Windows, I would also recommend to enable long paths by default system-wide.

---

## Comment 2

> Username: emmenlau  
> Created at: 2022-08-09 16:15:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209592376  

Dear @Lastique , thanks for the quick reply!  
  
My problem is actually the opposite. I do have prepend the path with `\\?\` prefix before passing to Boost.Filesystem. This is causing the problem in https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/src/windows_tools.hpp#L259

---

## Comment 3

> Username: Lastique  
> Created at: 2022-08-09 16:22:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209599273  

I don't understand, what problem? Does the call fail with the `\\?\` prefix? With what error code? Can you prepare a small reproducer code?  
  
Note that the prefix is only valid for absolute paths, so make sure you're not adding it if the path is relative.

---

## Comment 4

> Username: emmenlau  
> Created at: 2022-08-09 17:43:19 UTC  
> Updated at: 2022-08-09 17:45:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209684962  

> I don't understand, what problem? Does the call fail with the `\\?\` prefix? With what error code? Can you prepare a small reproducer code?  
  
I assumed that my description was more clear, sorry if it was not!  
  
Here are more details: The call of `::CreateFileW()` fails, if the path has a prefix `\\?\`. If the path has the prefix, then `::CreateFileW()` does return a handle, but the handle is all zeros. I'm not knowledgeable about Windows methods, so I do not understand if a handle of zero is correct. I just assume it is wrong. I assume this because subsequently `file_status head_status = detail::status_impl(head, &local_ec);` showed an error status.  
  
As a test, I added code above `::CreateFileW()` that removes the prefix `\\?\`, and the error was gone. Therefore I conclude that `::CreateFileW()` does not support `\\?\`.

---

## Comment 5

> Username: emmenlau  
> Created at: 2022-08-09 17:47:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209688440  

So here is my workaround that fixes the problem for me:  
```  
diff --git a/libs/filesystem/src/windows_tools.hpp b/libs/filesystem/src/windows_tools.hpp  
index 469be041e..e49ecd6a4 100644  
--- a/libs/filesystem/src/windows_tools.hpp  
+++ b/libs/filesystem/src/windows_tools.hpp  
@@ -179,7 +179,11 @@ struct handle_wrapper  
 //! Creates a file handle  
 inline HANDLE create_file_handle(boost::filesystem::path const& p, DWORD dwDesiredAccess, DWORD dwShareMode, LPSECURITY_ATTRIBUTES lpSecurityAttributes, DWORD dwCreationDisposition, DWORD dwFlagsAndAttributes, HANDLE hTemplateFile = NULL)  
 {  
-    return ::CreateFileW(p.c_str(), dwDesiredAccess, dwShareMode, lpSecurityAttributes, dwCreationDisposition, dwFlagsAndAttributes, hTemplateFile);  
+    std::wstring p_str = p.wstring();  
+    if ((p_str.size() < 260) && (p_str.size() > 4) && (p_str[0] == L'\\') && (p_str[1] == L'\\') && (p_str[2] == L'?') && (p_str[3] == L'\\')) {  
+        p_str = p_str.substr(4);  
+    }  
+    return ::CreateFileW(p_str.c_str(), dwDesiredAccess, dwShareMode, lpSecurityAttributes, dwCreationDisposition, dwFlagsAndAttributes, hTemplateFile);  
 }  
   
 } // namespace detail  
```  
I'm not saying that this is beautiful. But it just shows that there would be a way to avoid the problem.

---

## Comment 6

> Username: Lastique  
> Created at: 2022-08-09 18:14:56 UTC  
> Updated at: 2022-08-09 18:16:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209714916  

[`CreateFileW`](https://docs.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-createfilew) returns `INVALID_HANDLE_VALUE` on error, which is `(HANDLE)-1` (i.e. not zero). A zero returned handle is not considered an error.  
  
Also, I asked about the error code and reproducer because this could give a clue as to what the problem is.

---

## Comment 7

> Username: emmenlau  
> Created at: 2022-08-09 20:02:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209818117  

> Also, I asked about the error code and reproducer because this could give a clue as to what the problem is.  
  
Yes, I understand that. I do not currently have access to a Windows machine, but I'll try to send more information when I have.

---

## Comment 8

> Username: Lastique  
> Created at: 2022-08-09 22:11:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1209943681  

`CreateFileW` is [listed](https://docs.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=registry) among the APIs that do support long paths. I have added a test to verify this.  
  
I suspect the problem is with the path you're using. The `\\?\` prefix is not simply allowing for longer paths, it actually disables a great deal of path processing in Windows API. This means that the path must be absolute, must use backslashes as separators, and must not contain dot (".") and dot-dot ("..") elements. If you're using an UNC path then the prefix must be `\\?\UNC\` instead of `\\`. Please verify that the path you're passing to `weakly_canonical` meets these requirements.

---

## Comment 9

> Username: emmenlau  
> Created at: 2022-08-10 07:02:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1210248774  

Thanks a lot for the follow-up!  
  
What I can say so from my debugger session yesterday is that `weakly_canonical` was calling `CreateFileW` with only the head of the path in `file_status head_status = detail::status_impl(head, &local_ec);`. From what I recall from the debugger, the head of the path was something like `\\?\C:` and nothing else, maybe with one more backslash.  
  
> Please verify that the path you're passing to `weakly_canonical` meets these requirements.  
  
I have a method that takes care of these requirements before passing the path to `weakly_canonical`. It's possible that the method is buggy, but it did work for thousands of paths with `canonical`, and just failed for `weakly_canonical` so far. But I don't want to guess, so I'll try to reproduce the issue and let you know what I find!

---

## Comment 10

> Username: Lastique  
> Created at: 2022-08-10 10:31:09 UTC  
> Updated at: 2022-08-10 10:35:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1210482023  

> From what I recall from the debugger, the head of the path was something like `\\?\C:` and nothing else, maybe with one more backslash.  
  
Yes, that was part of how `weakly_canonical` works, and this turned out to be the problem because the "C:" path is not absolute. I have committed a fix that should resolve this problem.  
  
Thank you for reporting this.

---

## Comment 11

> Username: emmenlau  
> Created at: 2022-08-10 12:30:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1210606693  

Ok, I'm sitting at the Windows computer and can reproduce the issue now.  
  
The path I pass to `weakly_canonical` is `\\\\?\\C:\\data\\aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`. The double backslashes in the string are copied from source code, but the actual string should have single backslashes.  
  
With this path, I get exception `boost::filesystem::filesystem_error` with description `boost::filesystem::weakly_canonical: Incorrect function [system:1]: "\\?\C:"`  
  
When I execute the test in the debugger, it stops with an exception in `operations.cpp` line 4076. In my version of boost (1.79.0) that line reads:  
```  
BOOST_FILESYSTEM_THROW(filesystem_error("boost::filesystem::weakly_canonical", head, local_ec));  
```  
The debugger says about the exception:  
```  
Exception thrown at 0x00007FFBC778474C in BaseFileTest.exe: Microsoft C++ exception: boost::filesystem::filesystem_error at memory location 0x0000001AA913EDE0.  
```

---

## Comment 12

> Username: emmenlau  
> Created at: 2022-08-10 12:40:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1210618291  

But I see its fixed now, thanks!

---

## Comment 13

> Username: greenkalx  
> Created at: 2023-01-11 23:58:12 UTC  
> Updated at: 2023-01-12 02:18:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1379633581  

I was expecting this fix in 1_81_0.  
  
I see the changes in `libs\filesystem\src\operations.cpp` from 1_80 to 1_81.  
  
We have three failing unit tests:  
```  
BOOST_CHECK(Path(R"(\\?\UNC\x\y)").IsUnc());  
BOOST_CHECK(Path(R"(\\?\UNC\server\sharename\)").IsUnc());  
BOOST_CHECK(Path(R"(\\?\UNC\x\y\s\z)").VolumeRoot() == LR"(\\?/UNC\x\y\)");  
```  
They started failing after 1_73_0, I think first with 1_80_0.  
  
System is Windows 10 with long paths enabled, compiling with Visual Studio 2022 C++17.  
  
I built the .lib's and .dll's..  checked for stale references..

---

## Comment 14

> Username: Lastique  
> Created at: 2023-01-12 08:45:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1379989349  

Yes, the fix is in Boost 1.81.0.  
  
I'm not sure what unit tests you're running, they don't look like Boost.Filesystem. If you have a problem with Boost.Filesystem, please report it in a new issue, with a short reproducer.

---

## Comment 15

> Username: greenkalx  
> Created at: 2023-01-13 02:13:57 UTC  
> Updated at: 2023-01-13 04:22:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/247#issuecomment-1381226529  

Created https://github.com/boostorg/filesystem/issues/272
