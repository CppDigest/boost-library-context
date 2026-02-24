# #189 - open_or_create stuck in retry loop indefinitely on macOS aarch64 if retries are enabled [Open]

> Username: uptycs-rmack  
> Created at: 2022-12-27 14:19:59 UTC  
> Updated at: 2022-12-27 21:10:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/189  

Hi, I don't have an isolated repro case at the moment but wanted to share my findings. I have a cross-platform application that worked well with boost 1.66 on Linux x86_64, Linux ppc64le, Linux s390x, macOS x86_64, macOS aarch64 (M1). I upgraded to boost 1.81 and all platforms worked well except macOS aarch64. On that platform open_or_create sticks in a loop and never finishes. The two macOS platforms are compiled as a single universal binary with -arch x86_64 -arch arm64 mode using Apple clang version 14.0.0 (clang-1400.0.29.202). On the same M1 host, running the x86_64 binary under Rosetta emulation works, but the native aarch64 binary hangs. Changing BOOST_INTERPROCESS_MANAGED_OPEN_OR_CREATE_INITIALIZE_MAX_TRIES to 1 to disable the new retry behavior resolves the issue. If max tries is set to 2 or more it hangs. I'm happy to run any tests you recommend but I am unlikely to have time to try to whittle down my application to a self contained repro case for at least a few weeks. Thanks, Ryan

---

## Comment 1

> Username: uptycs-rmack  
> Created at: 2022-12-27 21:10:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/189#issuecomment-1366183292  

A bit more detail here, the reason it was going into the retry loop in the first place appears to be that the fix for GitHub Issue #53 actually broke my application. Rolling back that fix corrected my application's behavior and then whether or not the retry loop got stuck was immaterial.
