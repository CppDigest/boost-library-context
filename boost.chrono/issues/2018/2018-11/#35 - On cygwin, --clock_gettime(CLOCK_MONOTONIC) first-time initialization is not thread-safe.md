# #35 - On cygwin, ::clock_gettime(CLOCK_MONOTONIC) first-time initialization is not thread-safe [Closed]

> Username: jeking3  
> Created at: 2018-11-21 21:38:26 UTC  
> Updated at: 2018-11-25 07:56:31 UTC  
> Closed at: 2018-11-25 07:56:31 UTC  
> Url: https://github.com/boostorg/chrono/issues/35  

I'm seeing this while building Boost.Thread in cygwin (32-bit).  I added code to dump the values of t0 through t3 in the file `sync/mutual_exclusion/locks/shared_lock/cons/try_to_lock_pass.cpp`.  When it fails, which seems to be about 75% of the time, t0 is zero (ran this from thread/test):  
```  
$ ../../../b2.exe toolset=gcc variant=release warnings-as-errors=on warnings=all --abbreviate-paths define=_POSIX_C_SOURCE=200112L threadapi=pthread address-model=32 cxxstd=11  
 -q cxxflags=-Wno-unknown-pragmas cxxflags=-Wno-unused-variable cxxflags=-Wno-unused-but-set-variable shared_lock__cons__try_to_lock_p  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3243 targets...  
...updating 2 targets...  
testing.capture-output ../../../bin.v2/libs/thread/test/shared_lock__cons__try_to_lock_p.test/gcc-7.3.0/rls/cxstd-11-iso/thrdp-pthrd/thrd-mlt/vsblt-hdn/shared_lock__cons__try_t  
o_lock_p.run  
====== BEGIN OUTPUT ======  
t0=0 nanoseconds since boot  
t1=203061006792900 nanoseconds since boot  
t2=203060757198500 nanoseconds since boot  
t3=203061006765200 nanoseconds since boot  
sleep_time=249566700 nanoseconds  
d_ms=203060757 milliseconds  
d_ns=203060757226200 nanoseconds  
sync/mutual_exclusion/locks/shared_lock/cons/try_to_lock_pass.cpp(120): test '(d_ms).count() < (max_diff).count()' ('203060757' < '250') failed in function 'int main()'  
sync/mutual_exclusion/locks/shared_lock/cons/try_to_lock_pass.cpp(121): test '(d_ns).count() < (ns(max_diff)).count()' ('203060757226200' < '250000000') failed in function 'int  
 main()'  
2 errors detected.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
If I call boost::chrono::high_resolution_clock::now() from the main thread before it gets called from the second thread, everything is fine.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-22 00:20:55 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-440861217  

Also happens in debug builds.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-22 00:35:45 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-440865727  

Interesting, so on cygwin (32-bit) time_t is currently 32 bits, with no option to change it.  This could be an overflow issue that requires a cast.  Looking into it.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-22 01:36:35 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-440884875  

Nope, doesn't seem to be that.

---

## Comment 4

> Username: viboes  
> Created at: 2018-11-22 06:41:41 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-440928505  

Thanks for the report and to be working on a fix.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-22 13:47:29 UTC  
> Updated at: 2018-11-22 13:48:50 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-441035862  

This behavior is really bizarre, I added some code to loop back and ask for t0 again if it is zero:  
```  
  do {  
    t0 = Clock::now();  
    if (t0 == time_point(duration(ns(0))))  
      std::cout << "RETRYING: t0 was 0" << std::endl;  
  } while (t0 == time_point(duration(ns(0))));  
```  
  
It happens a lot, but never takes more than one extra execution to "fix" it (loop.sh runs the same test mentioned in the description 50 times, built debug mode):  
```  
Jim@pulsar /cygdrive/c/boost/libs/thread/test  
$ ./loop.sh  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
RETRYING: t0 was 0  
No errors detected.  
No errors detected.  
No errors detected.  
```  
  
loop.sh:  
```  
#!/bin/bash  
  
PATH="/cygdrive/c/boost/bin.v2/libs/atomic/build/gcc-7.3.0/dbg/cxstd-11-iso/thrd-mlt/vsblt-hdn:/cygdrive/c/boost/bin.v2/libs/chrono/build/gcc-7.3.0/dbg/cxstd-11-iso/thrd-mlt/vsblt-hdn:/cygdrive/c/boost/bin.v2/libs/system/build/gcc-7.3.0/dbg/cxstd-11-iso/thrd-mlt/vsblt-hdn:/cygdrive/c/boost/bin.v2/libs/thread/build/gcc-7.3.0/dbg/cxstd-11-iso/thrdp-pthrd/thrd-mlt/vsblt-hdn:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$PATH"  
  
for loop in {1..50}; do  
  ../../../bin.v2/libs/thread/test/shared_lock__cons__try_to_lock_p.test/gcc-7.3.0/rls/cxstd-11-iso/thrdp-pthrd/thrd-mlt/vsblt-hdn/shared_lock__cons__try_to_lock_p  
done  
```

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-23 16:09:25 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-441277245  

I found the root cause.  Down inside cygwin there is one-time initialization code for the `hires_ns::prime` call inside `winsup/cygwin/times.cc`.  The end result is that there is a race when two threads first call boost::chrono::high_resolution_clock::now() on cygwin.  I have written a simple example outside of boost and I am submitting it to their mailing list.  However, what this means is any call to `::clock_gettime` needs to be serialized on cygwin.  I also saw a FIXME on `gettimeofday` indicating thread safety needs to be added.

---

## Comment 7

> Username: jeking3  
> Created at: 2018-11-23 16:41:21 UTC  
> Url: https://github.com/boostorg/chrono/issues/35#issuecomment-441283448  

It looks like only calls to CLOCK_MONOTONIC need to be serialized for proper behavior.
