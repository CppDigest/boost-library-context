# #494 - Pool reconnection issue [Open]

> Username: gitSomething  
> Created at: 2025-10-01 15:05:32 UTC  
> Updated at: 2025-10-07 14:21:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/494  

Hi!  
I have following behavior which i dont know how to handle:  
the code is auto conn = co_await pool_.async_get_connection(boost::asio::cancel_after(std::chrono::seconds(5)));  
Once i stop mysql server and try to run it it throws: "MySQL error: asio.misc:2 End of file"  
I start mysql once again and try to connect, now it says "MySQL error: mysql.client:16 Getting a connection from a connection_pool was cancelled before a connection was available"  
But mysql is running.  
And only on third time it connects.  
  
If it works as intended how should i handle it ?

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-01 16:50:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3357263272  

Hi @gitSomething,  
  
First, could you please confirm which Boost version are you using?  
  
I understand that the test you're doing is:  
  
* Start your program. MySQL is healthy at this point.  
* Stop MySQL.  
* While MySQL is stopped, get a connection. This fails with `asio.misc:2`.  
* Start MySQL.  
* Immediately after this, try to get a connection. This fails with `mysql::client_errc::no_connection_available` (aka `mysql.client:16`).  
  
I think that what you're observing is the following sequence of events:  
  
* You start your program.  
* The connection pool creates the connections and attempts to connect them. Since MySQL is healthy, connect succeeds and the connections are marked as healthy.  
* You stop MySQL. The connections are closed. However, there is no way to detect this fact until you attempt a socket operation. So, the pool thinks they are still healthy.  
* You try to get a connection from the pool. This succeeds, but if you try to use it, an error is thrown. That is, it's not `async_get_connection` the one that fails, but the subsequent `async_execute` (or similar). `asio.misc:2` is the kind of error that you get when trying to read from a socket, but the server closed the connection.  
* You return the connection to the pool (this happens automatically when `conn` is destroyed).  
* The pool tries to clean up the connection, finds that it's unhealthy and tries to connect it again. This fails immediately because MySQL is not running.  
* On connection failure, the pool will wait `pool_params::retry_interval` (30 seconds by default) before trying it again.  
* You bring MySQL up.  
* You call `async_get_connection` with a 5 second timeout. The connection is waiting the `retry_interval`, so it's still not healthy. The call fails with `mysql::client_errc::no_connection_available`.  
* You wait some more time. Another connection attempt is made, and the connection connects successfully.  
* You call `async_get_connection` again, and it succeeds.  
  
I don't know the specifics of your test and whether this is compatible with what you're observing. Some things you can do to verify:  
  
* Set `pool_params::retry_interval` to a lower value (say 1 second), and repeat the test. You should still see the 1st error, but not the 2nd one.  
* The only way to detect that a connection is down is by pinging it. By default, the pool pings connection every 1 hour. You can configure this by setting `pool_params::ping_interval`. Set it to 1 second and repeat the test. You shouldn't see any more `asio.misc:2` errors (although you might see `mysql::client_errc::no_connection_available` if the server isn't alive on time).  
  
In general, it's always possible to get a `mysql::client_errc::no_connection_available` if the server is down.  
  
Let me know if this helps.

---

## Comment 2

> Username: gitSomething  
> Created at: 2025-10-01 19:27:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3357834506  

Hi again.  
Thanx for such detailed answer!  
My boost ver is 1.88.  
  
I have another question about prepared statements, usually we want to prepare them all at once at the moment program starts.  
And then use them regularly and free them only when program ends. So we can get performance boost.  
But pool does it immediately as connection goes back to it. And sure it does when it reconnects..  
I assume such optimization is not possible ? Or is there a way to control what pool is freeing and get some flag from connection to know if it was newly established and we need re-prepare statements ?

---

## Comment 3

> Username: anarthal  
> Created at: 2025-10-02 15:27:41 UTC  
> Updated at: 2025-10-02 15:30:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3361817741  

Boost 1.88 has the behavior I described (as does 1.89). Is the information I gave you enough to solve the issue?  
  
Unfortunately, there is no way to re-use prepared statements with connection pools at this time. This is tracked by #252 and #285. What would be your ideal interface regarding this?  
  
Alternatively, you can use `with_params` rather than statements. This uses (secure) client-side SQL query composition, which saves you from preparing statements. You can use `pooled_connection::return_without_reset()` if you do this, and this will make the pool not use `async_reset_connection` when they're returned to the pool, which makes everything faster. See [this section](https://www.boost.org/doc/libs/latest/libs/mysql/doc/html/mysql/connection_pool.html#mysql.connection_pool.session_state) of the docs.  
  
Let me know if you have any comments/ideas.

---

## Comment 4

> Username: gitSomething  
> Created at: 2025-10-02 22:18:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3363405759  

Thank you!  
I think, its enough for me to understand what is going on and how to fix it.  
  
Ideal would be a pool param that will allow to not reset prepared statements, but free all other stuff, and async_get_connection could return a connection with a flag set in case the connection is newly created or reconnected, so we can reinit statements  
  
You have a great library!

---

## Comment 5

> Username: anarthal  
> Created at: 2025-10-03 10:08:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3365130723  

There is no protocol primitive to reset a session without cleaning statements, too. So we either reset everything, or nothing. We have `pooled_connection::return_without_reset()` to avoid doing the reset already. So I think we're missing the flag to reinit statements. You will probably need a way to store the `statement` objects associated to each connection (e.g. a `vector<statement>`), since you will need these to call `async_execute`. So I'd say we need a way to associate an arbitrary data structure to a `pooled_connection`, rather than a single flag.  
  
What do you think of this:  
  
```cpp  
// The per-connection state  
struct ConnState {  
    std::vector<statement> stmts;  
  
    // Called by the pool whenever a connection is reset  
    void reset() {  
         stmts.clear();  
    }  
};  
  
// In your main. connection_pool now needs to be a template to accommodate this struct  
basic_connection_pool<ConnState> pool (ioc);  
pool.async_run(...); // same as before  
  
  
// In your coroutine  
asio::awaitable<void> do_stuff(basic_connection_pool<ConnState>& pool) {  
    // Get a connection. Use auto here, I'm only trying to show the type of conn  
    basic_pooled_connection<ConnState> conn = co_await pool.async_get_connection();  
  
    // Do any setup if required  
    ConnState& state = conn.state();  
    if (state.stmts.empty()) {  
        // This connection is either new or has been reset  
        auto stmt = co_await conn.async_prepare_statement(...); // have a look at pipelines, they might help here  
        state.stmts.push_back(stmt);  
    }  
  
    // Now use the connection normally  
    co_await conn.async_execute(state.stmts[0].bind(...), ...);  
  
    // Don't forget to return the connection like this on success! Otherwise,  
    // the pool will repeatedly reset the statements and performance will be terrible.  
    // If an error was to happen, this function should NOT be called, so the pool cleans up the faulty connection  
    conn.return_without_reset();  
}  
  
```

---

## Comment 6

> Username: anarthal  
> Created at: 2025-10-03 10:10:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3365133796  

(This will probably make connection_pool slower to compile, since it can no longer be type-erased easily).

---

## Comment 7

> Username: gitSomething  
> Created at: 2025-10-03 13:48:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3365751464  

Looks nice to me!  
But what is another state stuff except prepared statements and how big it may grow ?  
does async_execute allocates stuff that needs to be free by session cleanup ?

---

## Comment 8

> Username: anarthal  
> Created at: 2025-10-03 14:07:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3365821152  

`async_execute` does not allocate anything per-se, but certain SQL statements might. For example, consider transactions:  
  
```cpp  
// Start a transaction  
co_await conn.async_execute("START TRANSACTION", ...);  
  
// Something in your code here throws an exception - e.g. a SQL query fails, a vector is accessed out of bounds...  
```  
  
In this example, `conn` is in an open transaction. This counts as connection state. Once the connection is returned to the pool, if the pool does nothing, the next `async_get_connection` will get a connection that is already inside a transaction, without knowing it. This is prone to all kind of bad stuff - for instance, you'd see that your SQL statements do nothing sometimes (because they were never committed).  
  
So, when a connection is returned to the pool, the pool runs [`async_reset_connection`](https://www.boost.org/doc/libs/latest/libs/mysql/doc/html/mysql/ref/boost__mysql__any_connection/async_reset_connection/overload1.html), unless you explicitly used `return_without_reset`. This is essentially you saying "I give my word that the connection is good".  
  
`async_reset_connection` is handled by the server, so we can't change it. It discards things like transactions, table locks, variables, and also prepared statements.  
  
Another user wanted a feature like this, but they preferred a cache - so a `std::map` where you could insert the statement once you've prepared it. So I think it's better to make the type customizable by the user.

---

## Comment 9

> Username: gitSomething  
> Created at: 2025-10-07 14:21:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/494#issuecomment-3377130448  

map would be good for variable amount of different queries sure.  
but we have fixed amount so vector is better in our case.  
you are right - type customizable by the user is the best option.
