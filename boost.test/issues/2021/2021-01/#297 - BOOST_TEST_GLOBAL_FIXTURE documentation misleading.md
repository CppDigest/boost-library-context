# #297 - BOOST_TEST_GLOBAL_FIXTURE documentation misleading [Closed]

> Username: Mondlied  
> Created at: 2021-01-06 21:09:29 UTC  
> Updated at: 2022-03-08 17:53:15 UTC  
> Closed at: 2022-03-08 17:53:15 UTC  
> Url: https://github.com/boostorg/test/issues/297  

The documentation of BOOST_TEST_GLOBAL_FIXTURE makes no mention of a restriction of the class names that can be passed. Passing a class name including the namespace leads to a compiler error though. The documentation should be adjusted to indicate this (or the macro adjusted, assuming this is even possible.)  
  
Documentation document: https://github.com/boostorg/test/blob/develop/doc/test_organization/fixtures.qbk (Section "Global fixture")  
  
Example of the issue  
  
```  
namespace foo {  
    struct Bar {  
    }  
}  
  
BOOST_TEST_GLOBAL_FIXTURE(foo::Bar);  
```  
  
It's pretty simple to work around this issue, but the documentation should mention the restriction on the macro parameters.  
  
Workaround for the above case:  
  
```  
namespace foo {  
     BOOST_TEST_GLOBAL_FIXTURE(Bar);  
}  
```  
  
Alternatively a typedef/using could be used, but this could lead to name conflicts that are avoided by putting `BOOST_TEST_GLOBAL_FIXTURE` inside the namespace.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-03-08 17:53:15 UTC  
> Url: https://github.com/boostorg/test/issues/297#issuecomment-1062046797  

Merged in https://github.com/boostorg/test/pull/337 rev https://github.com/boostorg/test/commit/b52b0ea6429c393485f8ea27ab5edd678823e65a.
