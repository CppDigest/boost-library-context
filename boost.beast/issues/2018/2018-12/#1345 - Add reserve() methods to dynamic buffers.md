# #1345 - Add reserve() methods to dynamic buffers [Closed]

> Username: mst7555  
> Created at: 2018-12-03 18:30:19 UTC  
> Updated at: 2018-12-13 13:55:28 UTC  
> Closed at: 2018-12-13 13:55:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1345  

It seems that async_read_some triggers the handler for every 0.5k chunk of data - is there a way to control size of it ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-03 19:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1345#issuecomment-443845312  

Are you talking about `http::async_read_some`? That can be indirectly controlled by the choice of dynamic buffer. Which one are you using?

---

## Comment 2

> Username: mst7555  
> Created at: 2018-12-04 11:27:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1345#issuecomment-444067655  

Yes it is http::async_read_some and I currently use boost::beast::flat_buffer.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-04 14:37:01 UTC  
> Updated at: 2018-12-04 23:38:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1345#issuecomment-444121451  

We have confirmed the problem you are experiencing. What you need is to call `flat_buffer::reserve` to ensure a certain minimum capacity before calling `async_read_some` (or `async_read`). Unfortunately, that function does not exist yet. But you can use this function as a work-around:  
```  
/** Guarantee a minimum capacity.  
  
    Any buffers obtained using @ref data or @prepare will be invalidated.  
  
    @param n The desired minimum capacity. The actual capacity upon return may  
        be greater than this value.  
*/  
template<class Allocator>  
void reserve(boost::beast::basic_flat_buffer<Allocator>& b, std::size_t n)  
{  
    if(n < b.capacity())  
        return;  
    if(b.size() > 0 && b.size() >= b.capacity())  
        return;  
    if(n >= b.max_size())  
        return;  
    b.prepare(n - b.size());  
    BOOST_ASSERT(b.capacity() >= n);  
}  
```  
  
I'll add this as a member function but it won't ship until Boost 1.70

---

## Comment 4

> Username: mst7555  
> Created at: 2018-12-04 15:35:15 UTC  
> Updated at: 2018-12-04 15:35:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1345#issuecomment-444143283  

Great! Thanks for a very quick response! I've tried your code and modified it to make it work.  
First problem was bad parameter type of the buffer - there should be:  
`  void reserve(boost::beast::basic_flat_buffer<Allocator>& b, std::size_t n)`  
Another thing was that initially both b.size() and b.capacity() for flat_buffer returns 0 what fulfils 2nd if condition preventing reserve() from doing anything. I've changed it to:  
`  if (b.size() > 0 && b.size() >= b.capacity())`  
And everything started to work as expected!  
Cheers!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-12-04 16:01:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1345#issuecomment-444153131  

I've edited my response to incorporate your changes, thanks!
