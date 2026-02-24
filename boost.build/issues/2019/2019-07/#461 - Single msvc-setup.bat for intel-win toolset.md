# #461 - Single msvc-setup.bat for intel-win toolset [Open]

> Username: eldiener  
> Created at: 2019-07-15 12:47:07 UTC  
> Updated at: 2021-06-11 01:55:38 UTC  
> Url: https://github.com/boostorg/build/issues/461  

With the changes to the way msvc compiler setup works using Boost created setup scripts, the intel-win toolset now has a single location for creating an msvc-setup.bat setup script for all versions of Intel C++ for Windows and all vc++ compatibilities for each version. This also means that with the default implementation of the end-user undocumented \<rewrite-setup-scripts\> option, when it is not specified in the toolset definition, that the msvc-setup.bat script for intel-win is only regenerated if it does not exist, using different versions of Intel C++ for Windows and/or different vc++ compatibility choices for any particular version will use the same msvc-setup.bat script, causing all subsequent choices after the first to fail compilation. The current workaround to this unfortunate behavior is to specify the \<rewrite-setup-scripts\>false option in the toolset definition for Intel C++ for Windows.  However the creation of the msvc-setup.bat script file, in different subdirectories depending on the intel version and vc++ compatibility, should be the way things should work rather than the current situation of a single subdirectory for all choices.   
  
I have made changes to intel-win.jam in a PR ( https://github.com/boostorg/build/pull/460 ) so that the \<rewrite-setup-scripts\>always choice acts the way it does in msvc.jam to always regenerate msvc-setup.bat. But this is still a flawed workaround to this problem, since two different Intel toolsets can be used at the "same" time regenerating the current single location msvc-setup.bat. The \<rewrite-setup-scripts\>off provides a better current workaround. But since the \<rewrite-setup-scripts\> option is not documented for end-users he would hardly know about it to workaround the current problem. This is why I am bringing up this issue.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:01 UTC  
> Url: https://github.com/boostorg/build/issues/461#issuecomment-859203322  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
