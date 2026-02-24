# #420 The modifications remove unnecessary uses of std::move() for improved… [Closed]

> Username: ArielSilver  
> Created at: 2023-07-01 19:53:03 UTC  
> Updated at: 2023-07-02 06:01:41 UTC  
> Closed at: 2023-07-02 06:01:41 UTC  
> Url: https://github.com/boostorg/asio/pull/420  

This pull request addresses two modifications in the promise_impl class within the experimental/impl/promise.hpp file:  
There was unnecessary use of std::move() in the apply_impl function. Specifically, the line f(std::get(std::move(result_type))...); so this could modified to remove the unnecessary std::move() so it is modified to template<typename Func, std::size_t... Idx>  
void apply_impl(Func f, boost::asio::detail::index_sequence<Idx...>)  
{  
auto& result_type = reinterpret_cast<promise_impl::result_type>(&result);  
f(std::get(result_type)...); // Remove std::move()  
}  
Second modification is made In the operator() function, the move constructor is called to initialize the result_type object, but the subsequent usage of std::move(ts)... to pass the arguments seems unnecessary. Since the arguments are already passed by value, they are implicitly moved into the parameters. By removing std::move() from ts... and directly passing ts... to the constructor of result_type, the code avoids an unnecessary use of std::move(). Here it is modified version  
**void operator()(std::remove_reference_t... ts)  
{  
assert(impl_);  
  
using result_type = typename promise_impl<  
void(Ts...), allocator_type, executor_type>::result_type ;  
  
new (&impl_->result) result_type(ts...); // Remove std::move()  
  
impl_->done = true;  
  
if (impl_->completion)  
impl_->complete_with_result();  
}**

---
