# #119 - Fix the preconditoins in the library docs [Open]

> Username: akrzemi1  
> Created at: 2023-09-24 17:38:48 UTC  
> Updated at: 2023-09-24 17:38:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/119  

Promise and Generator state that on `get()` exception is thrown if not ready:  
https://klemens.dev/async/reference.html#generator-outline  
https://klemens.dev/async/reference.html#promise-outline  
  
Are you consciously giving such guarantee? I would expect that to be a precondition on `get()`: call it only if `ready() == true`.  
  
But you should also explain what it means that a result is ready. For instance, when the coroutine threw an exception and it has not been inspected yet by the awaiter, is "result ready"?  
  
The throwing of `std::logic_error` is a poor design choice (also employed in the Standard Library) as it sends a mixed message. Is it a programmer bug when I do that? Or is it something that I can expect deterministic results for?  
  
I would call it a precondition and use `BOOST_ASSERT` in the implementation, and if the user wishes to throw `std::logic_error`, let them install an assertion handler.  
  
For other functions like `async::thread::detach()` the library doesn't specify at all what happens if I call `.detach()` twice. Does `.detach()` have a precondition that `this->joinable() == true`?  
  
Also, what happens when I `co_await` ona `promise` `p` whose `operator bool` returns `false`? Is it a contract violation?  
  
What is the precondition on `co_await p`? is it `p.ready()` or `p. operator bool()`? How are they different?  
  
I should be able to figure out these answers from the documentaiton.
