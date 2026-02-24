# #175 - prepared statements design problem [Closed]

> Username: enginelesscc  
> Created at: 2023-08-29 17:39:49 UTC  
> Updated at: 2024-08-10 16:23:59 UTC  
> Closed at: 2024-08-10 16:23:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/175  

Appending to [157](https://github.com/boostorg/mysql/issues/157)  
  
> When you're done with a statement, you can instruct the server to de-allocate it using [connection::(async_)close_statement](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/close_statement.html).  
> Note that statement's destructor does not close the statement, since this is an async operation that can fail.  
  
I don't agree with this.  
As we are using C++, where the point of the language is the use of RAII, this is calling for disaster for many people who assume the default behavior is that the object (statement) is destroyed once it goes out of scope.  
  
Well yes, I understand the concern about async failure, but I prefer at least a synchronous close instead of a leak.  
  
In the end for those who care about async.. there is still the [async_close_statement](https://www.boost.org/doc/libs/1_83_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/async_close_statement.html) method.  
  
A server resource is no different than a memory resource.  
But once the statement went out of scope there is no way to close it anymore (leaked) and this is an oversight, especially when connections are long-lived.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-08-30 15:43:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-1699422485  

Making the destructor invoke a sync method (that can actually fail) can have even worse consequences:  
  
```  
awaitable<string> do_query(tcp_ssl_connection& conn) {  
    results r;  
    auto stmt = co_await conn.async_query("SELECT name FROM employees", use_awaitable);  
    co_await conn.async_execute(stmt.bind(), r, use_awaitable);  
    co_await conn.async_close_statement(stmt, use_awaitable);  
    co_return r.at(0).at(0).as_string();  
}  
```  
  
If for whatever reason `async_execute` fails there, you're invoking a sync method in async code, which is really bad.  
  
> for those who care about async  
  
You're talking like this is almost no-one, when it's one of the main points of the library.  
  
That said, I'm open to implementing a sync RAII-style guard for statements (e.g. `statement_guard`) that implements this.

---

## Comment 2

> Username: enginelesscc  
> Created at: 2023-08-30 17:36:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-1699587021  

My thoughts:  
  
Lets assume the connection failed.  
-> We mark the conn as dead and we throw.  
  
Old Way: nothing, but no leak as the statement closure will be handled by server.  
  
Proposal: In this case, the dtor of stmt which would be calling close_statement should just nop(do nothing).  
  
Q: What about a mysql error/exception?  
  
Old way: we throw and therefore just leak the statement and waste server resources.  
  
Proposal: Thanks to unwind, stmt dtor will be called, which closes the statement, there is nothing that would go wrong here in terms of mysql logic.  
  
Well of course, dtor may throw, bad. But we may just swallow it and not throw? Do we care if cleanup in dtor fails? Dont think so as in worst case the connection has died. (There should be no case where mysql server refuses a statement closure)  
  
  
> You're talking like this is almost no-one, when it's one of the main points of the library.  
  
This is besides the point, do you prefer **accidental blowup/DOS of production db** (global stmt limit) or reduced performance at the local application level caused by blocking operations?  
  
And if it really has to be async in dtor, or chosen based on if last op was async or not, we could post to an internal connection strand and handle it in the background?

---

## Comment 3

> Username: enginelesscc  
> Created at: 2023-08-30 17:58:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-1699614189  

Three references which all clean up in dtor (and swallow errors):  
  
1: https://github.com/Roslaniec/MariaCpp/blob/master/src/prepared_stmt.cpp#L51  
2: https://github.com/mysql/mysql-connector-cpp/blob/trunk/jdbc/driver/mysql_prepared_statement.cpp#L390  
  
3:  
https://github.com/mariadb-corporation/mariadb-connector-cpp/blob/master/src/ServerSidePreparedStatement.cpp#L38  
https://github.com/mariadb-corporation/mariadb-connector-cpp/blob/master/src/util/ServerPrepareResult.cpp#L36

---

## Comment 4

> Username: anarthal  
> Created at: 2023-08-31 13:49:12 UTC  
> Updated at: 2023-08-31 13:49:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-1701075846  

Sure, in a sync world it's the way to go.  
  
> This is besides the point, do you prefer accidental blowup/DOS of production db (global stmt limit) or reduced performance at the local application level caused by blocking operations?  
  
Don't underestimate the impact of calling sync functions in async code. It will completely hang the server thread and make everything unresponsive. And it's an almost-impossible to track bug. This is not something I'm going to assume.  
  
Dispatching in the background is a bad idea because you require that the connection is alive until the operation completes, which you have no way to ensure. So until we have a proper async cleanup mechanism (there's a proposed Boost library that handles this with coroutines), this will only apply to sync users.  
  
Since calling network operations in a destructor is non-conventional in Asio, and it shouldn't apply to async users, I want it to be explicitly opt-in, yet simple to use. I'm thinking of:  
  
```  
void f(tcp_ssl_connection& conn) {  
    statement_guard guard = conn.prepare_statement_guarded("SELECT ...");  
    results r;  
    conn.execute(guard->bind(...), r);  
}  
```  
  
With `statement_guard` wrapping the returned statement and invoking `close_statement` in the destructor (errors are swallowed).  
  
Would that suit your use case?

---

## Comment 5

> Username: enginelesscc  
> Created at: 2023-08-31 16:53:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-1701413704  

Generally yes, but I have just found out its possible to have async raii: [co_resource](https://vector-of-bool.github.io/2021/12/30/co_resource.html)  
I would assume this to be added to boost/asio at some point in the future.  
  
That way we can't leak statements nor block threads in async contexts.  
Can we use that?  
  
# async  
async_prepare_statement yields a `boost::mysql::co_statement_guard` and calls async_close_statement on resume.  
-> resume is called by `boost::mysql::co_statement_guard`s destructor  
  
# blocking  
prepare_statement (blocking) returns a `mysql::statement_guard`, which calls close_statement on destructor  
  
# example  
  
[Example code](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxBIaXKQADqgKhE4MHt6%2BekkpjgJBIeEsUTFccbaY9nkMQgRMxAQZPn7xdpgOabX1BAVhkdGxrXUNTVlD3b1FJYMAlLaoXsTI7BzmAMzByN5YANQma25OCgTEmKz72CYaAILrm9uYewfIx/iCF1e3ZhsMW167%2BzcaGIC0cIQ%2BN0%2BBEwLASBmhgIIAE8EoxWI8ACoQ65bJgKBQ7dA%2BFhIgD6wNQewA7FYbgkvBFaHhkCBPjt2TtjsQvA4dgkQSw8EpScjUdTadcOVKdkwvEQdsBMARSacCIsGKTUBFtO0CBAZuK2dLjar1YTiWSKYCsWtLjTXiAUCRQcFMKSEIZ0PRAfzUILhaLMBdHVQBaTff7MBAAFQEBBCmYmKkAEX2EuN7KTqZuGZlcspwVSYlJCi8ClRDHQ%2Bp2DFQmFUywSBHFO1NxAYnII6EdpfLrlJIQAbtEk1YU2nqdnJRnZfKaMxaCWyxWqwba/XG83R62lWaHSBeyvSWIAO5MJEKUdZifXnMZweoPDoHdq9ukh9P6vrhuYJuG8d3saH7Pl4DAepW9DoKSG6/tU1bbnGIInjeAHTpm45rBKRrsrOlJMGehAqmc6BIvBNIvmaJxeEGmGTthOzATKBHKoergQLh5gAGw7BSBrbreaEMY%2Bz74UwhGnKWbBkRYdF0sQeCDkw0Ksne%2B4UnKrrup63oHBGQpuoGFw8c6pJpkaRIsCS5LOux%2BbGSCBogPZqCkhAvH/lO16YZ8UIwnCSk0W4gbMGwOw2pcNy4vixkqmJeCGnSDJMiyRpcjyzZ6QGKI0emnbcryhZbjS9Fqc6GkhFpEGBWgsV4BQICZQZ2VGRSpneXeXm5WlvKNSK2UJYJaAMMcYXRjsSJ4JUWBQYp3huk2xBXhhuU4XZE1Te%2BYjUW5AgjViZjcfUwBrnWP5/legEcuttDTZtc3hicTzJnsB1HWZl3sm2Hb7qxlbHrQZ4XktqbtYJAlSrhCpKkRr4alqOoONJ9Gfbu7Y7IVo6lSC5VuuBXqBb1hm2iGYaNTGcYJp19HgxykOFXgxa/auNanZuLZfZ23YHsu/ZDiOxXLbJgmQ/OjM85WX6s7B7Oo99XY9uLUGnuel4CyDMk0%2ByjFfe%2BwmSzB51q/RjGgXjkHQWdcF8eRiGoMhtE01TckKQFKnTljLoVWbBMCvpfWoi1JnvdOjFbGcDBeAkSOXXgVA7DtIKktbK0cq1AB0Ek%2BFGiag8a6dYFyqCkTnWEaAAnOyrVPTWXi0LQC3B1cZeO6h9KMsybuVy5xBxRAhXo45zmuXgaetcnNMAH41T3dXR7c5dh4Ykf6sH6FTqtRBcTsqCoj3RDENG1ZDSN35s9uHPRunZMzGn123bN1EPYtDuoRvqBjTv0RKSQAC0FxH7tZsp9pbn1lkPNO19b6TRupgGaW15onBQuvbeu9v7EDClxABw0gFS0NjJC%2BV9fZKH1FAqasC7qPwbi/ZB084pb0/nvEg%2Bxkw7VqngLi9CToGyKqXMui8I5RxLvRKuzDt6jyDqDJuqBxGJ2rhHOuVDeEXwppeXOQsnbz2uCFTA5YmDLB2CSBQABHWgA0pTdWbMcAKbBBBmIzMEZsD83TA1XhmKx0IbF6gcQxeBg8nGuScePD6bj5ZOjlE8NwgIXpmEMSYnsdQPGMAIPE6xSSnJJOIEicwZgIlRP3K4WgrjjSa2lBPdxMIklz1zOY0JaBwmAiidk2JtAUmJMEI6MpCSKntJ2BkrJZgckNIOJzR0BSinShKRo%2Bi2jdH6KGiEDoAg7HGgslZK0BxgJGTxEiX45JaDJDdOUzxEBmmtO6QQLe/BUBBMErmUq9SDiNIGacp0DAFnVEdNs3ZWwDkli6Z49JghMmkB8XNJy2TcnDKuWnfxkK3AjJAGMtR1SYpfXGVKASyNU7dzoQcF5RyklbIUDs5A4ZTgJHqIc/5lTvGBOWdU%2B5W5HnDKaReOJrz3lpE%2BcS3Z/JfyUr%2BaknpfSIVDPhfkiC6KMz4upbYq5EBAlSuNIyuF0SXnzN1FykAXzSV8opacQVbTknjWgegUVzLxWhKRSnXMNU747CuUq6UKqxVqrZS0jlmrnDap5bq8lAqCU9JyMqO%2B5rInDIlV6J1UoaqiUIDKX1ezfmBr1FcoRwTlW1NBKq1lxiPUasWX4H1JKyX8oNSmpyaKBmqsjYU5F1Tp6yydRojFqFMU3G8SwMSDAqkchdRatV3bAUnH6YMgdtbxmQ2OCwJlz11UCE5d6nVpb9VUqFXqKk6bBL9vDfCppQ6%2BTJGVMgU4AUQVOJ2OC6trrp0ED/raWFN6rWSvrTup5Zg05pxPCQAA1p%2BtOYa8nPqjZIlMHA5gtI4AAVl4H4DgWhSCoE4JEyw1hOQLCWI8dYPBSDJPg%2BBuYP6QBQbLmnMwUGoMaCpGXDQGhOJUipGYcokHJCwc0LwJDHBeAKBABoXD7G5hwFgDARATpYR0GiOQSgaBxP0BiMABQzAEgKAQKgAgpAsCDmZJgAAapNE8AB5CsnAcM0FoNCYgPGIARHY6QCIwR6hIhM7wezzBMkGe1LqZzpAZOeIMwwWgTn8MacwBELwwA3BiFoDx7gvAsBdqMOIYL%2BBTgdGHDFhD9Z2hyhWAhhxlRbNMgiD3TJHgsC2ZOHgFgzm5hUAMApvTmBDPGdizIQQIgxDsCkG1%2BQSg1C2d0PEAwRgUDWGsPoPAEQeOQDmDvaoMWf6vGYaYVDlgmMceHMQeSBd4BzDaIWlwlZRgtFIIEV0UwBjxByKkAQx3sjJBuwwSY/RSgVCqJ0YYjRPDND0Pt6oXQGjPeKJd2wn27vjEB%2Bdl7Eg9sYeWDD/QnAYOkDgwhzjOxVAAA5OI/04pIBUyBkDxyogwH9BoIC4EICQF6awuAzF4HhrQMxCPEb4yx3g1WzBUjToxqDmOoOcU4mXKkmOuBl1p9IVHHHODcd4/x/DgmRMQCQKCek6mKA7Vk9EUI6JOCqDxwYhQg4ieDjF2neEOiCC8FgVT7beh%2BDtdEOIbrDvesqHUMFwbpATw9wSDVxH0G2PBc4wZuUavt5xyxzjg3wBCfE%2B5KT8nHgtfoOw/T%2BXTO5gIGIgMfUAfWOkGq4xsjVJJBSDWGsDQZguCY7o2XFHtnOOy744zgjpAiOSA0DzyQkgy5MbLuLgZ1H6%2BQbWEHtHMuM9t8g2YDnsQ%2BNS8Q5P1vzPSCbZSM4SQQA)  
Output:  
  
```  
main: entry  
mysql::connection::async_prepare_statement: entry  
mysql::statement::statement: entry  
mysql::connection::async_prepare_statement: yield  
main: post_create, value : 7  
..work... // coroutine holding the statement goes out of scope here!  
mysql::connection::async_prepare_statement: post_yield  
mysql::connection::async_close_statement: entry, value: 7  
mysql::connection::async_prepare_statement: return  
mysql::statement::~statement: entry  
```

---

## Comment 6

> Username: anarthal  
> Created at: 2023-09-01 11:28:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-1702600360  

That's unfortunately C++20-coroutine-specific, and from what I've read, it looks specific to each coroutine library.  
  
This library targets Asio's universal async model. Async functions are not coroutine or callback based per-se. They all get passed a [`CompletionToken`](https://think-async.com/Asio/boost_asio_1_28_0/doc/html/boost_asio/overview/model/completion_tokens.html) object, that specifies what they should become into. If you pass the special value `boost::asio::use_awaitable`, you have C++20 coroutines. If you pass `boost::asio::yield_context`, you have stackful coroutines. You can pass a callback if you like old style programming. The set of valid completion tokens is not a closed set. This is how you can use the library with C++20 coroutines, while still being compatible with C++11 code.  
  
I think the main problem with C++20 coroutines right now is that you need a lot of boilerplate on top of them to make them useful. There is a library proposed for Boost that implements something very similar to the idea of a `co_resource` you linked. The author went more Pythonic and called the feature [`with`](https://klemens.dev/async/#with). If this happens to go into Boost, I will definitely add an async guard class that implements this.  
  
I would still have the C++11 compatibility problem. I can't make it the default return value for `async_prepare_statement` because it's only compatible with one completion token (in this case `use_op`, which is the token implemented by the Async library). Otherwise I'd be breaking users of callbacks, stackful coroutines, and so on.  
  
Yeah, doing async with Asio is a headache. It's really powerful, but sometimes you pay too much a price for the generality. I hope that [async](https://klemens.dev/async/) gets someday widely adopted and we can use safer constructs like the one you describe.  
  
Async's review's taking place on September. I can pass you the dates in case you'd like to participate.  
  
I will try to get the sync guard done for now, and will do the async depending on what happens during the review.

---

## Comment 7

> Username: anarthal  
> Created at: 2024-08-10 16:23:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/175#issuecomment-2282200383  

With the advent of `connection_pool` and `reset_session`, which cleans up statements automatically, and the new Asio and MySQL coroutine features, which make async almost the default, I'm convinced this is not the way to go.
