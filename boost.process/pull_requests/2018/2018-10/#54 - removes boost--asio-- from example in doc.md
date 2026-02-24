# #54 removes boost::asio:: from example in doc [Merged]

> Username: tnixeu  
> Created at: 2018-10-20 10:03:53 UTC  
> Updated at: 2018-10-26 03:35:07 UTC  
> Merged at: 2018-10-26 03:35:06 UTC  
> Closed at: 2018-10-26 03:35:07 UTC  
> Url: https://github.com/boostorg/process/pull/54  

In an example `boost::asio::io_service ios;` is used like this and looks in the generated doc like `boost::asio::boost::asio::io_service ios;` This pull request removes `boost::asio::` from the example.

---

## Comment 1

> Username: tnixeu  
> Created_at: 2018-10-20 11:08:49 UTC  
> Url: https://github.com/boostorg/process/pull/54#issuecomment-431570955  

I fixed some more of those issues. I am not sure if I have to change the pull request or make a new one. I see both commits listed here. So I think I do not have to make a new pull request.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2018-10-24 16:39:30 UTC  
> Url: https://github.com/boostorg/process/pull/54#issuecomment-432733705  

Why would you remove `boost::asio`? I mean I should rename it to `io_context`, but I don't see why you would remove the scope.

---

## Comment 3

> Username: keith-bennett-gbg  
> Created_at: 2018-10-24 17:17:54 UTC  
> Url: https://github.com/boostorg/process/pull/54#issuecomment-432748947  

There's a few places where the namespace prefix is duplicated such that it says `boost::asio::boost::asio::`in the rendered documentation. Which is fine enough for someone who's paying attention and knows that typos happen and can reason about it. But it's annoying anyway. I think that's partially what this is trying to resolve.

---

## Comment 4

> Username: tnixeu  
> Created_at: 2018-10-24 21:18:03 UTC  
> Url: https://github.com/boostorg/process/pull/54#issuecomment-432831662  

I removed it, because it is added during generating the doc again. That is the first commit.  
In the second commit I fixed also some other replacement issues. Please have a look at the generated doc.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2018-10-25 02:14:38 UTC  
> Url: https://github.com/boostorg/process/pull/54#issuecomment-432889508  

Oh, right, because the macro generates the prefix. Thanks, I'll update it to 'io_context' in another place.

---
