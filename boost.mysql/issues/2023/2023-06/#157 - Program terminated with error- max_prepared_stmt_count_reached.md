# #157 - Program terminated with error: max_prepared_stmt_count_reached [mysql.common-server:1461] [Closed]

> Username: tonyyxliu  
> Created at: 2023-06-01 14:37:11 UTC  
> Updated at: 2023-06-01 17:05:27 UTC  
> Closed at: 2023-06-01 17:05:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/157  

Hi authors,  
I am using this library to build a load generator for MYSQL, and found it very useful. Unfortunately, it seems that too many calls of `connection.[async_]prepare_statement` function will crash my program. Could you please help me fix this issue? Thanks a lot!  
  
## Cause of Error  
I wrote my load generator based on the [`async_callbacks`](https://github.com/boostorg/mysql/blob/master/example/async_callbacks.cpp) example you provided, but got the following error when the QPS becomes large (10k for example). For each request, I currently need to call `connection.[async_]prepare_statement` and `connection.[async_]execute` one time.  
  
## Error Message  
```text  
terminate called after throwing an instance of 'boost::wrapexcept<boost::mysql::error_with_diagnostics>'  
  what():  er_max_prepared_stmt_count_reached [mysql.common-server:1461]  
````  
  
I have some questions regarding this problem which may be helpful:  
- Is it necessary to prepare the statement any time, even if the statement is hard-coded without any parameter?  
- Can I explicitly construct the `boost::mysql::statement` from string type? If so, how to do that?  
  
Thank you very much for your help and such a nice library!  
  
## Other Questions When Using This Libraries  
  
I have also attached something which I found a little bit weird when using this library here. I will be very grateful if you could have a look on them.  
-  The function api `connection.async_execute` seems to be implemented only in the [`include/boost`](https://github.com/boostorg/mysql/tree/master/include/boost) library here in GitHub but not in the official Boost-1.8.2, which only provides a similar function called `connection.async_execute_statement`. I don't know how to use this function since the `WriteFieldTuple` is not user defined as you said in a previous issue, and I have to include your [`include/boost`](https://github.com/boostorg/mysql/tree/master/include/boost) as a third-party library for my project along with the Boost-1.8.2, which actually looks pretty similar to the one in the official Boost-1.8.2. It is very strange and hard to use. Is this because the function `connection.async_execute` has not been included in Boost yet?  
- Is the connection pool feature ready to use now? I saw you opened an issue previously but no further information found. Up to now, I am just calling the `connect, prepare, execute` functions and have no idea if the connection pool technique is really applied.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-06-01 15:34:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/157#issuecomment-1572283328  

Hi @tonyyxliu!  
  
Some background: there are two ways to execute SQL with this library:  
* [Text queries](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/queries.html) can be used to run plain-text SQL, without parameters. You can run them using [`connection::(async_)query`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/query.html). Text queries are run with a single interaction with the server, but can't have parameters.  
* [Prepared statements](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/prepared_statements.html) are server-side objects. You prepare them using [`connection::(async_)prepare_statement`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/prepare_statement.html). The server returns us an identifier, which is stored in the [`statement`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__statement.html) object. You can then execute them as many times as you want, using [`connection::(async_)execute_statement`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/execute_statement.html). When you're done with a statement, you can instruct the server to de-allocate it using [`connection::(async_)close_statement`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/close_statement.html). **Note that `statement`'s destructor does not close the statement**, since this is an async operation that can fail.  
  
The MySQL server has a limit on how many prepared statements you can have open for a single connection. This is given by the [`max_prepared_stmt_count`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_prepared_stmt_count) server global variable. By default, you can have 16382 open statements per connection. The error you are getting is a server error, described [here](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html#error_er_max_prepared_stmt_count_reached). It means that your session is attempting to open more statement handles than what your server allows.  
  
First of all, I'd check that you're correctly calling `connection::(async_)close_statement` once you don't need statements anymore - if you don't and you keep preparing statements, your program is essentially leaking resources.  
  
If you're correctly closing statements, but you're still reaching the server limit, you can try the following:  
* If you've got control of the server, you can increase the statement handle limit by setting the global variable `max_prepared_stmt_count`. For example, `SET GLOBAL max_prepared_stmt_count = 50000`.  
* Answering your question, if your SQL doesn't have parameters, you don't need a statement. You can use `connection::(async_)query`, instead. You pass it a text string and the server will run it.  
* Instead of creating and destroying statements dynamically, you can try to prepare all the statements you may use beforehand, when you establish the session. When handling requests, you would only execute, but not prepare, them.  
  
Answering your last questions:  
  
> Can I explicitly construct the boost::mysql::statement from string type?  
  
No, you can't, because `statement` represents a server-side handle. But you can accomplish what you're looking for using `connection::query(string_view)` instead.  
  
> The function api connection.async_execute seems to be implemented only in the [include/boost](https://github.com/boostorg/mysql/tree/master/include/boost) library here in GitHub but not in the official Boost-1.8.2  
`connection::execute` is a newer function which is not present in the 1.82 release. Functionally, it's equivalent to `connection::execute_statement`. If you're using 1.82, you should use `connection::execute_statement`, not `connection::execute`.  
  
`WritableFieldTuple` doesn't have a definition because it's a concept. Any `std::tuple<T1, T2...>` of primitive or string types will be accepted by `connection::execute_statement`. The exact concept definition is [here](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__FieldLikeTuple.html). You have [an example on how to use it here](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/prepared_statements.html#mysql.prepared_statements.executing_a_statement). You shouldn't be able to include the master branch as a third party use in any case. If `param1` and `param2` are the actual parameters you want for your statement `stmt`, you can use:   
  
```  
    int param1 = /* obtain it from wherever */;  
    std::string param2 = /* obtain it from wherever */ ;  
    boost::mysql::results result;  
    conn.execute_statement(stmt, std::make_tuple(param1, param2), result);  
```  
  
> Is the connection pool feature ready to use now?  
  
Unfortunately not yet. I wrote a proof-of-concept but it's still missing some work. I don't think it will be able to make 1.83 but it will probably make 1.84.  
  
Out of curiosity, what is a load generator?  
  
If you include me some examples of your SQL queries, I may be able to provide further guidance.  
  
Please let me know if this solved your issue. If you have any other question, please contact me =)  
  
Regards,  
Ruben.

---

## Comment 2

> Username: tonyyxliu  
> Created at: 2023-06-01 17:05:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/157#issuecomment-1572445555  

Hi @anarthal   
I am so grateful for your timely and detailed reply, and that perfectly solves my issue. For your question,  
  
> Out of curiosity, what is a load generator?  
  
The word "load" can be considered as "burden on the server", refering to queries or requests in practice. A load generator aims to generate certain burden on the server (for example, 10k SELECT queries per second) and collect the latency, mainly for testing and analyzing the performance and robustness of the server.  
  
Thank you again very much for your answer! It really helps a lot, and I should close this issue as completed.  
  
Sincerely,  
Yuxuan
