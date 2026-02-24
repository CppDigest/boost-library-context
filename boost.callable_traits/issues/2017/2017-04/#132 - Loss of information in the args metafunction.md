# #132 - Loss of information in the args metafunction [Closed]

> Username: ldionne  
> Created at: 2017-04-17 02:37:33 UTC  
> Updated at: 2017-11-04 20:40:09 UTC  
> Closed at: 2017-11-04 20:40:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/132  

Issue raised in https://lists.boost.org/Archives/boost/2017/04/234374.php.  
  
Both `void(T)` and `void(T, ...)` result in the same parameter list according to the `args` metafunction. This results in a loss of information, and thus one can't reconstruct the original signature from the parameter list obtained by `args`. One solution would be to use a special type, such as `ct::varargs_t`, to represent C-style varargs.

---

## Comment 1

> Username: badair  
> Created at: 2017-11-04 20:40:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/132#issuecomment-341928294  

In generic code, I think it is more convenient to ignore varargs by default in this metafunction than to require the user to handle this special case. The user can still opt-in to handle this case by using has_varargs.
