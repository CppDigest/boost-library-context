# #540 Make sure b2 exits immediately on syntax errors [Merged]

> Username: mloskot  
> Created at: 2020-02-26 23:34:49 UTC  
> Updated at: 2021-10-02 21:13:18 UTC  
> Merged at: 2020-03-02 20:03:39 UTC  
> Closed at: 2020-03-02 20:03:39 UTC  
> Url: https://github.com/boostorg/build/pull/540  

Refines #538  
  
Fixes #539  
  
------  
  
  
There is a difference between BEFORE/AFTER outputs of `b2` for syntactically erroneous files.  
Although I don't understand some of parts of the BEFORE outputs (see questions below), the AFTER output is now shorter and clearer, I think.  
  
  
### Situation BEFORE this fix:  
  
1. Valid `jamroot.jam`  
  
    ```  
    $ cat jamroot.jam  
    exe foo : foo.cpp ;  
  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc  
    ...found 7 targets...  
    ...updating 5 targets...  
    gcc.compile.c++ bin/gcc-9/debug/foo.o  
    gcc.link bin/gcc-9/debug/foo  
    ...updated 5 targets...  
  
    $ echo $?  
    0  
    ```  
  
1. Invalid `jamroot.jam`  
  
    ```  
    $ cat jamroot.jam  
    exe foo : foo.cpp  
  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc  
    jamroot.jam:1: syntax error at EOF  
    bin/project-cache.jam:4: syntax error at keyword {  
    ...found 1 target...  
  
    $ echo $?  
    1  
    ```  
  
    What is this `bin/project-cache.jam:4: syntax error at keyword {` in the output?  
  
1. Invalid `file.jam`  
  
    ```  
    $ cat file.jam  
    exe foo : foo.cpp  
  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 -ffile.jam toolset=gcc  
    file.jam:1: syntax error at EOF  
    don't know how to make toolset=gcc  
    ...found 1 target...  
    ...can't find 1 target...  
  
    $ echo $?  
    1  
    ```  
  
    What is this `don't know how to make toolset=gcc` in the output?  
  
### Situation AFTER this fix:  
  
2. Valid `jamroot.jam`  
  
    ```  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc  
    a  
    ...found 7 targets...  
    ...updating 5 targets...  
    gcc.compile.c++ bin/gcc-9/debug/foo.o  
    gcc.link bin/gcc-9/debug/foo  
    ...updated 5 targets...  
  
    $ echo $?  
    0  
    ```  
  
2. Invalid `jamroot.jam`  
  
    ```  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc  
    jamroot.jam:1: syntax error at EOF  
    ...found 1 target...  
  
    $ echo $?  
    1  
    ```  
  
2. Invalid `file.jam`  
  
    ```  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc -ffile.jam  
    file.jam:1: syntax error at EOF  
  
    $ echo $?  
    1  
    ```

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-02-27 04:30:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/540#pullrequestreview-365408549  

📁 src/engine/jam.cpp

```diff
 654 |-             last_update_now_status = status;
 658 |+         if ( status )
 659 |+             goto cleanup;
```

> Username: grafikrobot  
> Created_at: 2020-02-27 04:30:19 UTC  
> Updated_at: 2020-03-01 18:42:52 UTC  
> Url: https://github.com/boostorg/build/pull/540#discussion_r384908119  

Not fond of `goto` in general. And in this case using it has the effect of skipping over the `PROFILE_EXIT` and `profile_dump`. I'd prefer wrapping the code below it, up to the `PROFILE_EXIT`, with an `if (!status)`.

> Username: mloskot  
> Created_at: 2020-02-27 08:00:03 UTC  
> Updated_at: 2020-03-01 18:42:52 UTC  
> Url: https://github.com/boostorg/build/pull/540#discussion_r384963291  

I was hoping for my first ever `goto` contribution to a production code ;)  
I noticed `goto` users in the current code and I followed the pattern. I did miss the skipped `PROFILE_EXIT` though.  
  
I will change that.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2020-02-27 04:33:44 UTC  
> Url: https://github.com/boostorg/build/pull/540#issuecomment-591775527  

Even though I'd love for the `jamroot.jam` after case was the same immediate fail as the `-ffile.jam` case... It's good for now :-)

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-02-27 18:39:51 UTC  
> Url: https://github.com/boostorg/build/pull/540#issuecomment-592113110  

@grafikrobot I have submitted new commit with the refinement of the change.  
  
But, I've noticed something strange which I need help in clarifying:  
  
- `b2` reports syntax errors to `stdout` and not `stderr`, is this by design?  
- `b2` run for the "Invalid jamroot.jam" test case no longer outputs `jamroot.jam:1: syntax error at EOF` but if I run `-ffile.jam`, the error is printed. This is puzzling the most!  
  
Here is the re-run of the **Situation AFTER this fix** from the PR description above but with `XXX` extra output I added inside `if ( status )` case and inside the `cleanup` function to trace it   
  
1. Valid `jamroot.jam`  
  
    ```  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build" -j1 toolset=gcc >out.txt 2>err.txt  
  
    $ cat out.txt  
    ...found 7 targets...  
    ...updating 5 targets...  
    gcc.compile.c++ bin/gcc-9/debug/foo.o  
    gcc.link bin/gcc-9/debug/foo  
    ...updated 5 targets...  
  
    $ cat err.txt  
    XXX cleanup  
    ```  
  
2. Invalid `jamroot.jam`  
  
    ```  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build" -j1 toolset=gcc >out.txt 2>err.txt  
  
    $ cat out.txt  
    ...found 1 target...  
  
    $ cat err.txt  
    XXX aborting yyanyerrors status=1  
    XXX cleanup  
    ```  
  
    Why no syntax error printed here for `jamroot.jam` but it is printed for `file.jam` below?  
  
    Both files have the same invalid content `exe foo : foo.cpp` w/o ` ;` terminator.  
  
3. Invalid `file.jam`  
  
    ```  
    $ b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build" -j1 -ffile.jam toolset=gcc >out.txt 2>err.txt  
  
    $ cat out.txt  
    file.jam:1: syntax error at EOF  
  
    $ cat err.txt  
    XXX aborting yyanyerrors status=1  
    XXX cleanup  
    ```  
  
What is going on?

---

## Review 4 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-03-01 14:39:43 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/540#pullrequestreview-366863723  

So... I don't understand why you want to move so much code around. When a simpler `if (!status)` around lines 656-674 would do the same?

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-03-01 18:58:29 UTC  
> Url: https://github.com/boostorg/build/pull/540#issuecomment-593131901  

@grafikrobot No exciting reason, I suspected we will need to call the cleanup from more places. I have replaced the function with the branch.

---
