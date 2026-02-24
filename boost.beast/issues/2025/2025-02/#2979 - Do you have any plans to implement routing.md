# #2979 - Do you have any plans to implement routing? [Closed]

> Username: HENRYHKll  
> Created at: 2025-02-06 15:34:03 UTC  
> Updated at: 2025-02-21 15:44:54 UTC  
> Closed at: 2025-02-21 15:44:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2979  

I currently support about 10 independent projects using boost::beast, and all of them use http. I have seen how the routing mechanism works in these projects, and I must say that it is not ideal. All projects have good code, but as the product grows, the project's code base is growing as well, and it can become difficult to maintain. For routing, some projects use Boost.URL, while others use their own library or "if-else" statements. When there are only a few endpoints, this is not a big issue, but when there are dozens of endpoints, each with multiple methods, the code can become unreadable and difficult to maintain. We could use add-on libraries over boost::beast to help with routing, but this would introduce an additional dependency. After discussing the issue with my colleagues, I realized that they too were confused about why there was no out-of-the-box routing available. Is it worth waiting for the implementation of routing in Beast?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-02-10 20:32:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2979#issuecomment-2649171251  

We are very unlikely to implement routing in Beast. I would advise that you accept adding another dependency to the project. Dependencies allow external libraries to specialize and be the best at their tasks. Adding it to Beast doesn't really make sense. To put it a different way, should Boost.Asio add HTTP, Websocket, and routing? (No).
