# #329 - Cannot override positional-dependent options [Open]

> Username: toonetown  
> Created at: 2018-08-10 01:05:35 UTC  
> Updated at: 2021-06-26 03:09:58 UTC  
> Url: https://github.com/boostorg/build/issues/329  

With the change at https://github.com/boostorg/build/commit/61989801cab6a8f92969fcb82340a1e299e14e5e a (perhaps) unintended side effect is that options specified in a `project-config.jam` file are no longer listed last - so those options (`-fvisibility` for example) which are position-dependent (later ones override earlier ones), there is no longer a way to override.  
  
This can be seen by looking at the compile commands for `boost::log` and `boost::serialization` if you specify `<cxxflags>-fvisibility=default` in `project-config.jam`.  Prior to this commit (and, rolling back this commit) will place the `-fvisibility=default` *after* `-fvisibility=hidden`.  After this commit, the `-fvisibility=hidden` comes after, thus not providing a way to override the option.  
  
I am attaching a [project-config.txt](https://github.com/boostorg/build/files/2276270/project-config.txt) file.  Building `boost::log` with clang on macOS will demonstrate the issue.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-08-10 01:42:26 UTC  
> Url: https://github.com/boostorg/build/issues/329#issuecomment-411949663  

AMDG  
  
  In general, controlling option order is poorly supported  
by Boost.Build.  This particular issue has always been  
handled inconsistently between toolsets.  This changeset  
makes the behavior the same as for gcc.  In addition, the  
generic behavior for Boost.Build is that local options  
override global ones, so having the toolset options override  
the requirements on specific targets is somewhat problematic  
to begin with.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: toonetown  
> Created at: 2018-08-10 02:17:16 UTC  
> Url: https://github.com/boostorg/build/issues/329#issuecomment-411954907  

I am OK with the way it is - especially if it means more consistency.  I would value consistency over configurability.  The reason I logged this was that it was a change in behavior from one version to another (and it was a change that happened to bite me).   
  
If there were a way to specify something to go in to the `$(USER_OPTIONS)` variable, that would completely solve my problem.  How do you get values into there?  When I specify values, such as `<cxxflags>`, in `project-config.jam` they appear to go into the `$(OPTIONS)`.   
  
Perhaps this is a documentation task instead of a change in functionality.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-08-10 02:35:45 UTC  
> Url: https://github.com/boostorg/build/issues/329#issuecomment-411957704  

AMDG  
  
On 08/09/2018 08:17 PM, Nathan Toone wrote:  
> I am OK with the way it is - especially if it means more consistency.  I would value consistency over configurability.  The reason I logged this was that it was a change in behavior from one version to another (and it was a change that happened to bite me).   
>   
> If there were a way to specify something to go in to the `$(USER_OPTIONS)` variable, that would completely solve my problem.  How do you get values into there?  When I specify values, such as `<cxxflags>`, in `project-config.jam` they appear to go into the `$(OPTIONS)`.   
>   
  
If you put this in project-config.jam, it will end up in USER_OPTIONS:  
  
project :  
  requirements  
    <toolset>clang-darwin:<cxxflags>-fvisibility=default  
;  
  
However, this won't actually solve your problem, because  
-fvisibility=hidden is also in USER_OPTIONS and the  
options are sorted alphabetically, in this case.  There  
is a syntax to control option order explicitly, but IIRC,  
it only works for path features.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:22 UTC  
> Url: https://github.com/boostorg/build/issues/329#issuecomment-868936419  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
