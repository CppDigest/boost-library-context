# #15 Do not run coverage until all jobs have finished [Closed]

> Username: seelabs  
> Created at: 2016-05-23 17:27:10 UTC  
> Updated at: 2016-05-27 23:03:31 UTC  
> Closed at: 2016-05-27 23:03:31 UTC  
> Url: https://github.com/boostorg/beast/pull/15  

Sometimes lcov would report negative counts. This may be related to lcov  
running while the job it was collecting counts for was also running.  
This patch makes two changes: Do not run the coverage build under  
gdb, and wait for all jobs to finish before running lcov.  
  
@ximinez @sublimator

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-05-23 22:49:29 UTC  
> Url: https://github.com/boostorg/beast/pull/15#issuecomment-221119127  

Nice looking changes especially the number of processors

---

## Comment 2

> Username: sublimator  
> Created_at: 2016-05-24 00:55:18 UTC  
> Url: https://github.com/boostorg/beast/pull/15#issuecomment-221138245  

good call on the wait!  
  
Gdb messes with coverage results?

---

## Comment 3

> Username: sublimator  
> Created_at: 2016-05-24 05:23:07 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64329772  

This doesn't work on OSX, nor docker

---

## Comment 4

> Username: sublimator  
> Created_at: 2016-05-24 06:03:25 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64332739  

I've noticed some stalls some time, which were the reason for the (hamfisted) extra kill invocation (one possible cause of the issue).

---

## Comment 5

> Username: sublimator  
> Created_at: 2016-05-24 06:29:23 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64334468  

```  
  
[1]+  Running                 nohup $wsecho &  
+kill -INT %1  
+wait  
+lcov --no-external -c -d . -o testrun.info  
+lcov -a baseline.info -a testrun.info -o lcov-all.info  
lcov: WARNING: negative counts found in tracefile testrun.info  
lcov: ERROR: cannot write to lcov-all.info!  
```  
  
hrmmm ???  You might have gotten lucky here

---

## Comment 6

> Username: sublimator  
> Created_at: 2016-05-24 06:58:51 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64336900  

One thing to note, which may or may not be pertinent, is that the travis run for this pull request didn't actually run `wstest`, even though it started the echo server.

---

## Comment 7

> Username: sublimator  
> Created_at: 2016-05-24 07:48:35 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64343327  

```  
++lscpu -p  
++wc -l  
++sort -u -t, -k 2,4  
++grep -v '^#'  
+num_procs=1  
+build_beast  
+/home/travis/boost_1_60_0/bjam toolset=gcc variant=asan address-model=64 -j1  
```  
  
Seems like Travis is just -j1 as per the usual implicit default. I'm using this in an updated branch:  
  
```  
if [[ `uname` == "Darwin" ]]; then  
  num_jobs=`sysctl -n hw.ncpu`  
else  
  num_jobs=`nproc`  
fi  
```  
  
This works on circle CI Docker and locally and on Travis:  
  
```  
+[[ Linux == \D\a\r\w\i\n ]]  
++nproc  
+num_jobs=2  
```

---

## Comment 8

> Username: sublimator  
> Created_at: 2016-05-24 07:53:03 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64343859  

Noting that, in an updated version, I added a sleep of 5 seconds to let the server `wind down` before issuing a kill. Not sure if it's needed or it will help ?

---

## Comment 9

> Username: seelabs  
> Created_at: 2016-05-24 12:36:58 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64382245  

Thanks, pushed a fix for osx

---

## Comment 10

> Username: seelabs  
> Created_at: 2016-05-24 12:43:31 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64383146  

Rats. OK, thanks. We'll have to think of something else.

---

## Comment 11

> Username: sublimator  
> Created_at: 2016-05-24 12:44:55 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64383331  

This only gets 1 on Travis

---

## Comment 12

> Username: sublimator  
> Created_at: 2016-05-24 12:46:14 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64383525  

`nproc` works in Docker / linux  
  
Above method shows 32 on CircleCI, when really the container only has  2 `available`

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2016-05-24 12:46:32 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64383561  

Spelling: `pysical cores`

---

## Comment 14

> Username: sublimator  
> Created_at: 2016-05-24 12:47:20 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64383668  

What's the `-s` arg to uname do?

---

## Comment 15

> Username: seelabs  
> Created_at: 2016-05-24 12:51:15 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64384191  

Fixed

---

## Comment 16

> Username: sublimator  
> Created_at: 2016-05-24 12:53:16 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64384480  

Why not threads? e.g. my quad core i7 has 8 threads and I typically use -j8 for scons etc. Have I been "doing it wrong? ?

---

## Comment 17

> Username: seelabs  
> Created_at: 2016-05-24 12:54:31 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64384661  

Returns the kernel name.

---

## Comment 18

> Username: seelabs  
> Created_at: 2016-05-24 13:18:30 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64388180  

Yeah, you'll get better compilation times if you use physical cores rather than virtual cores (at least on the machines I've tested).

---

## Comment 19

> Username: seelabs  
> Created_at: 2016-05-24 13:20:43 UTC  
> Url: https://github.com/boostorg/beast/pull/15#issuecomment-221266057  

@sublimator I pushed a fix that takes the min of physical cores and virtual cores. That should work well everywhere.

---

## Comment 20

> Username: sublimator  
> Created_at: 2016-05-24 14:55:22 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64406972  

Lesson learned! Cheers

---

## Comment 21

> Username: sublimator  
> Created_at: 2016-05-24 14:59:53 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64408103  

fwiw, since I added in the 5 second sleep after wstest, I haven't noticed any further issues.

---

## Comment 22

> Username: sublimator  
> Created_at: 2016-05-24 15:40:30 UTC  
> Updated_at: 2016-05-25 15:58:40 UTC  
> Url: https://github.com/boostorg/beast/pull/15#discussion_r64416884  

We'll want `sysctl -n hw.physicalcpu` to get the `physical cores` on OSX it seems :)

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2016-05-25 19:12:14 UTC  
> Url: https://github.com/boostorg/beast/pull/15#issuecomment-221676749  

This is merged in b5 but we still get "negative counts" from lcov.

---
