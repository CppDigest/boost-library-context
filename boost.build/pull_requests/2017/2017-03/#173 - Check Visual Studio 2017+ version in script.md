# #173 Check Visual Studio 2017+ version in script [Merged]

> Username: KindDragon  
> Created at: 2017-03-13 15:40:40 UTC  
> Updated at: 2021-10-02 22:18:45 UTC  
> Merged at: 2017-03-19 16:32:22 UTC  
> Closed at: 2017-03-19 16:32:22 UTC  
> Url: https://github.com/boostorg/build/pull/173  

CSharp script simplified  
Boost.Build tests fixed for 64bit Windows

---

## Comment 1

> Username: KindDragon  
> Created_at: 2017-03-14 17:01:06 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-286489038  

Toolset removed from AppVeyour parameter because `msvc` toolset can be used only for Visual Studio 6

---

## Comment 2

> Username: KindDragon  
> Created_at: 2017-03-18 17:05:28 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287559750  

@grafikrobot Hi. I finally fixed it for AppVeyor, but some test failing on Windows

---

## Review 3 [Commented]

> Username: grafikrobot  
> Created_at: 2017-03-19 03:04:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/173#pullrequestreview-27739121  

📁 appveyor.yml

```diff
  17 |+ build_script:
  18 |+ - ps: |
  19 |+     & .\bootstrap.bat
```

> Username: grafikrobot  
> Created_at: 2017-03-19 03:04:43 UTC  
> Updated_at: 2017-03-19 11:51:17 UTC  
> Url: https://github.com/boostorg/build/pull/173#discussion_r106796860  

I know it's redundant.. But I would prefer if we also run build.bat for testing. It gives the compile out which contains warnings. Which would be good to see here as it then has a chance of getting fixed.

> Username: KindDragon  
> Created_at: 2017-03-19 03:21:06 UTC  
> Updated_at: 2017-03-19 11:51:17 UTC  
> Url: https://github.com/boostorg/build/pull/173#discussion_r106797087  

but you can check bootstrap.log in artifacts for that https://ci.appveyor.com/project/boostorg/build/build/1.0.94/job/ivfq7ou4nd5yu95n/artifacts  
  
This is not enough?

> Username: grafikrobot  
> Created_at: 2017-03-19 13:38:01 UTC  
> Url: https://github.com/boostorg/build/pull/173#discussion_r106806727  

I didn't see that before.. It's OK as is for now.


---

## Comment 4

> Username: KindDragon  
> Created_at: 2017-03-19 05:46:28 UTC  
> Updated_at: 2017-03-19 07:32:40 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287596202  

`link` test still failing:  
``` xml  
<test-log library="build" test-name="link" test-type="run" toolset="msvc" test-program="tools/build/v2/test/link.py" target-directory="boost/bin.v2/boost.build.tests/msvc/link">  
<run result="fail">  
failure {{{  
Unexpected changes found  
}}}  
unexpected changes {{{  
Added files   : []  
Removed files : []  
Modified files: []  
Touched files : ['include/file1.h']  
  
}}}  
changes caused by the last build command {{{  
Added files   : ['include/file2.h']  
Removed files : []  
Modified files: []  
Touched files : ['bin/config.log', 'bin/project-cache.jam', 'include/file1.h']  
  
}}}  
STDOUT {{{  
Performing configuration checks  
  
    - symlinks supported       : yes (cached)  
  
}}}  
STDERR {{{  
  
}}}  
stacktrace {{{  
at line 597 of C:\projects\build\test\BoostBuild.py (fail_test)  
	from line 722 of C:\projects\build\test\BoostBuild.py (expect_nothing_more)  
	from line 99 of C:\projects\build\test\link.py (test_merge_existing)  
	from line 106 of C:\projects\build\test\link.py (test_merge_existing_all)  
	from line 328 of C:\projects\build\test\link.py (&lt;module&gt;)  
	from line 59 of test_all.py (run_tests)  
	from line 314 of test_all.py (&lt;module&gt;)  
  
}}}  
  
</run>  
</test-log>  
```  
  
EDIT: Pass on my local machine

---

## Comment 5

> Username: KindDragon  
> Created_at: 2017-03-19 07:11:15 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287599213  

This PR should fix also this issue https://svn.boost.org/trac/boost/ticket/10782

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2017-03-19 13:41:01 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287617433  

This all looks good. And I'd really like to merge this. But the 30 minute vs2017 run time worries me. Does that slowdown happen for regular users when they run bootstrap locally? Or is it a issue with Appveyor only?

---

## Comment 7

> Username: KindDragon  
> Created_at: 2017-03-19 15:07:53 UTC  
> Updated_at: 2017-03-19 15:08:09 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287622875  

No, this slowdown only because of tests. Tests run Powershell script many times and because of this slowdown happen.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2017-03-19 16:33:29 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287628691  

Thanks for the rewrite of this.. Much happier now that the VS checking code is minimized.

---

## Comment 9

> Username: refack  
> Created_at: 2017-03-19 16:42:59 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287629392  

You just killed my beutifull #184 😢

---

## Comment 10

> Username: refack  
> Created_at: 2017-03-19 16:50:04 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287629860  

> No, this slowdown only because of tests. Tests run Powershell script many times and because of this slowdown happen.  
  
old `build.bat` enters a bottomless recursion, that's why there's 320 calls to `powershell`. Easy to solve with a guard clause  
```  
IF DEFINED EXPENSIVE_POWERSHELL goto :somewhere_else  
SET EXPENSIVE_POWERSHELL=1  
```

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2017-03-19 16:50:09 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287629865  

@refack Sorry about that.. But I'd rather take something that works now. Than something in-progress.

---

## Comment 12

> Username: refack  
> Created_at: 2017-03-19 18:17:54 UTC  
> Updated_at: 2017-03-19 18:18:06 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287635814  

@KindDragon , @grafikrobot if you update the VS COM tools or their use, please /cc me so I can keep track and maybe push upstream.  
Thanks.

---

## Comment 13

> Username: refack  
> Created_at: 2017-03-19 18:36:27 UTC  
> Updated_at: 2017-03-19 20:16:32 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287637034  

> No, this slowdown only because of tests. Tests run Powershell script many times and because of this slowdown happen.  
  
Apparently there's recursion in the `jam` files as well. I added a guard in both in #179

---

## Comment 14

> Username: refack  
> Created_at: 2017-03-19 20:00:17 UTC  
> Url: https://github.com/boostorg/build/pull/173#issuecomment-287643075  

#179 integrates new tools version and passes tests in reasonable time (~6m)

---
