# #107 - dynamic buffer design cannot support the strong exception guarantee [Closed]

> Username: vinniefalco  
> Created at: 2018-05-07 18:12:00 UTC  
> Updated at: 2020-12-30 00:50:58 UTC  
> Closed at: 2020-12-30 00:50:57 UTC  
> Url: https://github.com/boostorg/asio/issues/107  

Dynamic buffer adapters such as `dynamic_string_buffer` remember the previous size of the input sequence so that the implementation of `commit` can identify which part of the underlying storage corresponds to the input sequence versus the output sequence. This prevents dynamic buffer implementations from offering the strong exception guarantee, as this code snippet demonstrates:  
```  
std::string s;  
try  
{  
    dynamic_buffer(s).prepare(50);  
    throw std::exception("oops");  
}  
catch(std::exception const&)  
{  
    assert(s.empty()); // fails  
}  
```  
  
Note that even without exceptions, traditional idioms which use `error_code` can still leave the underlying storage in an invalid state:  
```  
void f(socket& sock, DynamicBuffer&& b_, error_code& ec)  
{  
  typename std::decay<DynamicBuffer>::type b{b_};  
  auto const bytes_transferred = sock.read_some(b.prepare(50), ec);  
  if(ec)  
  {  
    // at this point, if b_ is a dynamic_string_buffer, the underlying string will  
    // have 50 surplus default-initialized characters  
    return;  
   }  
  ...  
```  
  
One solution is to adjust the size of the underlying storage in the destructor of the dynamic buffer, but this fails if the dynamic buffer is copied rather than moved, where the destructor of a moved-from object performs no actions.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:50:56 UTC  
> Url: https://github.com/boostorg/asio/issues/107#issuecomment-752289448  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#613](https://github.com/chriskohlhoff/asio/issues/613).
