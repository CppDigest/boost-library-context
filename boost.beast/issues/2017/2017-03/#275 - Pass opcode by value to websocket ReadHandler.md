# #275 - Pass opcode by value to websocket ReadHandler [Closed]

> Username: vinniefalco  
> Created at: 2017-03-01 17:04:51 UTC  
> Updated at: 2017-06-12 04:04:33 UTC  
> Closed at: 2017-06-12 04:04:33 UTC  
> Url: https://github.com/boostorg/beast/issues/275  

`beast::websocket::stream::async_read` takes `opcode` by reference which is a bit of a hassle. It would be nice to pass it by value. However this opens up a new set of problems. Which placeholder does the caller use? Does Beast provide new placeholders for it? For both `std::bind` and `boost::bind`? Its a mess...

---

## Comment 1

> Username: daniele77  
> Created at: 2017-03-02 14:03:02 UTC  
> Url: https://github.com/boostorg/beast/issues/275#issuecomment-283661031  

I think placeholders are not a real problem: unlike `boost::asio`, beast is a C++11 library, so the users will have `std::bind` and lambdas available. You can safely assume that your users will use only lambdas (or `std::bind` as a second choice). In that case, beast could provide just std placeholders (for error, opcode, response etc.). If, for whatever reason, a user want to use `boost::bind`, he could do so by using boost placeholders `_1`, `_2` ...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-08 20:08:28 UTC  
> Url: https://github.com/boostorg/beast/issues/275#issuecomment-307213113  

@daniele77 Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-12 04:04:33 UTC  
> Url: https://github.com/boostorg/beast/issues/275#issuecomment-307685500  

`opcode` is gone, now you call `got_text` or `got_binary` to find out if the last message was text or binary!
