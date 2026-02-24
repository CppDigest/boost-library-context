# #325 Apply catch_system_errors option to m_aux_em [Open]

> Username: rdoeffinger  
> Created at: 2022-01-31 18:17:10 UTC  
> Updated at: 2024-05-31 08:43:29 UTC  
> Url: https://github.com/boostorg/test/pull/325  

That way it is respected also in init_unit_test_suite.  
This allows handling these in some other way even in  
that function, and it also allows to install custom  
signal handling from within init_unit_test_suite,  
instead of the workaround of implementing a custom  
main() function.

---
