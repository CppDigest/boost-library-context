# #69 - Few best practice questions [Closed]

> Username: arcrc  
> Created at: 2023-12-08 18:53:16 UTC  
> Updated at: 2024-01-11 02:16:50 UTC  
> Closed at: 2024-01-11 02:16:50 UTC  
> Url: https://github.com/boostorg/leaf/issues/69  

I hope the examples are self-explanatory :).  
  
1. Propagating errors outside of for loop: https://godbolt.org/z/ezcdGPKzs  
2. How to propagate AdditionalError from inner context (that does not have a suitable handler) to the outer context: https://godbolt.org/z/jT4GxbMEG  
I could not get this to work. The problem is  `return {}`; in       
```  
[] (const CalcError& calc_error) -> leaf::result<OutputResult>  
{  
        std::cerr << "Calc Error!" << std::endl;  
        return {};  
}  
```  
Is it possible to check if any other errors are present in that scope?  
  
3. Bonus question: Would it be possible to extend on_error functionality to take lambda that is executed once an error occurs? Imagine a function that  BOOST_ASSIGN several error reporting functions. If any of them reports an error some cleanup is to be executed. One could try to solve it by "on loading" RequestCleanup error that is then handled in the top level handler. The disadvantage is that the top level hander has to have access to the cleanup functionality.   
To go away with that, on load could take a a lambda with RequestCleanup reference parameter, but then the hander has to remember to allocate storage by specifying the corresponding handler.  
If that's not that clear I could also provide an example.

---

## Comment 1

> Username: zajo  
> Created at: 2023-12-10 00:55:16 UTC  
> Url: https://github.com/boostorg/leaf/issues/69#issuecomment-1848800097  

1. In order to check for success/failure, it is sufficient to inspect the returned result<T>, but to access the actual error objects in case of a failure, you must use try_handle_some, try_handle_all or, if using exceptions, try_catch. See https://godbolt.org/z/7YGo74zeP. Do let me know if I didn't understand the intended behavior correctly.  
  
2. At line 46, return result.error() may or may not return an error, depends on whether the last call succeeded or not. Also it is not clear to me which failure AdditionalError is supposed to be associated with, as there may be many. I suspect you just want to return a new_error(AdditionalError{}).  
  
3. First, lambdas you pass to on_error don't execute when an error occurs, they execute at the time the on_error object is destroyed iff an error is currently being propagated (as opposed to merely having occurred). Secondly, as you point out, on_error does not have its own context/storage. In principle it is possible to have something like this, which provides storage, but we still have the problem of work being done only in the destructor of that object, rather than at the time errors occur.

---

## Comment 2

> Username: arcrc  
> Created at: 2023-12-10 18:40:15 UTC  
> Updated at: 2023-12-10 18:42:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/69#issuecomment-1849046189  

Regarding 1.  
  
`collectResults` acts as an intermediate layer - it neither handles nor creates any additional errors, but should rather transport ones that have been created iff no valid result is obtained. If no return statement in `for` loop is executed, I would like to propagate further errors that have storage and its `thread_local` pointer is not `nullptr`.   
  
However, I think I would need to know their `error_id` which is not available in that scope. What I could do is to return `leaf::current_error()`, but that will only transport the last error that has occurred (I think).   
  
When I think more about it, it seems that the approach I want to achieve has some downsides, e.g. if some additional error of a new type is created in the middle of the loop, then it would be propagated too, but it might be difficult to infer when it occurred. But I think it is not possible anyway to return all existing `error_id`s, right?   
  
As I stated in the comment, usually the elements of the vector are iterated, and the result is deterministic. If I would like to **propagate errors only from the last iteration** would the best option be to use `return getOutputResult(vec.back());` after `for` body (or check condition in for loop to determine if it is the last iteration, but I do not like that)?  
  
Regarding 2.  
  
Imagine `collectResults` fails. `CalcError` and `AdditionalError` and initialized. Handler in `doWork` only knows how to handle `CalcError` and is not aware of any other low level error types.  
If any other errors occurred I would like to give an outer handler a chance to handle them.   
```  
    [] (const CalcError& calc_error) -> leaf::result<OutputResult>  
    {  
        std::cerr << "Calc Error!" << std::endl;  
        return {}; //This should be executed only if no other errors than CalcError are present. Is it possible to propagate other existing errors?  
    }   
```  
  
Regarding 3.  
  
https://godbolt.org/z/TY7vrMf54  
One of the use cases is when all the calls to functions are wrapped in `BOOST_LEAF_CHECK`. When any of the function calls fails, then the error is to be reported and all local variables are going to be destroyed. If there are local variables defined after `load`, they will be destroyed first, but I would not expect any side effects. Therefore I would assume that from the user perspective (in most cases with trivial destructor of local variables) `on_error` will be executed as if it happened right after an error to be reported is created.  
If there are some issues, I think it would be the same as the case when `on_error` takes lambda with non-const error type reference param.  
  
If you think that such a functionality would be beneficial I would be very happy to contribute and work on it!

---

## Comment 3

> Username: zajo  
> Created at: 2023-12-10 19:17:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/69#issuecomment-1849055987  

Let's focus on 1 for now.  
  
The solution I proposed (https://godbolt.org/z/7YGo74zeP), collects the CalcError objects from each failed attempt to produce a valid result. If no valid result could be produced, it sends the collected CalcError objects wrapped in a GivingUpAfterMultipleRetriesError.  
  
If there are other known error object types, they can be organized and collected similarly, either by adding more error handlers inside the for loop, or changing the existing handler to take all known error object types by const pointer, then collecting what is available.  
  
If you want to be able to collect all error objects, even of types that are not known in the context of the for loop, there is no good solution for that at this time.  
  
Let me know if this helps, and then we'll move to 2 and 3.

---

## Comment 4

> Username: arcrc  
> Created at: 2023-12-10 21:04:44 UTC  
> Updated at: 2023-12-10 21:14:05 UTC  
> Url: https://github.com/boostorg/leaf/issues/69#issuecomment-1849084391  

Thanks for the quick feedback.  
  
It just shows how important is it to decide upfront what are the errors, what is to be propagated further and how to map errors between different layers.  
  
If all the specific errors (that occurred in the for loop) are to be propagated then your solution is probably the best. On the other hand, it couples implementation with the lower level function. Whenever lower level function adds a new error it should rather be added to the handler. Otherwise in certain scenarios, it might happen that instead of an important error, `GivingUpAfterMultipleRetriesError` with an empty vector is reported.  
  
Best practice for this case (from my point of view) would be:   
decide upfront what errors are to be propagated. Propagating errors from the last failed attempt is easy (`return getOutputResult(vec.back())` - no handler needed). When mapping or collecting of errors is required be prepared for additional maintenance overhead (especially when the new error types are added). In case various error handling mechanisms are needed try to implement a wrapper that injects handlers as a dependency.  
  
I think we can focus now on 2 and 3.

---

## Comment 5

> Username: zajo  
> Created at: 2024-01-09 05:16:33 UTC  
> Url: https://github.com/boostorg/leaf/issues/69#issuecomment-1882426802  

On 2:  
  
```  
return {}; //This should be executed only if no other errors than CalcError are present. Is it possible to propagate other existing errors?  
```  
  
The answer is no. Logically, the error objects of types other than `CalcError` are not different failures, they are error objects associated with the same failure that `CalcError` is associated with. If the failure can be handled based only on the `CalcError` object, then it is handled, otherwise not.  
  
On 3, I've made a change which is now merged into `develop`, which allows for `void` functions with no arguments to be used with `on_error`.
