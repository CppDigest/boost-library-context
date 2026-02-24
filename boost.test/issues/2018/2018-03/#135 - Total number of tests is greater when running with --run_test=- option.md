# #135 - Total number of tests is greater when running with --run_test=* option [Closed]

> Username: k15tfu  
> Created at: 2018-03-23 17:00:47 UTC  
> Updated at: 2018-03-24 08:56:31 UTC  
> Closed at: 2018-03-24 08:56:31 UTC  
> Url: https://github.com/boostorg/test/issues/135  

I have a Boost.Test project with 8772 test cases but today I spent a few hours trying to find ONE test which is not run on our build server because when I run it locally with --run_test=* the number of tests is greater by 1.  
  
Why?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-03-23 19:02:21 UTC  
> Url: https://github.com/boostorg/test/issues/135#issuecomment-375768334  

You may output all tests in your local machine and the test running by just running ``--list_content`` instead, and outputting to a file. Use a diff for comparing those 2 files afterwards, and let me know about your findings.

---

## Comment 2

> Username: k15tfu  
> Created at: 2018-03-23 21:07:41 UTC  
> Url: https://github.com/boostorg/test/issues/135#issuecomment-375798717  

They are the same O_o  
  
```  
...\x64.test.dbg>BoostTests.exe --run_test=*  
Running 8773 test cases...  
^C  
...\x64.test.dbg>BoostTests.exe --run_test=* --list_content 1>with.log 2>&1  
...\x64.test.dbg>BoostTests.exe  
Running 8772 test cases...  
^C  
...\x64.test.dbg>BoostTests.exe --list_content 1>without.log 2>&1  
.../x64.test.dbg$ diff with.log without.log  
.../x64.test.dbg$  
```

---

## Comment 3

> Username: k15tfu  
> Created at: 2018-03-23 21:11:26 UTC  
> Url: https://github.com/boostorg/test/issues/135#issuecomment-375799547  

Ahh. --list_content ignores --run_test filter.

---

## Comment 4

> Username: k15tfu  
> Created at: 2018-03-23 21:20:31 UTC  
> Url: https://github.com/boostorg/test/issues/135#issuecomment-375801534  

I have checked that there are really 8773 test cases in log files.

---

## Comment 5

> Username: k15tfu  
> Created at: 2018-03-23 22:52:38 UTC  
> Url: https://github.com/boostorg/test/issues/135#issuecomment-375818926  

Okay, I found. --run_test=* also runs the disabled test cases.  
  
Probably it's an expected behavior.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-03-24 08:56:31 UTC  
> Url: https://github.com/boostorg/test/issues/135#issuecomment-375857328  

I'll add a comment in the doc. Thanks.
