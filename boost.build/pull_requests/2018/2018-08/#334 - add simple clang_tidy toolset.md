# #334 add simple clang_tidy toolset [Closed]

> Username: arvidn  
> Created at: 2018-08-20 13:42:12 UTC  
> Updated at: 2021-10-02 20:59:24 UTC  
> Closed at: 2021-06-11 01:54:37 UTC  
> Url: https://github.com/boostorg/build/pull/334  

This makes it possible to run clang-tidy instead of building. instead of creating object files and binaries, it simply just creates empty files, to indicate it was successfully run.  
  
I've been using this in my project to run clang-tidy on travis, [here](https://github.com/arvidn/libtorrent/blob/master/.travis.yml#L123). (note that compilation errors are not reported as failures by clang-tidy, which happens to make that work because clang-tidy on travis has some issues with the standard library I believe).  
  
The reason this toolset is not called `clang-tidy` is that it would clash with the clang toolset, since `-` is used to separate toolset name from version.  
  
It does not handle `run` rules very gracefully, as the binaries it outputs are just empty files, trying to run them will fail. But it works as long as you specify `testing.execute=off`.  
  
I think this is a much better approach than https://github.com/boostorg/build/pull/133, and is meant to replace it.  
  
Thoughts?

---

## Review 1 [Commented]

> Username: frenchtoast747  
> Created_at: 2018-08-20 16:35:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/334#pullrequestreview-147718551  

Some comments about the implementation. I can't say whether it would be good to pull in or not.

📁 src/tools/clang_tidy.jam

```diff
  57 |+ 
  58 |+ actions compile.c++ {
  59 |+   "$(CONFIG_COMMAND)" -header-filter=*. -warnings-as-errors=* "$(>)" -- -x c++ $(OPTIONS) -D$(DEFINES) -I"$(INCLUDES)" \
```

> Username: frenchtoast747  
> Created_at: 2018-08-20 16:17:47 UTC  
> Updated_at: 2018-08-20 16:35:16 UTC  
> Url: https://github.com/boostorg/build/pull/334#discussion_r211322464  

Does clang-tidy allow for response files as input?  
  
If so, it would be nice to generate that here in the event that `DEFINES` or `INCLUDES` becomes large (to prevent blowing through max command line length):  
e.g. `@@($(>:D).rsp:E=$(OPTIONS) -D$(DEFINES) -I"$(INCLUDES)")`  
  
See [msvc.jam](https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam#L605) for an example of the syntax and note that you may have to join each of the options with a newline depending on how clang's response files work.  
  
Same for `compile.c` below.

---

📁 src/tools/clang_tidy.jam

```diff
  18 |+ 
  19 |+ rule init ( version ? :  command * : options * ) {
  20 |+   command = [ common.get-invocation-command clang_tidy : clang-tidy
```

> Username: frenchtoast747  
> Created_at: 2018-08-20 16:22:13 UTC  
> Updated_at: 2018-08-20 16:35:16 UTC  
> Url: https://github.com/boostorg/build/pull/334#discussion_r211323913  

It would be nice if this could provide some common installation paths (if there are any).


---

## Comment 2

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:50 UTC  
> Url: https://github.com/boostorg/build/pull/334#issuecomment-850859495  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:23 UTC  
> Url: https://github.com/boostorg/build/pull/334#issuecomment-932819637  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
