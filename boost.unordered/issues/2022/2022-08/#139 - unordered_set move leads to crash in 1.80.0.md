# #139 - unordered_set move leads to crash in 1.80.0 [Closed]

> Username: ergpudb  
> Created at: 2022-08-16 02:55:18 UTC  
> Updated at: 2022-11-01 20:35:01 UTC  
> Closed at: 2022-11-01 20:35:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/139  

This code segfaults in 1.80.0 but not 1.79.0 (nor using std::unordered_set):  
  
```  
    boost::unordered_set<std::string> a;  
    boost::unordered_set<std::string> b = std::move(a);  
    boost::unordered_set<std::string> c = std::move(a);  
    c.clear(); // <-- segfault  
```  
  
Based on the standard, the first move should leave `a` in a "valid but unspecified state"; since `a` should still be valid, moving from it a  second time presumably should not corrupt `c` (even if the contents of `c` are technically unspecified afterward).  
  
Note: it appears that unordered_map also exhibits the same behavior.

---

## Comment 1

> Username: ergpudb  
> Created at: 2022-08-16 02:58:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/139#issuecomment-1216087058  

Even simpler (no double move):  
  
```  
    boost::unordered_set<std::string> a;  
    boost::unordered_set<std::string> b = std::move(a);  
    a.clear(); // <-- segfault  
```

---

## Comment 2

> Username: cmazakas  
> Created at: 2022-08-16 15:38:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/139#issuecomment-1216810072  

Thank you for the bug report!  
  
I've replicated this issue locally and am aware of the cause.  
  
I'm working on the fix.

---

## Comment 3

> Username: cmazakas  
> Created at: 2022-11-01 20:35:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/139#issuecomment-1299106064  

@ergpudb this fix will be landing in 1.81. In the interim, I'm going to close the issue.
