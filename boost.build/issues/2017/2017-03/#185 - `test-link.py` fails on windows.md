# #185 - `test/link.py` fails on windows [Closed]

> Username: refack  
> Created at: 2017-03-19 20:27:05 UTC  
> Updated at: 2017-04-26 20:54:33 UTC  
> Closed at: 2017-04-26 20:54:33 UTC  
> Url: https://github.com/boostorg/build/issues/185  

```  
[15:27:09.68] D:\code\3party\build>src\engine\bin.ntx86\bjam -d3 -sBOOST_BUILD_PATH="test/.." -d0 --quiet dir1-link dir2-link toolset=msvc --test-config="test\test-config.jam" --ignore-toolset-requirements  
notice: could not find main target dir1-link  
notice: assuming it is a name of file to create.  
notice: could not find main target dir2-link  
notice: assuming it is a name of file to create.  
make    --      <e>dir1-link  
make    --      <e>dir1-link  
bind    --      <e>dir1-link: dir1-link  
time    --      <e>dir1-link: missing  
don't know how to make <e>dir1-link  
made+   nofind  <e>dir1-link  
make    --      <e>dir2-link  
make    --      <e>dir2-link  
bind    --      <e>dir2-link: dir2-link  
time    --      <e>dir2-link: missing  
don't know how to make <e>dir2-link  
made+   nofind  <e>dir2-link  
...found 2 targets...  
...can't find 2 targets...  
```  
output of run -d9:  
[fail9.txt](https://github.com/boostorg/build/files/853537/fail9.txt)

---

## Comment 1

> Username: KindDragon  
> Created at: 2017-03-20 03:15:56 UTC  
> Url: https://github.com/boostorg/build/issues/185#issuecomment-287671668  

Different error for me on AppVeyor:  
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

---

## Comment 2

> Username: swatanabe  
> Created at: 2017-03-29 13:26:14 UTC  
> Url: https://github.com/boostorg/build/issues/185#issuecomment-290088930  

AMDG  
  
  This problem is caused by Python's stat function not  
behaving as documented for symlinks.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: KindDragon  
> Created at: 2017-04-04 22:05:32 UTC  
> Url: https://github.com/boostorg/build/issues/185#issuecomment-291645979  

@swatanabe Can you add where that `os.stat` call (or even stacktrace for that call) and how it should behave?

---

## Comment 4

> Username: swatanabe  
> Created at: 2017-04-05 14:32:47 UTC  
> Url: https://github.com/boostorg/build/issues/185#issuecomment-291880342  

AMDG  
  
On 04/04/2017 04:05 PM, Arkady Shapkin wrote:  
> @swatanabe Can you add where that `os.stat` call (or even stacktrace for that call)  
  
It's in tree.py, when checking for changes made  
by the build.  
  
> and how it should behave?  
>   
  
The actual behavior is that of lstat.  i.e. it  
stats the link itself, not the link target.  
  
The easiest fix is just to change the test to  
add ignore_update for these files (which I  
think is really the correct thing anyway).  
I'll take care of this shortly.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: KindDragon  
> Created at: 2017-04-05 15:11:13 UTC  
> Url: https://github.com/boostorg/build/issues/185#issuecomment-291892980  

Thank you
