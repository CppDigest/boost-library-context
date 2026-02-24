# #421  The modifications remove unnecessary uses of std::move() for improve… [Open]

> Username: ArielSilver  
> Created at: 2023-07-02 06:20:18 UTC  
> Updated at: 2023-07-02 10:28:24 UTC  
> Url: https://github.com/boostorg/asio/pull/421  

### This pull request addresses two modifications in the `promise_impl` class within the `experimental/impl/promise.hpp` file:  
that there is indeed an unnecessary use of std::move() in the apply_impl function. Specifically, the line f(std::get<Idx>(std::move(result_type))...); could be modified to remove the unnecessary std::move().  
  
Here's the corrected version of the apply_impl function:  
**template<typename Func, std::size_t... Idx>  
void apply_impl(Func f, boost::asio::detail::index_sequence<Idx...>)  
{  
  auto& result_type = *reinterpret_cast<promise_impl::result_type*>(&result);  
  f(std::get<Idx>(result_type)...);  // Remove std::move()  
}**  
and In the operator() function of the promise_handler class, the modification involves removing the unnecessary use of std::move() when storing the result values in the promise object. The line new (&impl_->result) result_type(ts...); initializes the result object by directly forwarding the ts arguments without using std::move().  
  
This modification ensures that the result values are efficiently stored in the promise object without unnecessary moves.  
Certainly! Here's an explanation of the modifications made to the code:  
  
**void operator()(std::remove_reference_t<Ts>... ts)  
{  
  assert(impl_);  
  
  using result_type = typename promise_impl<  
    void(Ts...), allocator_type, executor_type>::result_type ;  
  
  new (&impl_->result) result_type(ts...);  // Remove std::move()  
  
  impl_->done = true;  
  
  if (impl_->completion)  
    impl_->complete_with_result();  
}**

---
