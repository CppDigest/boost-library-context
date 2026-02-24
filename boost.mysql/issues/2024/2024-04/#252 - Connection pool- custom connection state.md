# #252 - Connection pool: custom connection state [Open]

> Username: Chrys4lisfag  
> Created at: 2024-04-22 19:49:31 UTC  
> Updated at: 2024-06-22 09:13:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/252  

It is cool to see connection pool feature being developed right now!  
  
Currently I use my own very simple implementation and as I have full control of the pooled connection, I try to prepare some statements on connection init and later reuse them from the pooled connection.  
  
Taking brief look it seams that I am will not able to save cached statement and reuse it in my next request as:  
1. I can get different connection which won't do as "A prepared statement is specific to the session in which it was created"  
2. Internal reconnection which I do not control and statement could be deallocated.  
  
If I understood correctly about these issues currently couldn't be resolved for caching statements myself, could I do feature request to add optional internal statements caching, so when we do prepare statement it could cache on first use\pop from cache. And may be some control for caching strategy always\frequent\manually selected

---

## Comment 1

> Username: anarthal  
> Created at: 2024-04-23 09:01:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/252#issuecomment-2071783746  

Hi! Glad to see people using connection pooling already.  
  
Yes, you understood correctly. There are a number of workarounds you can use right now - let me know if any of these suit your needs:  
- There are two causes of losing prepared statements: reconnection and session reset. If you are making sure to never leave session state leftovers (e.g. uncommitted transactions), then you can use `pooled_connection::return_without_reset` to opt out from session reset. You'd still need to re-prepare statements on reconnection. When you finish preparing statements, you can set a variable of your choice (e.g. `SET @statements_have_been_prepared = 1`). When you retrieve a connection, query this variable. (i.e. `SELECT @statements_have_been_prepared`). If it's `NULL`, you need to re-prepare. As a workaround, you can store statements on your side in a mapping structure, from `any_connection*` to a `vector<statement>`. This is using an implementation detail (the fact that `any_connection` objects have stable addresses), which is not ideal, but that's likely not changing in the near future.  
- You can stop using statements and switch to text queries. This hasn't been possible until this release, which has introduced first-class support for dynamic SQL via [client-side SQL formatting](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/sql_formatting.html). Depending on your queries, this may be faster or slower than statements, so you'd need to measure. The general rule of thumb is that, unless you're retrieving lots of data, text queries are usually faster than preparing and then executing a statement.  
  
There's a number of things I can do on my side to make your use case better. Here are some suggestions, let me know what you think of these:  
  
- I can implement pipeline mode (see https://github.com/boostorg/mysql/issues/75), which would allow you to prepare statements in batch. My expectation is that preparing a batch of N statements with this feature will take a similar amount of time as preparing a single statement right now.  
- If I implement the latter, I can add a pipeline parameter to `pool_params`, where you can specify the statement to be prepared on connection initialization, and a function in `pooled_connection` to retrieve the results of the pipeline (and thus obtaining the statement handles). Depending on how much typing pipeline objects get, I may need to template `pool_params`, `connection_pool` and `pooled_connection`.  
- In a similar fashion, I could allow passing a user-defined `ConnectionState` type to the pool, such that every connection gets an instance of `ConnectionState`. `ConnectionState` would allow running arbitrary async initialization code after a successful connection:  
  
```cpp  
struct MyConnectionState {  
    // Store here any required state  
    std::vector<statement> statements;  
      
    // This would be called by the pool to initialize a connection: after reconnection and after session reset  
    // Writing this may not be as easy as you could imagine  
    template <class CompletionToken>  
    auto async_initialize(any_connection& conn, CompletionToken&& token) {  
        statements.clear();  
        return conn.async_prepare_statement("SELECT ...", asio::deferred([&](error_code ec, statement st) {  
            if (!ec) statements.push_back(st);  
            return asio::deferred.values(ec);  
        }));  
    }  
};  
```  
  
I'm reluctant to embed specific caching strategies in the code, as they are usually quite application specific.  
  
Let me know which alternative seems more attractive to you, I can provide more info as required.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: Chrys4lisfag  
> Created at: 2024-04-23 16:30:11 UTC  
> Updated at: 2024-04-23 16:32:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/252#issuecomment-2072876702  

Using client-side SQL formatting - Based on the warning in the documentation and my understanding, this feature does not protect against possible SQL injections, which is one of the reasons I use prepared statements for more secure code.  
  
As for your suggestions, it would be ideal to have the ability to store custom connection states with an initializer function, so we wouldn't have to map the connection address to the state.  
  
Regarding the pipeline mode, I am not familiar with its usage, but if it provides the possibility to perform bulk operations, I will certainly explore how to construct and process those bulk requests.  
  
And regarding workaround with setting variable, it will lead to extra network overhead (in my case not all the servers that use the db are in the single network).  
  
I am very grateful for your such detailed and helpful response!

---

## Comment 3

> Username: anarthal  
> Created at: 2024-04-23 17:34:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/252#issuecomment-2073000184  

It actually does protect. When you format a string using `format_sql`, any string values will be quoted and escaped as required. This functionality is similar in scope to https://github.com/mysqljs/sqlstring.  
  
Now, the warning is there because it should still be used with caution. Functions like `format_sql_to`, if combined with very complex logic, can end in vulnerabilities - not because of the function not escaping its input, but because of logic errors.  
  
Queries like  
  
```  
string q = format_sql(conn.format_opts().value(), "SELECT id FROM employee WHERE name = {}", name);  
```  
  
Are safe.

---

## Comment 4

> Username: Chrys4lisfag  
> Created at: 2024-04-23 17:59:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/252#issuecomment-2073050762  

> It actually does protect. When you format a string using `format_sql`, any string values will be quoted and escaped as required. This functionality is similar in scope to https://github.com/mysqljs/sqlstring.  
>   
> Now, the warning is there because it should still be used with caution. Functions like `format_sql_to`, if combined with very complex logic, can end in vulnerabilities - not because of the function not escaping its input, but because of logic errors.  
>   
> Queries like  
>   
> ```  
> string q = format_sql(conn.format_opts().value(), "SELECT id FROM employee WHERE name = {}", name);  
> ```  
>   
> Are safe.  
  
Thanks for the clarification, then I might really switch to it with internal connection pooling for now and if statements caching ability is added, I will switch back depending on performance difference in my case!
