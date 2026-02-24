# #11 - Add support for default construction to scope guards [Closed]

> Username: Lastique  
> Created at: 2023-12-01 14:01:08 UTC  
> Updated at: 2024-02-02 00:38:06 UTC  
> Closed at: 2024-02-02 00:38:06 UTC  
> Url: https://github.com/boostorg/scope/issues/11  

From Julien Blanc:  
  
> scope_exit is not default constructible. But it can be released and is movable, so a default constructed scope_exit state makes sense (this is the same as a moved-from scope_exit). I find this a rather strange design choice. What is the rationale behind this?  
  
> I think it does not hurt for scope_fail and scope_exit. We have scope_final for the simple case. I'm asking because i finally added it to the scope_exit-like we use in our code base.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-02-02 00:38:06 UTC  
> Url: https://github.com/boostorg/scope/issues/11#issuecomment-1922575664  

After the following discussion, it looks like default-constructible scope guards would have a very narrow use case because a default-constructed action function must be callable (as it cannot be modified after scope guard construction) and therefore would only have access to the global state. This is rarely sufficient, you normally want to bind local variables and `this` in scope guards.  
  
The original reviewer who requested this feature [admitted](https://lists.boost.org/Archives/boost/2023/12/255570.php) that this would have limited usability, and another reviewer, Andzej Krzemienski, also [noted](https://lists.boost.org/Archives/boost/2023/12/255580.php) that he supports the design without default constructors.  
  
So for now I'm not going to implement this feature until there is a compelling use case and more demand from users.
