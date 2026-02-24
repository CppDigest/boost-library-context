# #315 - Access violation for simple code [Closed]

> Username: sagunkho  
> Created at: 2024-07-12 15:59:46 UTC  
> Updated at: 2024-07-23 16:59:13 UTC  
> Closed at: 2024-07-23 16:59:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/315  

I have the following code which encompasses a boost::mysql connection using a shared_ptr. I want to keep the connection object for the duration of the application's runtime. I want the application to then close the connection on the end of the runtime. However, I get an access violation when debugging the process.  
```cpp  
class DatabaseProcess : public MainframeComponent  
{  
public:  
	// MainframeComponent dispatch module type is set to Main because DatabaseProcess doesn't run on its own thread.  
	DatabaseProcess(boost::asio::io_context &io_context) : _io_context(io_context), MainframeComponent(Horizon::System::RUNTIME_DATABASE) { }  
	~DatabaseProcess()   
	{   
		// Close connection object.  
		if (_connection != nullptr) {  
			_connection->close();  
			_connection.reset();  
		}  
  
	}  
	void initialize(int segment_number = 1) override { HLog(error) << "Database not configured"; }  
	void initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database)  
	{  
		try {  
    		boost::asio::ssl::context ssl_ctx(boost::asio::ssl::context::tls_client);  
    		_connection = std::make_shared<boost::mysql::tcp_ssl_connection>(_io_context.get_executor(), ssl_ctx);  
    		boost::asio::ip::tcp::resolver resolver(_io_context.get_executor());  
    		auto endpoints = resolver.resolve(host, std::to_string(port));  
    		boost::mysql::handshake_params params(user, pass, database);  
    		_connection->connect(*endpoints.begin(), params);  
		} catch (boost::mysql::error_with_diagnostics &error) {  
			HLog(error) << error.what();  
		}  
  
		bool value = _is_initialized;  
		_is_initialized.compare_exchange_strong(value, true);  
	}  
  
	void finalize(int segment_number = 1) override   
	{  
		bool value = _is_initialized;  
		_is_initialized.compare_exchange_strong(value, false);  
	}  
  
	std::shared_ptr<boost::mysql::tcp_ssl_connection> get_connection() { return _connection; }  
  
	bool is_initialized() { return _is_initialized.load(); }  
protected:  
    std::shared_ptr<boost::mysql::tcp_ssl_connection> _connection{nullptr};  
	boost::asio::io_context &_io_context;  
	std::atomic<bool> _is_initialized;  
};  
```  
  
Debug output:  
```cpp  
  
Running 1 test case...  
  
*** No errors detected  
Exception thrown at 0x00007FFA63663FAA (ntdll.dll) in ZoneSystemTest.exe: 0xC0000005: Access violation writing location 0x0000000000000024.  
The program '[27192] ZoneSystemTest.exe' has exited with code 0 (0x0).  
```  
 The lines responsible significantly are -  
```cpp  
_connection = std::make_shared<boost::mysql::tcp_ssl_connection>(_io_context.get_executor(), ssl_ctx);  
  
```  
When commented, the error goes away. Is it because we can't create a resource in memory of the connection object?

---

## Comment 1

> Username: sagunkho  
> Created at: 2024-07-12 17:28:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/315#issuecomment-2226014942  

The io_context belonged to a class which was the child of the class that holds the DatabaseProcess. So the answer was to change the io_context to be a member of that class and making sure that the destruction of DatabaseProcess occurs before the destruction of that class.

---

## Comment 2

> Username: anarthal  
> Created at: 2024-07-12 18:50:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/315#issuecomment-2226180438  

Correct. `ssl::context` must also be kept alive for the lifetime of the connection. I suggest that you use the same strategy that you're using for the `io_context`. Sharing SSL contexts between connections is safe.

---

## Comment 3

> Username: sagunkho  
> Created at: 2024-07-23 15:02:59 UTC  
> Updated at: 2024-07-23 15:05:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/315#issuecomment-2245501381  

I have encountered an error I think when creating and destroying a database connection using local io_context and ssl_contexts. I've kept `ssl::context` and `io_context` both alive and owned by the DatabaseProcess object.  
Please see the minimal reproducible example below -  
  
```cpp  
  
class DatabaseProcess : public MainframeComponent  
{  
public:  
	// MainframeComponent dispatch module type is set to Main because DatabaseProcess doesn't run on its own thread.  
	DatabaseProcess() : MainframeComponent(Horizon::System::RUNTIME_DATABASE) { }  
	~DatabaseProcess()   
	{   
	}  
	  
	void initialize(int segment_number = 1) override { HLog(error) << "Database not configured"; }  
	void initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database);  
  
	void finalize(int segment_number = 1) override   
	{  
		// Close connection object.  
		if (_connection != nullptr) {  
			_connection->close();  
		}  
		  
		bool value = _is_initialized;  
		_is_initialized.compare_exchange_strong(value, false);  
	}  
  
	std::shared_ptr<boost::mysql::tcp_ssl_connection> get_connection() { return _connection; }  
  
	bool is_initialized() { return _is_initialized.load(); }  
protected:  
	std::shared_ptr<boost::asio::ssl::context> _ssl_ctx{nullptr};  
    std::shared_ptr<boost::mysql::tcp_ssl_connection> _connection{nullptr};  
	boost::asio::io_context _db_io_context;  
	std::atomic<bool> _is_initialized;  
};  
  
void DatabaseProcess::initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database)  
{  
	try {  
		_ssl_ctx = std::make_shared<boost::asio::ssl::context>(boost::asio::ssl::context::tls_client);  
		_connection = std::make_shared<boost::mysql::tcp_ssl_connection>(_db_io_context.get_executor(), *_ssl_ctx);  
		boost::asio::ip::tcp::resolver resolver(_db_io_context.get_executor());  
		auto endpoints = resolver.resolve(host, std::to_string(port));  
		boost::mysql::handshake_params params(user, pass, database);  
		_connection->connect(*endpoints.begin(), params);  
	} catch (boost::mysql::error_with_diagnostics &error) {  
		HLog(error) << error.what();  
	}  
  
	bool value = _is_initialized;  
	_is_initialized.compare_exchange_strong(value, true);  
}  
```  
[![enter image description here][1]][1]  
  
  
  [1]: https://i.sstatic.net/tC8jviSy.png

---

## Comment 4

> Username: anarthal  
> Created at: 2024-07-23 16:18:27 UTC  
> Updated at: 2024-07-23 16:18:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/315#issuecomment-2245675770  

Member destructors in C++ run in reverse order of declaration. What's happening here is:  
- `DatabaseProcess::~DatabaseProcess()` gets called.  
- The `io_context` gets destroyed first.  
- The `tcp_ssl_connection` gets destroyed next, which holds a reference to the `io_context` that has just been destroyed, hence crashing.  
  
The easiest solution is to change the order of your members to make the `io_context` the first one.  
  
That being said, I'd reorder how you're storing your members there. If you need `shared_ptr` semantics on your connection, they need to apply to the `ssl::context` and the `io_context` as well. That is, the reference count for the three objects should be the same. For instance:  
  
```cpp  
class DatabaseProcess : public MainframeComponent  
{  
public:  
	// MainframeComponent dispatch module type is set to Main because DatabaseProcess doesn't run on its own thread.  
	DatabaseProcess() : MainframeComponent(Horizon::System::RUNTIME_DATABASE) { }  
	~DatabaseProcess()   
	{   
	}  
	  
	void initialize(int segment_number = 1) override { HLog(error) << "Database not configured"; }  
	void initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database);  
  
	// Groups an io_context, ssl_context and connection. Order here is relevant  
    struct DbConnection {  
        boost::asio::io_context ctx;  
		boost::asio::ssl::context ssl_ctx {boost::asio::ssl::context::tlsv13_client};  
		boost::mysql::tcp_ssl_connection conn {ctx, ssl_ctx};  
    };  
  
	void finalize(int segment_number = 1) override   
	{  
		// Close connection object.  
		if (_connection != nullptr) {  
			_connection->conn.close();  
		}  
		_connection = nullptr;  
	}  
  
	std::shared_ptr<DbConnection> get_connection() { return _connection; }  
  
	bool is_initialized() { return _connection != nullptr; }  
protected:  
	std::shared_ptr<DbConnection> _connection;  
};  
  
void DatabaseProcess::initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database)  
{  
	try {  
		_conn = std::make_shared<DbConnection>();  
		boost::asio::ip::tcp::resolver resolver(_conn.ctx);  
		auto endpoints = resolver.resolve(host, std::to_string(port));  
		boost::mysql::handshake_params params(user, pass, database);  
		_connection->conn.connect(*endpoints.begin(), params);  
	} catch (boost::mysql::error_with_diagnostics &error) {  
		HLog(error) << error.what();  
	}  
}  
```  
  
Another option is to apply the `shared_ptr` semantics to `DatabaseProcess` itself. I don't know your application enough to know whether this would be doable or not, but in my mind, it could be something like:  
  
```cpp  
class DatabaseProcess : public MainframeComponent  
{  
public:  
	// MainframeComponent dispatch module type is set to Main because DatabaseProcess doesn't run on its own thread.  
	DatabaseProcess() : MainframeComponent(Horizon::System::RUNTIME_DATABASE) { }  
	~DatabaseProcess()   
	{   
	}  
	  
	void initialize(int segment_number = 1) override { HLog(error) << "Database not configured"; }  
	void initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database);  
  
	void finalize(int segment_number = 1) override   
	{  
		// Close connection object.  
		if (_is_initialized) {  
			_connection.close();  
			_is_initialized = false;  
		}  
	}  
  
	std::shared_ptr<boost::mysql::tcp_ssl_connection> get_connection() { return _connection; }  
  
	bool is_initialized() { return _is_initialized.load(); }  
protected:  
	boost::asio::io_context _db_io_context;  
	boost::asio::ssl::context _ssl_ctx{boost::asio::ssl::context::tlsv13_client};  
    boost::mysql::tcp_ssl_connection _connection{_db_io_context, _ssl_ctx};  
	bool _is_initialized {false};  
};  
  
void DatabaseProcess::initialize(int segment_number, std::string host, int port, std::string user, std::string pass, std::string database)  
{  
	try {  
		boost::asio::ip::tcp::resolver resolver(_db_io_context.get_executor());  
		auto endpoints = resolver.resolve(host, std::to_string(port));  
		boost::mysql::handshake_params params(user, pass, database);  
		_connection.connect(*endpoints.begin(), params);  
		_is_initialized = true;  
	} catch (boost::mysql::error_with_diagnostics &error) {  
		HLog(error) << error.what();  
	}  
}  
  
// somewhere in your application  
void createDatabaseProcess()  
{  
	// Apply std::shared_ptr semantics to DatabaseProcess instead of individual connections  
	auto db = std::make_shared<DatabaseProcess>();  
}  
```  
  
Also, is there a reason why you're using `std::atomic` there? Bear in mind that `tcp_ssl_connection` is not thread-safe, so if you're sharing it between threads, you'll need some sort of synchronization.

---

## Comment 5

> Username: sagunkho  
> Created at: 2024-07-23 16:59:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/315#issuecomment-2245765181  

Thanks @anarthal I've managed to fix it thanks to you,
