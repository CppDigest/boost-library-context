# #290 hey boi [Merged]

> Username: ghost  
> Created at: 2022-12-27 04:56:08 UTC  
> Updated at: 2023-02-06 12:36:50 UTC  
> Merged at: 2023-02-06 12:36:50 UTC  
> Closed at: 2023-02-06 12:36:50 UTC  
> Url: https://github.com/boostorg/process/pull/290  



---

## Review 1 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:03:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/290#pullrequestreview-1230348210  

📁 include/boost/process/v2/ext/impl/cmd.ipp

```diff
 376 |-     if ((proc_info = kvm_getproc2(boost::process::v2::detail::ext::kd, KERN_PROC_PID, pid, sizeof(struct kinfo_proc2), &cntp))) 
 374 |+     if (!kd.get()) {ec = detail::get_last_error(); return vec;}
 375 |+     if ((proc_info = kvm_getproc2(kd.get().get().get().get().get().get().get(), KERN_PROC_PID, pid, sizeof(struct kinfo_proc2), &cntp)))
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:00:39 UTC  
> Updated_at: 2022-12-27 05:03:37 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1057441088  

that ain't looking right

> Username: Unknown  
> Created_at: 2023-01-02 12:34:45 UTC  
> Updated_at: 2023-01-02 12:34:46 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1060006747  

That's because I am out to `.get()` you! >:D


📁 include/boost/process/v2/ext/impl/exe.ipp

```diff
 161 |     {
 162 |-         std::string strbuff;
 162 |+         std::vector<char> strbuff;
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:00:58 UTC  
> Updated_at: 2022-12-27 05:03:37 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1057441149  

Why not string? With vector .data() is correct in C++11.

> Username: Unknown  
> Created_at: 2022-12-28 22:56:41 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1058640066  

The string is getting modified. .data() is const in c++11.


📁 include/boost/process/v2/impl/pid.ipp

```diff
 343 |+ 
 344 |+     std::unique_ptr<kvm_t, closer> kd{kvm_openfiles(nlistf, memf, nullptr, O_RDONLY, nullptr)};
 345 |+     if (!kd.get())
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:01:13 UTC  
> Updated_at: 2022-12-27 05:03:37 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1057441207  

the .get() is not necessary here.


📁 test/v2/Jamfile.jam

```diff
  31 |+   <target-os>bsd:<linkflags>-lc
  32 |+   <target-os>bsd:<linkflags>-lkvm
  33 |+   <target-os>bsd:<cxxflags>-std=c++11
```

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:02:21 UTC  
> Updated_at: 2022-12-27 05:03:37 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1057441502  

Nope, wrong place. I think -lkvm should suffice, actually.

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:02:38 UTC  
> Updated_at: 2022-12-27 05:03:37 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1057441586  

Of course also wrong above for freebsd.

> Username: klemens-morgenstern  
> Created_at: 2022-12-27 05:03:33 UTC  
> Updated_at: 2022-12-27 05:03:37 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1057441855  

Run `b2 cxxstd=11` from the command line.

> Username: Unknown  
> Created_at: 2022-12-28 22:53:50 UTC  
> Updated_at: 2023-01-02 12:36:09 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1058639318  

That might be true with -lpthread, but I know for certain from experience on *BSD's they outright do not link to -lc without the developer doing so manually.  
  
Edit:  
  
You guys have your own build system, which might handle this on *BSD's automatically, so I'm probably wrong about what I said.


---

## Review 2 [Commented]

> Username: Unknown  
> Created_at: 2023-01-18 02:36:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/290#pullrequestreview-1252749823  

📁 include/boost/process/v2/ext/detail/proc_info.hpp

```diff
 109 | BOOST_PROCESS_V2_DECL HANDLE open_process_with_debug_privilege(boost::process::v2::pid_type pid, boost::system::error_code & ec);
  46 |- BOOST_PROCESS_V2_DECL BOOL is_x86_process(HANDLE proc, boost::system::error_code & ec);
 110 |+ BOOST_PROCESS_V2_DECL ULONG RtlNtStatusToDosError(NTSTATUS Status) {
```

> Username: Unknown  
> Created_at: 2023-01-18 02:36:55 UTC  
> Url: https://github.com/boostorg/process/pull/290#discussion_r1073036205  

@klemens-morgenstern not sure if you are ok with this here but i couldn't get it to build with the `extern "C"` method you mentioned. The docs do say you need to either use LoadLibrary or GetProcAddress


---
