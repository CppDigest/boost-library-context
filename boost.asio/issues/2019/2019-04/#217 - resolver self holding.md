# #217 - resolver self holding [Closed]

> Username: g-glowacki  
> Created at: 2019-04-03 12:41:12 UTC  
> Updated at: 2019-04-04 19:32:48 UTC  
> Closed at: 2019-04-03 19:16:11 UTC  
> Url: https://github.com/boostorg/asio/issues/217  

Hi,  
I'm looking for a safe solution of using resolver in concurrent execution. I've come with an idea where the ResolveContext, can hold the resolver itself, is it safe?  
```  
struct ResolveContext  
{  
  public:  
    ResolveContext(std::shared_ptr<boost::asio::ip::tcp::resolver> resolver, Connector connector);  
          
    void operator()(const boost::system::error_code& ec, boost::asio::ip::tcp::resolver::iterator i);  
private:  
        std::shared_ptr<boost::asio::ip::tcp::resolver> resolver;  
        ClientConnector io;  
};  
  
ResolveContext::ResolveContext(  
    std::shared_ptr<boost::asio::ip::tcp::resolver> initialResolver,  
    ClientConnector connector  
    ) :  
    resolver(std::move(initialResolver)),  
    io(connector)  
{  
}  
  
ResolveContext::operator()(  
    const boost::system::error_code& ec,  
    boost::asio::ip::tcp::resolver::iterator i)  
{  
   if (!ec)  
   {  
          this->io->Connect(i);  
   }  
}  
  
```  
  
And now can have a simple function with just one shared between threads ioService:  
```  
Connect(  
   boost::asio::io_service& ioService,  
   Connector connector,  
    string host,  
    string port  
    )  
{  
    auto resolver = std::make_shared<boost::asio::ip::tcp::resolver>(ioService);  
    ResolveContext context(  
        resolver,  
        std::move(connector));  
  
    resolver->async_resolve(host, port, std::move(context));  
}  
  
```

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-03 15:18:14 UTC  
> Url: https://github.com/boostorg/asio/issues/217#issuecomment-479534792  

If you're using asio 1.66+ or higher you can just use a `unique_ptr`:  
```  
auto resolver = std::make_unique<boost::asio::ip::tcp::resolver>(ioService);  
auto& ref = *resolver;  
  
ref.async_resolve(host, port,  
  [r = std::move(resolver), c = std::move(connector)](error_code ec, auto it){  
    if (!ec)  
      c->Connect(it);  
});  
```

---

## Comment 2

> Username: travnick  
> Created at: 2019-04-04 05:42:48 UTC  
> Url: https://github.com/boostorg/asio/issues/217#issuecomment-479758104  

@djarek So in versions prior 1.66 it's unsafe to use such solution, why? Can you please provide more detailed explanation of why/why not. Is there any documentation about that?  
  
I'm asking because it's quite important to me to provide a proof (if possible) that this piece of code is going to work correctly.

---

## Comment 3

> Username: djarek  
> Created at: 2019-04-04 10:46:46 UTC  
> Url: https://github.com/boostorg/asio/issues/217#issuecomment-479847408  

Prior to 1.66 ASIO required CompletionHandlers to be CopyConstructible, which would cause a compilation failure of the snippet I posted.

---

## Comment 4

> Username: travnick  
> Created at: 2019-04-04 17:07:03 UTC  
> Url: https://github.com/boostorg/asio/issues/217#issuecomment-479983042  

Oh, you were talking only about the support for move semantics here.  
  
What about the safety of passing the "self" inside "this"? It looks like circular dependency issue, if somehow the `context` is stuck in some kind of queue (as documentation says, the request are waiting for resolution and it's possible to cancel them).  
  
This is the possible issue I'm aware of and - unfortunately - documentation does not explain it very well.

---

## Comment 5

> Username: djarek  
> Created at: 2019-04-04 19:32:48 UTC  
> Url: https://github.com/boostorg/asio/issues/217#issuecomment-480033243  

Of course, if you don't retain any reference/pointer to the resolver, then you have no ability to cancel the operation, so it will be pending until the ExecutionContext the resolver uses is destroyed.
