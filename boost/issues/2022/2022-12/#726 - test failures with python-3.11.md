# #726 - test failures with python-3.11 [Closed]

> Username: bdubbs  
> Created at: 2022-12-27 22:50:38 UTC  
> Updated at: 2023-08-13 16:55:37 UTC  
> Closed at: 2023-08-13 16:55:37 UTC  
> Url: https://github.com/boostorg/boost/issues/726  

With Python 3.11.1, the latest stable version, 20 of the boost-1.81.0 regression tests fail.  The build procedure used:  
  
./bootstrap.sh --prefix=/usr --with-python=python3 &&  
./b2 stage -j4 threading=multi link=shared  
  
Then when running tests:  
  
cd tools/build/test  
python3 test_all.py  
  
There are 20 failures like:  
  
core_at_file                      :FAILED  
failure {{{  
Could not open '/tmp/tmpb3hkhs8t/on1n2 .txt'  
}}}  
changes caused by the last build command {{{  
Added files   : ['on1n2 .txt']  
Removed files : []  
Modified files: []  
Touched files : []  
}}}  
STDOUT {{{  
file: on1n2 .txt  
}}}  
STDERR {{{  
}}}  
stacktrace {{{  
at line 636 of /build/boost/boost_1_81_0/tools/build/test/BoostBuild.py (fail_test)  
        from line 590 of /build/boost/boost_1_81_0/tools/build/test/BoostBuild.py (__read)  
        from line 595 of /build/boost/boost_1_81_0/tools/build/test/BoostBuild.py (read)  
        from line 775 of /build/boost/boost_1_81_0/tools/build/test/BoostBuild.py (expect_content)  
        from line 23 of /build/boost/boost_1_81_0/tools/build/test/core_at_file.py (<module>)  
        from line 241 of <frozen importlib._bootstrap> (_call_with_frames_removed)  
        from line 940 of <frozen importlib._bootstrap_external> (exec_module)  
        from line 690 of <frozen importlib._bootstrap> (_load_unlocked)  
        from line 1149 of <frozen importlib._bootstrap> (_find_and_load_unlocked)  
        from line 1178 of <frozen importlib._bootstrap> (_find_and_load)  
        from line 62 of /build/boost/boost_1_81_0/tools/build/test/test_all.py (run_tests)  
        from line 373 of /build/boost/boost_1_81_0/tools/build/test/test_all.py (<module>)  
}}}  
  
With python 3.10 (or python2), all tests pass.

---

## Comment 1

> Username: pyrophorol  
> Created at: 2023-08-13 14:41:25 UTC  
> Updated at: 2023-08-13 14:44:41 UTC  
> Url: https://github.com/boostorg/boost/issues/726#issuecomment-1676380227  

looks to be fixed with https://github.com/boostorg/build/commit/75bfba8a44910a0b6fb07d7eb7d766e2ca0f03ae (though I haven't tried building 1.83.0, but with 1.81.0 if I add that sys.version_info check for adding "U" to openMode those 20 errors don't come anymore)

---

## Comment 2

> Username: bdubbs  
> Created at: 2023-08-13 16:55:37 UTC  
> Url: https://github.com/boostorg/boost/issues/726#issuecomment-1676411950  

Looks good. Closing.
