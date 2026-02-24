# #698 - test target fails: /bin/sh: test/test_all.py: Permission denied [Open]

> Username: yurivict  
> Created at: 2020-12-30 18:06:55 UTC  
> Updated at: 2021-05-29 16:23:02 UTC  
> Url: https://github.com/boostorg/build/issues/698  

I run  
```  
./b2 toolset=clang test --prefix=${STAGEDIR}${PREFIX}  
```  
and it fails:  
```  
cd /usr/ports/devel/boost_build/work/build-29baa79 && ./b2 toolset=clang test --prefix=/usr/ports/devel/boost_build/work/stage/usr/local  
...found 413 targets...  
...updating 2 targets...  
capture-output .build/test/test_all.test/clang-linux-10.0.1/test_all  
====== BEGIN OUTPUT ======  
/bin/sh: test/test_all.py: Permission denied  
  
EXIT STATUS: 126  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:32 UTC  
> Url: https://github.com/boostorg/build/issues/698#issuecomment-850859619  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
