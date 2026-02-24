# #539 - BoostBuild.Tester not capturing b2 status correctly for -fx [Closed]

> Username: mloskot  
> Created at: 2020-02-23 00:19:30 UTC  
> Updated at: 2020-03-02 20:03:39 UTC  
> Closed at: 2020-03-02 20:03:39 UTC  
> Url: https://github.com/boostorg/build/issues/539  

After submission of the #538, `b2` fix to correctly report `1` instead of `0` for Jam syntax errors, I realised something is still fishy.  
  
1. Why none of the tests addressing syntax errors did not require any changes after the fix?  
2. Better, why those tests which actually expected `status=1` had been passing at all, for long time?  
  
------  
  
There are two common ways of how tests generate Jam scripts and the `BoostBuild.Tester` invokes `b2` for them:  
  
1. https://github.com/boostorg/build/blob/f951fb15596f0865f58adffc183ed5de9defd100/test/exit_status.py#L13-L24  
2. https://github.com/boostorg/build/blob/f951fb15596f0865f58adffc183ed5de9defd100/test/core_source_line_tracking.py#L39-L44  
  
To paraphrase my question No 2 above: why the `core_source_line_tracking.py` had been reporting `PASSED` without the #538 fix?  
  
------  
  
So, I translated both types of test cases from Python to plain `b2` command line (as `BoostBuild.Tester` generates them) and passed a Jam script with broken syntax.  
  
Then, I used `b2` build **without** the #538 fix:  
  
1. ```shell  
    mloskot@bionic:with-jamroot$ pwd  
    /mnt/d/boost.wsl/tools/build/test/with-jamroot  
  
    mloskot@bionic:with-jamroot$ cat jamroot.jam  
    exe h : h.cpp  
  
    mloskot@bionic:with-jamroot$ /mnt/d/boost.wsl/tools/build/test/../src/engine/b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc --test-config="/mnt/d/boost.wsl/tools/build/test/test-config.jam"  
    jamroot.jam:1: syntax error at EOF  
    ...found 1 target...  
  
    mloskot@bionic:with-jamroot$ echo $?  
    0  
    ```  
  
  
2. ```shell  
    mloskot@bionic:with-file$ pwd  
    /mnt/d/boost.wsl/tools/build/test/with-file  
  
    mloskot@bionic:with-file$ cat file.jam  
    exe h : h.cpp  
  
    mloskot@bionic:with-file$ /mnt/d/boost.wsl/tools/build/test/../src/engine/b2 -sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.." -j1 toolset=gcc --test-config="/mnt/d/boost.wsl/tools/build/test/test-config.jam" -ffile.jam  
    file.jam:1: syntax error at EOF  
    don't know how to make toolset=gcc  
    ...found 1 target...  
    ...can't find 1 target...  
  
    mloskot@bionic:with-file$ echo $?  
    1  
    ```  
  
Critical here is the difference in the `b2` outputs, that is, for the `b2 -ffile.jam` case, there seem to be actually two outputs bundled in one:  
  
```  
file.jam:1: syntax error at EOF  
```  
  
which presumably reported status `0` - remember, the #538 fix is not there - and this  
  
```  
...found 1 target...  
...can't find 1 target...  
```  
  
which overrides the `status=0` with `status=1` and makes `BoostBuild.Tester` all happy to report `PASSED` as it expects `status=1`  
  
```python  
t.run_build_system(["-ffile.jam"], status=1)  
```  
  
Poor `BoostBuild.Tester` is not aware it is being cheated :)  
  
------  
  
To prove my theory that the tests runner is actually not *seeing* the results it thinks it does, I created new test `/mnt/d/boost.wsl/tools/build/test/core_syntax_error_exit_status.py`, modified the tests list in the `test_all.py` to run only test scripts of my interest, and I run equivalents of the two cases above:  
  
1. `b2` with `-ffile.jam` case  
  
    ```shell  
    $ cat core_syntax_error_exit_status.py  
    #!/usr/bin/python  
    import BoostBuild  
    t = BoostBuild.Tester()  
    t.write("file.jam", """  
    exe hello : hello.cpp  
  
    """)  
    t.run_build_system(["-ffile.jam"], status=1)  
    t.cleanup()  
  
    $ python test_all.py gcc  
    core_source_line_tracking     :PASSED  
    core_syntax_error_exit_status :PASSED  
  
            === Test summary ===  
            PASS: 2  
            FAIL: 0  
    ```  
  
2. `b2` with (implicit) `jamroot.jam`  
  
    ```shell  
    $ cat core_syntax_error_exit_status.py  
    #!/usr/bin/python  
    import BoostBuild  
    t = BoostBuild.Tester()  
    t.write("jamroot.jam", """  
    exe hello : hello.cpp  
  
    """)  
    t.run_build_system(status=1)  
    t.cleanup()  
  
    $ python test_all.py gcc  
    core_source_line_tracking     :PASSED  
    core_syntax_error_exit_status :FAILED  
    failure {{{  
    "['/mnt/d/boost.wsl/tools/build/test/../src/engine/b2', '-sBOOST_BUILD_PATH="/mnt/d/boost.wsl/tools/build/test/.."', '-j1', 'toolset=gcc', '--test-config="/mnt/d/boost.wsl/tools/build/test/test-config.jam"']" returned 0 (expected 1)  
    }}}  
    reason {{{  
    unexpected status returned by bjam  
    }}}  
    changes caused by the last build command {{{  
    Added files   : ['bin/config.log', 'bin/project-cache.jam']  
    Removed files : []  
    Modified files: []  
    Touched files : []  
  
    }}}  
    STDOUT {{{  
    ...found 1 target...  
  
    }}}  
    STDERR {{{  
  
    }}}  
    stacktrace {{{  
    at line 608 of /mnt/d/boost.wsl/tools/build/test/BoostBuild.py (fail_test)  
            from line 497 of /mnt/d/boost.wsl/tools/build/test/BoostBuild.py (run_build_system)  
            from line 8 of /mnt/d/boost.wsl/tools/build/test/core_syntax_error_exit_status.py (<module>)  
            from line 62 of test_all.py (run_tests)  
            from line 170 of test_all.py (<module>)  
  
    }}}  
  
            === Test summary ===  
            PASS: 1  
            FAIL: 1  
    ```  
  
    Finally, with the #538 fix,   
  
    ```shell  
    $ python test_all.py gcc  
    core_syntax_error_exit_status :PASSED  
    core_source_line_tracking     :PASSED  
  
            === Test summary ===  
            PASS: 2  
            FAIL: 0  
    ```  
  
To summarise, the `status` **aliasing** in case of the `-ffile.jam` invocation kept the bug uncovered.  
  
It seems worth to add the `core_source_line_tracking.py` script to the tests suite.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-02-24 19:01:24 UTC  
> Url: https://github.com/boostorg/build/issues/539#issuecomment-590494745  

1. Yes, adding a test would be good.  
2. After thinking about it we need to make sure syntax errors are immediate failed exits. We can't guarantee any reasonable state for anything after the error. Hence we are lying to users.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-02-26 23:50:17 UTC  
> Url: https://github.com/boostorg/build/issues/539#issuecomment-591703388  

@grafikrobot I proposed a minimal fix in #540 As far as my tests and understanding go, it does seem to improve things.
