# #1903 - Documentation issue with ConnectCondition [Closed]

> Username: ErikvLinstee  
> Created at: 2020-04-15 13:56:29 UTC  
> Updated at: 2020-05-05 00:18:46 UTC  
> Closed at: 2020-05-05 00:18:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1903  

The documentation is conflicted about the ConnectCondition.  
  
In basic_stream::connect it says:  
>  A function object that is called prior to each connection attempt. The signature of the function object must be:  
>   
> bool connect_condition(  
>     error_code const& ec,  
>     typename Protocol::endpoint const& next);  
>   
  
  
In basic_stream::async_connect it says:  
>  A function object that is called prior to each connection attempt. The signature of the function object must be:  
>   
> bool connect_condition(  
>     error_code const& ec,  
>     Iterator next);  
>   
  
Depending on my code, the compiler says one or the other:  
  
```  
        stream_.async_connect(  
            begin,  
            end,  
            [this](boost::beast::error_code const& ec, auto const& next)  
            {  
                std::cout << "Trying: " << next << std::endl;  
                return true;  
  
            },  
            std::move(handler));  
```  
  
 > connector.cpp:32:41: error: no match for ‘operator<<’ (operand types are ‘std::basic_ostream<char>’ and ‘const boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp>’)  
>    32 |                 std::cout << "Trying: " << next << std::endl;  
>   
  
So it's an iterator?  
But with this:  
  
```  
        stream_.async_connect(  
            begin,  
            end,  
            [this](boost::beast::error_code const& ec, auto const& next)  
            {  
                std::cout << "Trying: " << next->endpoint() << std::endl;  
                return true;  
  
            },  
            std::move(handler));  
```  
  
> connector.cpp:32:44: error: base operand of ‘->’ has non-pointer type ‘const boost::asio::ip::basic_resolver_entry<boost::asio::ip::tcp>’  
>    32 |                 std::cout << "Trying: " << next->endpoint() << std::endl;  
  
So now it's a resolver_entry?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-15 14:12:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-614064472  

when you say "depending on my code", what do you mean exactly?

---

## Comment 2

> Username: ErikvLinstee  
> Created at: 2020-04-15 15:05:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-614095339  

> when you say "depending on my code", what do you mean exactly?  
  
Assuming that it is supposed to be an endpoint then this should work: `std::cout << "Trying: " << next << std::endl;`  
  
However, the compiler says it can't, because next is an endpoint iterator.  
  
So when I change the code to derefence the endpoint it is pointing to like so: `std::cout << "Trying: " << next->endpoint() << std::endl;`,  
it now says next is an endpoint.  
  
I am sure `auto const& next` is the problem, because when I replace that by `endpoint const& next`, then `<< next <<` works.

---

## Comment 3

> Username: ErikvLinstee  
> Created at: 2020-04-15 15:13:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-614099627  

> Interesting.  
>   
> Do I take it that you are suggesting that the timeout should be per-connect-attempt rather than overall for the entire connect operation?  
  
I don't know about should, but yes, that is what I would prefer. However, that would conflict with Asio, would it not?  
  
In my production code, I have a class that solves this by taking an iterator from the resolver and trying one at a time, until a succesful connect, or until reaching `boost::asio::ip::tcp::resolver::iterator()`.  
A timer is set for every attempt and if it goes of, the next in line is attempted.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-20 14:49:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-616603050  

> > when you say "depending on my code", what do you mean exactly?  
>   
> Assuming that it is supposed to be an endpoint then this should work: `std::cout << "Trying: " << next << std::endl;`  
>   
> However, the compiler says it can't, because next is an endpoint iterator.  
>   
> So when I change the code to derefence the endpoint it is pointing to like so: `std::cout << "Trying: " << next->endpoint() << std::endl;`,  
> it now says next is an endpoint.  
>   
> I am sure `auto const& next` is the problem, because when I replace that by `endpoint const& next`, then `<< next <<` works.  
  
Without seeing the context of the code, I'm shooting in the dark. It's possible that neither overload has been selected because they would be ambiguous.  
  
I tend to find with asio that it's better to specify the handler arguments explicitly. Otherwise about 1 time in 10 I have a problem.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-04-20 14:52:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-616605353  

> > Interesting.  
> > Do I take it that you are suggesting that the timeout should be per-connect-attempt rather than overall for the entire connect operation?  
>   
> I don't know about should, but yes, that is what I would prefer. However, that would conflict with Asio, would it not?  
>   
> In my production code, I have a class that solves this by taking an iterator from the resolver and trying one at a time, until a succesful connect, or until reaching `boost::asio::ip::tcp::resolver::iterator()`.  
> A timer is set for every attempt and if it goes of, the next in line is attempted.  
  
This seems like a sensible solution. I think it would be diffucult to try to provide a fully generic solution without pushing developers into corners in which they'd rather not be pushed.

---

## Comment 6

> Username: ErikvLinstee  
> Created at: 2020-04-20 16:19:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-616659904  

On 20-04-2020 16:49, Richard Hodges wrote:  
>  
> Without seeing the context of the code, I'm shooting in the dark. It's   
> possible that neither overload has been selected because they would be   
> ambiguous.  
>  
> I tend to find with asio that it's better to specify the handler   
> arguments explicitly. Otherwise about 1 time in 10 I have a problem.  
>  
  
Yes, never mind, I have found the same. It is more likely than not   
indeed a problem with template deduction. I have double checked the code   
just to be sure and next is supposed to be an endpoint.

---

## Comment 7

> Username: ErikvLinstee  
> Created at: 2020-04-20 16:29:07 UTC  
> Updated at: 2020-04-20 16:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-616665757  

On 20-04-2020 16:53, Richard Hodges wrote:  
  
>     In my production code, I have a class that solves this by taking  
>     an iterator from the resolver and trying one at a time, until a  
>     succesful connect, or until reaching  
>     |boost::asio::ip::tcp::resolver::iterator()|.  
>     A timer is set for every attempt and if it goes of, the next in  
>     line is attempted.  
>  
> This seems like a sensible solution. I think it would be diffucult to   
> try to provide a fully generic solution without pushing developers   
> into corners in which they'd rather not be pushed.  
>  
  
My former solution was specific to tcp::socket, because that was what I   
was working with at the time.  
  
I have since refactored the code into a generic solution, that takes a   
stream as a template argument. The only requirement is that the stream   
class has an async_connect member that takes an endpoint and a   
completion handler as arguments, and that has a close member. Like   
tcp_stream and tcp::socket for instance.  
  
Here is its interface, I can provide full source if anyone is interested:  
  
```  
     explicit connector(StreamType& stream) noexcept;  
  
     template <  
         typename EndpointSequence,  
         typename Handler>  
     void async_connect(  
         EndpointSequence endpoints,  
         Handler&& handler) noexcept;  
  
     template <  
         typename EndpointSequence,  
         typename ConnectCondition,  
         typename Handler>  
     void async_connect(  
         EndpointSequence endpoints,  
         ConnectCondition condition,  
         Handler&& handler) noexcept;  
  
     void cancel() noexcept;  
  
     void expires_after(std::chrono::seconds expiry_time) noexcept;  
```  
  
I have only implemented the async_connect case, but adding a synchronous   
connect would be trivial. The expires_after member defines how long   
before an individual connection attempt will time out.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-04-26 16:21:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-619579071  

I copied the javadoc from Asio, so it might need adjusting.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-05-01 15:55:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-622445134  

@ErikvLinstee   
  
Hi Eric, it's been a little while since you reported this issue.  
  
Is there specific action you'd like me to take, or can I go ahead and close the issue?

---

## Comment 10

> Username: ErikvLinstee  
> Created at: 2020-05-01 16:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1903#issuecomment-622468995  

Fix the documentation, I guess. If I recall correctly, the ConnectCondition is using an endpoint, not an iterator.
