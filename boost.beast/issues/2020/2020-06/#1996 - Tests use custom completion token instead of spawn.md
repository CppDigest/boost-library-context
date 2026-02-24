# #1996 - Tests use custom completion token instead of spawn [Open]

> Username: vinniefalco  
> Created at: 2020-06-25 13:45:28 UTC  
> Updated at: 2020-06-25 13:45:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1996  

Some of the tests use stackful coroutines (`boost::asio::spawn`), which creates a risky dependence on an extra component. These tests can be replaced with the style of manually advancing the operations by calling `io_context::run_one` strategically, as is done in some of the newer tests.  
  
To make this even easier, we can introduce a new completion token type for tests which simply advances the I/O context until a condition is met and then returns, turning asynchronous calls into blocking calls. Example:  
  
```  
error_code ec;  
auto bytes_transferred =  
  ws.async_read_some( b, run_for(2)[ec] ); // calls run_one twice  
```
