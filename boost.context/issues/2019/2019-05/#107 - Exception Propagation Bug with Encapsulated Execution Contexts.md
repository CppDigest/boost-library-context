# #107 - Exception Propagation Bug with Encapsulated Execution Contexts [Closed]

> Username: plewistopher  
> Created at: 2019-05-17 03:01:58 UTC  
> Updated at: 2019-05-25 05:25:14 UTC  
> Closed at: 2019-05-17 15:33:30 UTC  
> Url: https://github.com/boostorg/context/issues/107  

### Boost Version  
`1.69.0 compiled for amd64`  
### Compiler  
`g++ (GCC) 5.3.1 20160406 (Red Hat 5.3.1-6)`  
### OS  
`Linux myhostname 2.6.32-642.6.2.el6.x86_64 #1 SMP Mon Oct 24 10:22:33 EDT 2016 x86_64 x86_64 x86_64 GNU/Linux`  
  
This code causes a runtime exception as following:  
```  
Unknown C++ exception thrown in the test body.  
test: /home/plewis/dpkg/refroot/amd64/opt/include/boost/context/detail/exception.hpp:37: boost::context::detail::forced_unwind::~forced_unwind(): Assertion `caught' failed.  
zsh: abort (core dumped)  ./test  
```  
  
Code:  
```cpp  
// A Wrapper Class  
class Core {  
  boost::context::execution_context<void> ctx_;  
public:  
  explicit Core(boost::context::execution_context<void>&& ctx)  
      : ctx_{std::move(ctx)} {}  
  
  auto&& done() { return std::move(ctx_); }  
};  
  
boost::context::execution_context<void>&& fn02(Core&& c) {  
  throw std::runtime_error("help!");  
  return c.done();  
}  
  
// A Test Case  
void failing_exception_test_case() {  
  std::exception_ptr exc{};  
  
  boost::context::execution_context<void> source(  
      [&exc](boost::context::execution_context<void>&& ctx) {  
        try {  
          ctx = fn02( std::move(Core( std::move(ctx) )) );  
        } catch (boost::context::detail::forced_unwind const&) {  
          throw;  
        } catch (...) {  
          exc = std::current_exception();  
        }  
        return std::move(ctx);  
      });  
  
  try {  
    source = source();  
    if (exc) {  
      std::rethrow_exception(exc);  
    }  
  } catch (std::runtime_error const&) {  
    std::cout << "Runtime Error Caught" << std::endl;  
  }  
}  
```  
  
But this test, without the wrapper class, works fine:  
  
```cpp  
boost::context::execution_context<void>&& fn01(  
    boost::context::execution_context<void>&& ctx) {  
  throw std::runtime_error("help!");  
  return std::move(ctx);  
}  
  
void working_exception_test_case() {  
  std::exception_ptr exc{};  
  
  boost::context::execution_context<void> source(  
      [&exc](boost::context::execution_context<void>&& ctx) {  
        try {  
          ctx = fn01( std::move(ctx) );  
        } catch (boost::context::detail::forced_unwind const&) {  
          throw;  
        } catch (...) {  
          exc = std::current_exception();  
        }  
        return std::move(ctx);  
      });  
  
  try {  
    source = source();  
    if (exc) {  
      std::rethrow_exception(exc);  
    }  
  } catch (std::runtime_error const&) {  
    std::cout << "Runtime Error Caught" << std::endl;  
  }  
}  
```  
  
Output:  
  
```  
Runtime Error Caught  
```

---

## Comment 1

> Username: plewistopher  
> Created at: 2019-05-25 05:25:13 UTC  
> Url: https://github.com/boostorg/context/issues/107#issuecomment-495857209  

I stored a reference in my class instead of the value. Apparently I'm doing  
something wrong with my move declarations. There's a stack overflow about  
this precisely.  
  
On Sat, May 25, 2019, 01:19 Gareth Ellis <notifications@github.com> wrote:  
  
> @plewistopher <https://github.com/plewistopher> - I'm having a similar  
> issue. How did you resolve this?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/context/issues/107?email_source=notifications&email_token=AEYHJ4XXCB47SSYDADJJXDLPXDD7ZA5CNFSM4HNRV4Y2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODWHCWBQ#issuecomment-495856390>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AEYHJ4SKRBIQN5EXILLHKKLPXDD7ZANCNFSM4HNRV4YQ>  
> .  
>
