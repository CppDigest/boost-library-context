# #103 - queue_interprocess_test fails with bad_alloc after recent Interprocess changes [Closed]

> Username: pdimov  
> Created at: 2024-09-24 13:19:55 UTC  
> Updated at: 2024-09-25 00:55:52 UTC  
> Closed at: 2024-09-25 00:55:52 UTC  
> Url: https://github.com/boostorg/lockfree/issues/103  

As discussed here  
  
https://github.com/boostorg/interprocess/commit/cbeb5b5ea9d363484306b33c0a0ad304914ed3f3  
  
`queue_interprocess_test` now fails with  
  
```  
          Start  764: run-boost_lockfree-queue_interprocess_test  
 704/1970 Test  #764: run-boost_lockfree-queue_interprocess_test ............................................Subprocess aborted***Exception:   0.08 sec  
terminate called after throwing an instance of 'boost::interprocess::bad_alloc'  
  what():  boost::interprocess::bad_alloc  
```  
(https://github.com/boostorg/boost/actions/runs/10976479575/job/30477262572)  
  
Ion says that the fix is to increase the hardcoded 262144 here  
  
https://github.com/boostorg/lockfree/blob/boost-1.86.0/test/queue_interprocess_test.cpp#L30  
  
slightly (1% is enough.)

---

## Comment 1

> Username: timblechmann  
> Created at: 2024-09-24 13:46:31 UTC  
> Url: https://github.com/boostorg/lockfree/issues/103#issuecomment-2371328466  

@pdimov thanks for the heads-up. i checked the interprocess discussion a few days ago, but the web interface didn't show anything. fix is coming
