# #173 - Typo in the from_current_exception example [Closed]

> Username: d4nnie  
> Created at: 2024-07-24 16:59:29 UTC  
> Updated at: 2024-09-12 07:38:16 UTC  
> Closed at: 2024-09-12 07:38:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/173  

Example from the [docs]( https://www.boost.org/doc/libs/1_85_0/doc/html/stacktrace/getting_started.html#stacktrace.getting_started.stacktrace_from_arbitrary_except) fails to [compile](https://godbolt.org/z/T4W17h87b):  
```  
<source>: In function 'int main()':  
<source>:14:62: error: 'from_current_exception' is not a member of 'boost::stacktrace'  
   14 |     boost::stacktrace::stacktrace trace = boost::stacktrace::from_current_exception();  // <---  
      |                                                              ^~~~~~~~~~~~~~~~~~~~~~  
Compiler returned: 1  
```  
  
Obviously, as said in the docs themselves, the full name for `from_current_exception` will be `boost::stacktrace::stacktrace::from_current_exception`.

---

## Comment 1

> Username: NoumanAmir657  
> Created at: 2024-08-10 09:21:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/173#issuecomment-2280559367  

Can I fix this?

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-09-12 07:38:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/173#issuecomment-2345493152  

Fixed in https://github.com/boostorg/stacktrace/pull/181
