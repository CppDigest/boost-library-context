# #448 - boost::asio::execution::bad_executor needs an explicit BOOST_ASIO_DECL [Open]

> Username: jcelerier  
> Created at: 2025-03-16 12:05:41 UTC  
> Updated at: 2025-03-16 12:13:24 UTC  
> Url: https://github.com/boostorg/asio/issues/448  

When linking with lld (from clang-64 MinGW) on windows, with BOOST_ASIO_DYN_LINK:  
  
     ld.lld: error: undefined symbol: vtable for boost::asio::execution::bad_executor  
  
Every other symbol is correct, but since the type `boost::asio::execution::bad_executor` itself is not marked `BOOST_ASIO_DECL`, then the implicit dtor won't be marked as exported.

---

## Comment 1

> Username: jcelerier  
> Created at: 2025-03-16 12:13:23 UTC  
> Url: https://github.com/boostorg/asio/issues/448#issuecomment-2727363603  

hmm.. actually I'm still getting the same link error despite marking everything:  
  
```  
class BOOST_ASIO_DECL bad_executor : public std::exception  
{  
public:  
  /// Constructor.  
  BOOST_ASIO_DECL bad_executor() noexcept;  
  BOOST_ASIO_DECL virtual ~bad_executor() noexcept;  
  
  /// Obtain message associated with exception.  
  BOOST_ASIO_DECL virtual const char* what() const noexcept;  
};  
  
BOOST_ASIO_DECL bad_executor::bad_executor() noexcept { }  
BOOST_ASIO_DECL bad_executor::~bad_executor() noexcept { }  
  
BOOST_ASIO_DECL const char* bad_executor::what() const noexcept  
{  
  return "bad executor";  
}  
```
