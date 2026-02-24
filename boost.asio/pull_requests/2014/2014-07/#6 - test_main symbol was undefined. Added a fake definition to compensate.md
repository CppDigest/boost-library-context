# #6 test_main symbol was undefined. Added a fake definition to compensate. [Closed]

> Username: ismirlian  
> Created at: 2014-07-17 21:52:42 UTC  
> Updated at: 2014-10-16 09:10:11 UTC  
> Closed at: 2014-10-16 09:10:11 UTC  
> Url: https://github.com/boostorg/asio/pull/6  

The symbol test_main was left undefined for every single one of the asio tests. As a result I would get the following error over and over:  
  
ld: 0711-317 ERROR: Undefined symbol: .test_main(int, char**)  
  
The weird thing is that the tests did not appear to be using this symbol at all, yet it was still preserved. To compensate I added an empty definition of test_main. This allowed all of the tests in the suite to run. Most of the tests passed except for two, but for unrelated issues that I still need to look at.  
  
It is possible that the semantics of the AIX linker and the way that GCC interacts with the linker are causing extra symbols to be preserved. There might need to be in the future more work that needs to be done for either, but for now I think this is a good solution.

---

## Comment 1

> Username: ismirlian  
> Created_at: 2014-08-01 19:01:34 UTC  
> Url: https://github.com/boostorg/asio/pull/6#issuecomment-50922320  

Hey  chriskohlhoff I saw that you added something similar to what I wrote for the unit test workaround on AIX around a week ago. Is it ok for me to close this pull request now?

---
