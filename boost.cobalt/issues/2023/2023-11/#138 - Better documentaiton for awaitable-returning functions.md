# #138 - Better documentaiton for awaitable-returning functions [Open]

> Username: akrzemi1  
> Created at: 2023-11-09 02:40:20 UTC  
> Updated at: 2023-11-09 02:51:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/138  

The docs for `race` have, among others, the following dclaration:  
  
```c++  
// Variadic race with the default random number generator  
template<asio::cancellation_type Ct = asio::cancellation_type::all, awaitable... Promise>  
awaitable race(Promise && ... p);  
```  
  
(1) Why are you using term `Promise`? Are any other awaitables than `cobalt::promise<>` allowed?  
  
(2) There is a sentence "This is supported by [promise](https://www.boost.org/doc/libs/develop/libs/cobalt/doc/html/index.html#promise), [generator](https://www.boost.org/doc/libs/develop/libs/cobalt/doc/html/index.html#generator) and [gather](https://www.boost.org/doc/libs/develop/libs/cobalt/doc/html/index.html#gather)". *What* is supported? `race()`? interruption? Does `race` only work with `promise` and `generator`? What is `gather` doing in this sentence?  
  
(3) In the function declaration is `Promise && ...` supposed to mean an rvalue reference or a perfect-forwarding (universal) reference? The declaration implies that it is rvalue reference, and you can only move into this function. But in that case the following cannot work:  
  
```c++  
cobalt::promise<void> do_wait()  
{  
  auto t1 = task1();  
  auto t2 = task2();  
  co_await cobalt::race(t1, t2);   
  co_await cobalt::race(t1, t2);   
}  
```  
  
(4) I expect a section:  
  
*Mandates:* `sizeof...(Promise) > 0`.  
  
This guarantees that the program is ill-formed when you have a zero-size parameter pack.  
  
(5) I expect a section:  
  
*Preconditions:* All awaitables in `p` can be `co_await`ed.  
  
(6) I expection a section:  
  
*Returns:* An `awaitable` `A` where `resume_type<A>` is `variant2::variant<normalzed_type<resume_type<Promise>>...>`.  
  
And somewhere else you should have the definition of `resume_type` (retun type of `await_resume()`) and `normalzed_type` (identity function, except that `void` is converted to `monostate`.  
  
(7) I expect a section specific to functions returning awaitables, desribing what happens when you `co_await` on the return object:  
*Await effect:* Returns the result of `co_await a` where `a` is one arbitrarily selected element of parameter pack `p`. The remaining awaitables in `p` can still be `co_await`ed.  
  
(8) I expect a section:  
  
*Throws:* Nothing.  
  
Or does this function throw anything?
