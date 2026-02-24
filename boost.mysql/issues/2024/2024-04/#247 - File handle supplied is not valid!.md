# #247 - File handle supplied is not valid! [Closed]

> Username: sagunkho  
> Created at: 2024-04-03 11:45:09 UTC  
> Updated at: 2024-04-03 15:06:25 UTC  
> Closed at: 2024-04-03 15:06:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/247  

I'm using Boost.MySQL with boost version 1.84. I want to use the connection so I've created a class and added a connection member variable. I acquire a connection from the MySQL server and save the connection in the class. What happens next is that when I access the connection, I get a file handle error.  
  
```  
The file handle supplied is not valid [system:10009]  
```  
  
The code relevant to the saving of the connection is as follows -  
  
```  
  
	void initialize(std::string host, int port, std::string user, std::string pass, std::string database)  
	{  
		try {  
    		boost::asio::io_context ctx;  
    		boost::asio::ssl::context ssl_ctx(boost::asio::ssl::context::tls_client);  
    		_connection = std::make_shared<boost::mysql::tcp_ssl_connection>(ctx.get_executor(), ssl_ctx);  
    		boost::asio::ip::tcp::resolver resolver(ctx.get_executor());  
    		auto endpoints = resolver.resolve(host, std::to_string(port));  
    		boost::mysql::handshake_params params(user, pass, database);  
    		_connection->connect(*endpoints.begin(), params);  
		} catch (boost::mysql::error_with_diagnostics &error) {  
			HLog(error) << error.what();  
		}  
  
		bool value = _is_initialized;  
		_is_initialized.compare_exchange_strong(value, true);  
	}  
  
	std::shared_ptr<boost::mysql::tcp_ssl_connection> get_connection() { return _connection; }  
```  
  
The `_connection` variable is saved in the class and I can later call a small function to test the connection -  
  
```  
  
	std::shared_ptr<boost::mysql::tcp_ssl_connection> get_database_connection()   
	{   
		return get_component<DatabaseProcess>(DATABASE_MAINFRAME)->get_connection();  
	}  
      
	bool test_database_connection()  
	{  
		try {  
    		const char *sql = "SELECT 'Hello World!'";  
    		boost::mysql::results result;  
			get_database_connection()->execute(sql, result);  
  
			if (result.rows().at(0).at(0).as_string().compare("Hello World!") == 0)  
				return true;  
		} catch (boost::mysql::error_with_diagnostics &error) {  
			HLog(error) << error.what();  
		}  
		return false;  
	}  
```  
The issue arises in calling the function. It fails on the `execute` step, but if I perform the execute within the function where the connection was created, it works.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-04-03 13:13:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/247#issuecomment-2034574620  

The `io_context` and `ssl::context` objects must be kept alive as long as you're using the connection. Try this:  
  
```  
struct connection_block {  
    boost::asio::io_context ctx;  
    boost::asio::ssl::context ssl_ctx {boost::asio::ssl::context::tls_client};  
    boost::mysql::tcp_ssl_connection conn {ctx.get_executor(), ssl_ctx};  
};  
  
void initialize(std::string host, int port, std::string user, std::string pass, std::string database)  
{  
  try {  
      _connection = std::make_shared<connection_block>();  
      boost::asio::ip::tcp::resolver resolver(_connection->ctx.get_executor());  
      auto endpoints = resolver.resolve(host, std::to_string(port));  
      boost::mysql::handshake_params params(user, pass, database);  
      _connection->conn.connect(*endpoints.begin(), params);  
  } catch (boost::mysql::error_with_diagnostics &error) {  
	  HLog(error) << error.what();  
  }  
  
bool value = _is_initialized;  
_is_initialized.compare_exchange_strong(value, true);  
}  
  
boost::mysql::tcp_ssl_connection& get_connection() { return _connection->conn; }  
  
```

---

## Comment 2

> Username: sagunkho  
> Created at: 2024-04-03 15:01:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/247#issuecomment-2034864107  

@anarthal Thanks, I'm using a global `io_context` now and it works well.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-04-03 15:06:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/247#issuecomment-2034875510  

That's the intended use. Remember to keep the SSL context alive, too. Recall also that a single io_context and ssl::context can be used to create as many connections as you want.
