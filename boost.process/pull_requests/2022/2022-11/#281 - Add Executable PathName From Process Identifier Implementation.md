# #281 Add Executable PathName From Process Identifier Implementation. [Closed]

> Username: ghost  
> Created at: 2022-11-30 15:51:21 UTC  
> Updated at: 2022-12-27 04:42:26 UTC  
> Closed at: 2022-12-27 04:42:26 UTC  
> Url: https://github.com/boostorg/process/pull/281  

**exe_path(pid) documentation:**  
  
`boost::filesystem::path boost::process::v2::ext::exe_path(boost::process::pid_type pid);` returns the executable pathname associated with the given process identifier in the pid argument. boost::filesystem::path::string::empty() is true if the function has failed. The return value will be an absolute pathname with all symbollic links resolved. The pathname will be normalized, meaning there will be no double slashes, no dot, and no dot-dot in the returned pathname. The returned pathname is limited to MAX_PATH characters on Windows, and on Unix-likes, the returned pathname is limited to PATH_MAX characters. PATH_MAX is implementation-defined by the platform. On OpenBSD, the platform relies on retrieving argv[0] from the assocaited process's command line arguments, to resolve the executable pathname, because OpenBSD does not have an official or dedicated API for such. The argv[0] suffix is appended to the $PWD environment variable of the associated process as the prefix, when the argv[0] suffix is a relative pathname, with the current working directory of the assocaited process as the fallback prefix. On failure, the executable pathname will have a wrong ino_t and/or dev_t value, and the function will return an empty string.

---

## Review 1 [Changes requested]

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:07:26 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/process/pull/281#pullrequestreview-1199604841  

📁 include/boost/process/v2/detail/impl/exe.ipp

```diff
  24 |+     LUID luid;
  25 |+     TOKEN_PRIVILEGES tkp;
  26 |+     if (OpenProcessToken(GetCurrentProcess(), TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, &hToken)) {
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:02:38 UTC  
> Updated_at: 2022-11-30 16:07:26 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036159541  

shouldn't that failing create an error?

> Username: Unknown  
> Created_at: 2022-11-30 16:20:44 UTC  
> Updated_at: 2022-11-30 16:20:45 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036180467  

The error gets recorded to GetLastError(). Although the website doesn't seem to say why it would ever error, so I'm not sure what the failure cases would be without debugging it hands on.

> Username: Unknown  
> Created_at: 2022-12-01 16:46:16 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1037345579  

I think i know what you want now, you want me to add an error code argument to the helper functions so i can pass the errors on to the functions which call them. makes sense now. Taking care of it.

---

📁 include/boost/process/v2/detail/impl/exe.ipp

```diff
  42 |+     BOOL isWow = true;
  43 |+     SYSTEM_INFO systemInfo;
  44 |+     GetNativeSystemInfo(&systemInfo);
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:02:47 UTC  
> Updated_at: 2022-11-30 16:07:26 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036159712  

Can't fail?

> Username: Unknown  
> Created_at: 2022-11-30 16:17:16 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036176465  

That is correct, unless I missed something: https://learn.microsoft.com/en-us/windows/win32/api/sysinfoapi/nf-sysinfoapi-getnativesysteminfo

> Username: Unknown  
> Created_at: 2022-11-30 17:14:02 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036241120  

The function below it can fail, though, so i added a check.  I'm not sure what to do when that function fails, because if it returns a true, but the target process is actually x64 then it will likely segfault when reading proc info depending on the function


📁 include/boost/process/v2/detail/impl/process_handle_windows.ipp

```diff
 137 |+   auto nt_err = NtResumeProcess(handle);
 138 |+   if (nt_err > 0xC0000000)
 139 |+     ec = detail::get_last_error();
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:03:07 UTC  
> Updated_at: 2022-11-30 16:07:26 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036160107  

I'll need to check if that is actually correct.

> Username: Unknown  
> Created_at: 2022-12-15 02:28:49 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1049153494  

I guess this can be marked as resolved as soon as I use your suggestion to convert NTSTATUS to a 'normal' win32 error code. I'll let you know once I've done this.


📁 include/boost/process/v2/detail/process_handle_fd.hpp

```diff
 216 |+         if (pid_ <= 0)
 217 |+             return ;
 218 |+         if (::kill(pid_, SIGTERM) == -1)
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:03:23 UTC  
> Updated_at: 2022-11-30 16:07:26 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036160435  

my typo?

> Username: Unknown  
> Created_at: 2022-11-30 16:25:28 UTC  
> Updated_at: 2022-11-30 16:25:47 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036185986  

Glad to know even the best of us can typo.


📁 include/boost/process/v2/detail/process_handle_fd_or_signal.hpp

```diff
 245 |+         if (pid_ <= 0)
 246 |+             return ;
 247 |+         if (::kill(pid_, SIGTERM) == -1)
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:03:40 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036160749  

sam here - looks like it should be `SIGCONT`.


📁 include/boost/process/v2/exe.hpp

```diff
  28 |+ #else
  29 |+ #error "Platform not supported"
  30 |+ #endif
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:04:07 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036161310  

all the same decl, no need for the #ifdefs.


📁 include/boost/process/v2/impl/exe.ipp

```diff
  46 |+ {
  47 |+     boost::filesystem::path path;
  48 |+     if (pid == GetCurrentProcessId())
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:04:45 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036162133  

I wonder if we (1) need this and (2) if that maybe should be it's own function.

> Username: Unknown  
> Created_at: 2022-11-30 17:23:27 UTC  
> Updated_at: 2022-11-30 17:28:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036251089  

> To retrieve the module name of the current process, use the [GetModuleFileName](https://learn.microsoft.com/en-us/windows/desktop/api/libloaderapi/nf-libloaderapi-getmodulefilenamea) function with a NULL module handle. This is more efficient than calling the GetProcessImageFileName function with a handle to the current process.  
>   
> To retrieve the name of the main executable module for a remote process in win32 path format, use the [QueryFullProcessImageName](https://learn.microsoft.com/en-us/windows/desktop/api/winbase/nf-winbase-queryfullprocessimagenamea) function.  
  
See "Remarks" at:  
  
https://learn.microsoft.com/en-us/windows/win32/api/psapi/nf-psapi-getprocessimagefilenamea  
  
They don't make it all that obvious, but it sounds to me like GetModuleFileName() with nullptr is more efficient than GetProcessImageFileName **and** QueryFullProcessImageName, at least to me it seems they are saying that.  
  
At least when trying to get the exe path from the current process.

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
  64 |+         wchar_t buffer[MAX_PATH];
  65 |+         DWORD size = sizeof(buffer);
  66 |+         if (QueryFullProcessImageNameW(proc, 0, buffer, &size) != 0)
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:04:51 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036162249  

Error handling?

> Username: Unknown  
> Created_at: 2022-11-30 17:06:21 UTC  
> Updated_at: 2022-11-30 17:06:22 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036233155  

Are you saying you want me to store the value of GetLastErrorr somewhere? I thought std::error_code did this automatically with the function I am using when it returns an empty string.

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 123 |+ boost::filesystem::path exe_path(pid_type pid, error_code & ec) 
 124 |+ {
 125 |+     std::boost::filesystem::path;
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:05:14 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036162688  

can I get a few comments of what this is?

> Username: Unknown  
> Created_at: 2022-11-30 16:29:51 UTC  
> Updated_at: 2022-11-30 16:29:52 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036191334  

that was a typo, isn't it just without the std::?

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 208 |+                         if (kif[i].fd_fd == KERN_FILE_TEXT) 
 209 |+                         {
 210 |+                             if (st.st_dev == (dev_t)kif[i].va_fsid || st.st_ino == (ino_t)kif[i].va_fileid)
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:05:45 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036163326  

that's one `if` statement.  
  
`static_cast` plz

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 193 |+         bool success = false;
 194 |+         struct stat st;
 195 |+         if (!stat(in.c_str(), &st) && (st.st_mode & S_IXUSR) && (st.st_mode & S_IFREG))
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:05:55 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036163503  

can be one `if` statement.


📁 include/boost/process/v2/impl/pid.ipp

```diff
  69 |+ #if defined(BOOST_PROCESS_V2_WINDOWS)
  70 |+ 
  71 |+ std::vector<pid_type> all_pids(error_code & ec)
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:06:39 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036164286  

I wonder if that should be able to take a custom allocator.

> Username: Unknown  
> Created_at: 2022-11-30 17:10:20 UTC  
> Updated_at: 2022-11-30 17:10:21 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036237150  

You mean using malloc or new instead of std::vector?

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 14:15:33 UTC  
> Updated_at: 2022-12-04 14:15:34 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038978289  

no, something like `std::pmr::polymorphic_allocator`. But I am unsure that's worth it here.

> Username: Unknown  
> Created_at: 2022-12-16 03:36:38 UTC  
> Updated_at: 2022-12-16 03:36:39 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1050340759  

let me know once you've made up your mind on this

> Username: klemens-morgenstern  
> Created_at: 2022-12-16 05:02:10 UTC  
> Updated_at: 2022-12-16 05:02:11 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1050373813  

yeah it's nonsense. we're iterating through the process table, a few allocs won't matter.


📁 test/v2/pid.cpp

```diff
  15 |+ 
  16 |+     auto all = bp2::all_pids();
  17 |+     auto itr = std::find(all.begin(), all.end(), bp2::current_pid());
```

> Username: klemens-morgenstern  
> Created_at: 2022-11-30 16:07:19 UTC  
> Updated_at: 2022-11-30 16:07:27 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036165037  

This should have two more tests: one for a child process and one for a grand child.

> Username: Unknown  
> Created_at: 2022-11-30 16:49:58 UTC  
> Updated_at: 2022-11-30 16:49:59 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036215503  

I won't be able to do that until I add my parent and child from pid functions. This pr is meant just for getting the exe path from a pid. If you are ok with me conflating the two pull requests I can do that if you want.

> Username: Unknown  
> Created_at: 2022-11-30 16:50:26 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1036216063  

In the meantime, I'm going to create a test for getting exe from path out of all pids.


---

## Comment 2

> Username: ghost  
> Created_at: 2022-11-30 17:19:29 UTC  
> Url: https://github.com/boostorg/process/pull/281#issuecomment-1332494537  

@klemens-morgenstern i marked a few conversations as resolved, feel free to unresolve them if i did so incorrectly.

---

## Review 3 [Changes requested]

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:50:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/process/pull/281#pullrequestreview-1203614313  

📁 include/boost/process/v2/detail/exe.hpp

```diff
   1 |+ // Copyright (c) 2022 Klemens D. Morgenstern
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:08:54 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038926473  

Thy name goes here.


📁 include/boost/process/v2/detail/impl/exe.ipp

```diff
  21 |+ #if defined(_WIN32)
  22 |+ 
  23 |+ HANDLE open_process_with_debug_privilege(pid_type pid, error_code & ec) {
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:09:59 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038926585  

```diff  
-HANDLE open_process_with_debug_privilege(pid_type pid, error_code & ec) {  
+HANDLE open_process_with_debug_privilege(pid_type pid, error_code & ec)   
+{  
```  
  
Personal preference.

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:11:09 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038926775  

I'd also like to get a few lines on what this does and why we need it. Not to mention that `basic_process`.

> Username: Unknown  
> Created_at: 2022-12-04 13:40:16 UTC  
> Updated_at: 2022-12-04 13:40:17 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038972760  

Not to mention that basic_process? What do you mean by that? I added a few lines giving explanation comments.

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 14:16:36 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038978423  

I probably should've finished the sentence - the basic_process you get as result of launching already has the debug privilege I think.

> Username: Unknown  
> Created_at: 2022-12-05 03:04:10 UTC  
> Updated_at: 2022-12-05 03:04:11 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1039104632  

well that's only if you launched it yourself it sounds like you meant, however this can access any process even ones you didn't run yourself. It allows you to read information from processes run by other users as well as admin run processes.

---

📁 include/boost/process/v2/detail/impl/exe.ipp

```diff
  47 |+ }
  48 |+ 
  49 |+ BOOL is_x86_process(HANDLE proc, error_code & ec) {
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:11:53 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038926878  

Just out of curiosity: Is there something like that on linux?

> Username: Unknown  
> Created_at: 2022-12-04 13:38:58 UTC  
> Updated_at: 2022-12-04 13:38:59 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038972514  

If linux has anything like this it is bound to be provided by the procfs. A quick google search didn't give me any results i was hoping for, but i can look more after addressing more pressing things you critiqued :)


📁 include/boost/process/v2/detail/impl/process_handle_windows.ipp

```diff
 136 |+     auto nt_err = NtResumeProcess(handle);
 137 |+     if (nt_err > 0xC0000000)
 138 |+         ec = detail::get_last_error();
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:12:44 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038927028  

We need to check if the GetLastError call is correct here. It's possble we need another error category, if the codes don't align.

> Username: Unknown  
> Created_at: 2022-12-04 13:45:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038973605  

I'm not sure if GetLastError returns with these undocumented api's. Isn't that what the NTSTATUS is for?

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 14:17:17 UTC  
> Updated_at: 2022-12-04 14:17:18 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038978510  

I think so - and we'd need an error_category for NTSTATUS then, which may end up horrific.

> Username: Unknown  
> Created_at: 2022-12-05 03:06:13 UTC  
> Updated_at: 2022-12-05 03:06:40 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1039105316  

yeah - i wouldn't overthink it, i would prefer being less paranoid for now and be satisfied with something less than perfect and fix it as needed if someone reports breakage or flaws, that as the only real way to know how to perfect it would be through hands on test cases imo


📁 include/boost/process/v2/impl/exe.ipp

```diff
 120 |+         path = exe;
 121 |+     }
 122 |+     if (path.empty())
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:17:33 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038927629  

This error handling probably works, but is incorrect. Doc states  
  
> Otherwise, it returns NULL, the contents of the array  resolved_path are undefined, and [errno](https://man7.org/linux/man-pages/man3/errno.3.html) is set to indicate the error.  
  
So the way I'd write this is as so:  
  
```cpp  
    char exe[PATH_MAX];  
    if (realpath(("/proc/" + std::to_string(pid) + "/exe").c_str(), exe) != nullptr)   
        path = exe;  
    else  
        ec = detail::get_last_error();  
```

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 130 |+ {
 131 |+     std::string path;
 132 |+     int mib[4];
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:20:05 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038927897  

I personally would do this:   
```diff  
-    int mib[4];  
+    int mib[4] = {CTL_KERN, KERN_PROC, KERN_PROC_PATHNAME, pid};  
```

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 147 |+             {
 148 |+                 path = buffer;
 149 |+             }
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:20:22 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038927945  

^ error handling?

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 166 |+     mib[2] = pid;
 167 |+     mib[3] = KERN_PROC_PATHNAME;
 168 |+     if (sysctl(mib, 4, nullptr, &len, nullptr, 0) == 0)
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:20:56 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038928017  

this looks like it's the same as above? Why not just put the #if around the `mib` ?

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 224 |+             }
 225 |+             kvm_close(kd);
 226 |+         }
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:23:35 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038928302  

error handling !

> Username: Unknown  
> Created_at: 2022-12-04 10:20:55 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038944468  

lol will do

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 248 |+                     std::string tmp;
 249 |+                     std::stringstream sstr(penv); 
 250 |+                     while (std::getline(sstr, tmp, ':'))
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:35:01 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038929624  

That won't do. I got a whole set of facilities in the `environment` header to do all this stuff without the overhead of `string`  and `stringstream`. Please look there and minimize the amount of allocations.

---

📁 include/boost/process/v2/impl/exe.ipp

```diff
 293 |+ filesystem::path exe_path(boost::process::v2::pid_type pid, std::error_code & ec)
 294 |+ {
 295 |+     std::string path;
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:35:29 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038929666  

why the intermediate `path` string btw.? WHy not just return `exe`?

> Username: Unknown  
> Created_at: 2022-12-04 10:17:09 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038943962  

I see now, the problem you had earlier with my realpath error handling was related to the fact i am copying the strings, which is more memory being allocated when it could be avoided. I'll fix that, nice catch!


📁 include/boost/process/v2/exe.hpp

```diff
  23 |+ BOOST_PROCESS_V2_DECL boost::filesystem::path exe_path(boost::process::v2::pid_type pid, std::error_code & ec);
  24 |+ BOOST_PROCESS_V2_DECL boost::filesystem::path exe_path(boost::process::v2::pid_type pid);
  25 |+ #endif
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:18:36 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038927731  

I am wondering about the naming - wouldn't `executable` be a fit too?  
  
```cpp  
executable(proc.id());  
```  
  
Looks pretty good to me.


📁 include/boost/process/v2/impl/pid.ipp

```diff
 235 |+         {
 236 |+             char *token = nullptr;
 237 |+             if ((token = strtok(buffer, " "))) {
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:37:36 UTC  
> Updated_at: 2022-12-04 08:50:41 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038929886  

this if thingy is hard to read, can you organize that differently?

---

📁 include/boost/process/v2/impl/pid.ipp

```diff
 239 |+                     if ((token = strtok(nullptr, " "))) {
 240 |+                         if ((token = strtok(nullptr, " "))) {
 241 |+                             pid_type ppid = (pid_type)strtoul(token, nullptr, 10);
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:37:43 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038929926  

`static_cast`

---

📁 include/boost/process/v2/impl/pid.ipp

```diff
 248 |+             {
 249 |+                 ec = detail::get_last_error();
 250 |+                 return vec;
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:38:06 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038929999  

that would leave `stat` open, wouldn't it?

> Username: Unknown  
> Created_at: 2022-12-04 13:32:44 UTC  
> Updated_at: 2022-12-04 13:32:45 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038971532  

correcto, nice catch

---

📁 include/boost/process/v2/impl/pid.ipp

```diff
 280 |+     {
 281 |+         for (int i = 0; i < cntp; i++)
 282 |+             vec.push_back(proc_info[i].ki_pid);
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:39:16 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038930138  

technically a data leak, if `push_back` throws, `free` won't get called.  
  
Also: call `vec.reserve()` in all the examples please, so we only alloc once.

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:39:38 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038930183  

A smart_ptr might also do - `unique_ptr` with a custom deleter will do the trick.

> Username: Unknown  
> Created_at: 2022-12-11 04:05:40 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1045168573  

Are you saying we should use `std::unique_ptr<pid_type>` instead of `std::vector<pid_type>`, correct?

---

📁 include/boost/process/v2/impl/pid.ipp

```diff
 582 |+ }
 583 |+ 
 584 |+ std::vector<pid_type> parent_pid(pid_type pid, std::error_code & ec) {
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:48:25 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038931277  

why is this returning a vector and not just one `pid_type` ?

> Username: Unknown  
> Created_at: 2022-12-04 13:35:03 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038971939  

in the case of error, it shouldn't return a pid at all. If you have a suggestion on an alternate way of handling that, I'm all ears.

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 14:14:57 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038978188  

I think `-1` is fair - the value is nonsense if `ec` got set.

> Username: Unknown  
> Created_at: 2022-12-04 14:24:19 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038979533  

-1 is not possible on windows because it is unsigned long. although we could do static_cast<pid_type>(-1) which i guess is basically what you meant


📁 test/v2/pid.cpp

```diff
  37 |+     };
  38 |+     #if defined(_WIN32)
  39 |+     // pid of zero is guaranteed to exist on Windows
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:49:12 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038931388  

this should be unified!

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:49:35 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038931454  

Could be that you have a `root_pid` constant that is either 1 or 0 depending on the os.

> Username: Unknown  
> Created_at: 2022-12-04 10:19:07 UTC  
> Updated_at: 2022-12-04 12:50:14 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038944202  

Yeah, that's a good point. I'll create a root_pid constant then.

---

📁 test/v2/pid.cpp

```diff
  43 |+     BOOST_CHECK_NE(grand_child_pids(1, children, grand_children), false);
  44 |+     #endif
  45 |+ }
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-04 08:50:01 UTC  
> Updated_at: 2022-12-04 08:50:42 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038931517  

Is there a test to check for a child process we launched?

> Username: Unknown  
> Created_at: 2022-12-04 10:20:13 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1038944377  

That would probably be better than what I have now, actually, because this test fails inconsistently where it stands.


---

## Review 4 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2022-12-13 09:17:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/281#pullrequestreview-1215053297  

📁 include/boost/process/v2/impl/cwd.ipp

```diff
  74 |+       goto err;
  75 |+     }
  76 |+     boost::process::v2::detail::ext::cwd_cmd_env_from_proc(proc, &buffer, MEMCWD, ec);
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-13 09:17:57 UTC  
> Url: https://github.com/boostorg/process/pull/281#discussion_r1046851850  

I think this should return a buffer in a `vector`.


---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2022-12-13 09:18:28 UTC  
> Url: https://github.com/boostorg/process/pull/281#issuecomment-1348013288  

I also think the `ext` stuff should be in a  `v2/ext` folder, rather then just `v2`.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2022-12-13 09:25:28 UTC  
> Url: https://github.com/boostorg/process/pull/281#issuecomment-1348031347  

Found it: https://learn.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-rtlntstatustodoserror  
  
^ this needs to be used when we get an NTSTATUS as return value.

---

## Comment 7

> Username: ghost  
> Created_at: 2022-12-15 01:40:33 UTC  
> Url: https://github.com/boostorg/process/pull/281#issuecomment-1352446930  

> Found it: https://learn.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-rtlntstatustodoserror  
>   
> ^ this needs to be used when we get an NTSTATUS as return value.  
  
Nice find!!!! :D

---
