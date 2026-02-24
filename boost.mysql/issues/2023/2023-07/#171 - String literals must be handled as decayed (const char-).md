# #171 - String literals must be handled as decayed (const char*) [Closed]

> Username: liubing  
> Created at: 2023-07-16 07:09:12 UTC  
> Updated at: 2023-07-17 15:04:27 UTC  
> Closed at: 2023-07-17 15:04:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/171  

Firstly, thank you for providing such a great library.  
  
Because handling timeout is such a common usage, it is natural to wrap it up like this (the code is based on the latest 1.83b):  
```  
template<class T>  
static boost::asio::awaitable<boost::mysql::results> db_execute(boost::mysql::tcp_connection& db_conn, T&& stmt, std::chrono::steady_clock::duration timeout = 8s)  
{  
    boost::mysql::results results;  
    boost::mysql::diagnostics diag;  
    auto r = co_await(  
        db_conn.async_execute(std::forward<T>(stmt), results, diag, use_nothrow_awaitable) ||  
        Timeout(timeout));  
    if (auto p = std::get_if<0>(&r))  
    {  
        auto [ec] = *p;  
        boost::mysql::throw_on_error(ec, diag);  
        co_return results;  
    }  
    else  
    {  
        boost::mysql::throw_on_error(boost::asio::error::operation_aborted);  
    }  
}  
```  
Everything works perfectly except it cannot handle things like this:  
```  
            co_await db_execute(db_conn, "START TRANSACTION");  
```  
But if you pass the string literal as its decayed form, it will work:  
```  
            co_await db_execute(db_conn, (const char*)"START TRANSACTION");  
```  
To work around this problem, I have to add the following overload:  
```  
static boost::asio::awaitable<boost::mysql::results> db_execute(boost::mysql::tcp_connection& db_conn, const char* stmt, std::chrono::steady_clock::duration timeout = 8s)  
{  
    co_return co_await db_execute(db_conn, std::string_view(stmt), timeout);  
}  
```  
I only tested it on MSVC (19.36.32537.0).  
  
The problem has bothered me for days. Why is this happening? Do you have any suggestions?  
  
Thanks in advance.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-07-17 09:49:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1637728232  

I haven't been able to reproduce your issue. [I've created a gist](https://gist.github.com/anarthal/c8786890c1295816197828ee4cc5fca5) with what I've tried, compiling with Boost 1.83 beta 1, CMake and your exact MSVC version.  
  
Can you please try this gist and verify whether it works?  
Can you also please post a minimum but full reproducible example, together with the compiler settings you're using (ideally, including the actual `cl.exe` command line that causes the failure.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: liubing  
> Created at: 2023-07-17 12:12:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638003434  

I've reproduced the issue using the code you provided in the gist, with a few changes:  
1) Changed tcp_ssl_connection to tcp_connection for I'm using MariaDB which uses non-ssl connections by default;  
2) Fill username and password;  
The full source code:  
```  
#include <boost/mysql/diagnostics.hpp>  
#include <boost/mysql/error_with_diagnostics.hpp>  
#include <boost/mysql/handshake_params.hpp>  
#include <boost/mysql/row_view.hpp>  
#include <boost/mysql/tcp.hpp>  
#include <boost/mysql/throw_on_error.hpp>  
  
#include <boost/asio/as_tuple.hpp>  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/experimental/awaitable_operators.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/context.hpp>  
#include <boost/asio/this_coro.hpp>  
#include <boost/asio/use_awaitable.hpp>  
#include <boost/system/system_error.hpp>  
  
#include <exception>  
#include <iostream>  
#include <stdexcept>  
#include <variant>  
  
using boost::mysql::error_code;  
using namespace std::chrono_literals;  
using namespace boost::asio::experimental::awaitable_operators;  
  
void print_employee(boost::mysql::row_view employee)  
{  
    std::cout << "Employee '" << employee.at(0) << " "   // first_name (string)  
        << employee.at(1) << "' earns "            // last_name  (string)  
        << employee.at(2) << " dollars yearly\n";  // salary     (double)  
}  
  
// Using this completion token instead of plain use_awaitable prevents  
// co_await from throwing exceptions. Instead, co_await will return a std::tuple<error_code>  
// with a non-zero code on error. We will then use boost::mysql::throw_on_error  
// to throw exceptions with embedded diagnostics, if available. If you  
// employ plain use_awaitable, you will get boost::system::system_error exceptions  
// instead of boost::mysql::error_with_diagnostics exceptions. This is a limitation of use_awaitable.  
constexpr auto tuple_awaitable = boost::asio::as_tuple(boost::asio::use_awaitable);  
  
template <class T>  
static boost::asio::awaitable<boost::mysql::results> db_execute(  
    boost::mysql::tcp_connection& db_conn,  
    T&& stmt,  
    std::chrono::steady_clock::duration timeout = 8s  
)  
{  
    std::cout << "sizeof(stmt) = " << sizeof(stmt) << std::endl;  
    std::cout << "stmt = " << stmt << std::endl;  
  
    boost::mysql::results results;  
    boost::mysql::diagnostics diag;  
    boost::asio::steady_timer timer{co_await boost::asio::this_coro::executor};  
    timer.expires_after(timeout);  
  
    auto r = co_await(  
        db_conn.async_execute(std::forward<T>(stmt), results, diag, tuple_awaitable) ||  
        timer.async_wait(tuple_awaitable)  
        );  
    if (auto p = std::get_if<0>(&r))  
    {  
        auto [ec] = *p;  
        boost::mysql::throw_on_error(ec, diag);  
        co_return results;  
    }  
    else  
    {  
        throw boost::system::system_error(boost::asio::error::operation_aborted);  
    }  
}  
/**  
 * Our coroutine. It must have a return type of boost::asio::awaitable<T>.  
 * Our coroutine does not communicate any result back, so T=void.  
 * Remember that you do not have to explicitly create any awaitable<void> in  
 * your function. Instead, the return type is fed to std::coroutine_traits  
 * to determine the semantics of the coroutine, like the promise type.  
 * Asio already takes care of all this for us.  
 *  
 * The coroutine will suspend every time we call one of the asynchronous functions, saving  
 * all information it needs for resuming. When the asynchronous operation completes,  
 * the coroutine will resume in the point it was left.  
 *  
 * The return type of an asynchronous operation that uses use_awaitable  
 * as completion token is a boost::asio::awaitable<T>, where T  
 * is the second argument to the handler signature for the asynchronous operation.  
 * If any of the asynchronous operations fail, an exception will be raised  
 * within the coroutine.  
 */  
boost::asio::awaitable<void> coro_main(  
    boost::mysql::tcp_connection& conn,  
    boost::asio::ip::tcp::resolver& resolver,  
    const boost::mysql::handshake_params& params,  
    const char* hostname,  
    const char* company_id  
)  
{  
    error_code ec;  
    boost::mysql::diagnostics diag;  
  
    // Resolve hostname. We may use use_awaitable here, as hostname resolution  
    // never produces any diagnostics.  
    auto endpoints = co_await resolver.async_resolve(  
        hostname,  
        boost::mysql::default_port_string,  
        boost::asio::use_awaitable  
    );  
  
    // Connect to server  
    std::tie(ec) = co_await conn.async_connect(*endpoints.begin(), params, diag, tuple_awaitable);  
    boost::mysql::throw_on_error(ec, diag);  
  
    // We will be using company_id, which is untrusted user input, so we will use a prepared  
    // statement.  
    boost::mysql::statement stmt;  
    std::tie(ec, stmt) = co_await conn.async_prepare_statement(  
        "SELECT first_name, last_name, salary FROM employee WHERE company_id = ?",  
        diag,  
        tuple_awaitable  
    );  
    boost::mysql::throw_on_error(ec, diag);  
  
    // Execute the statement  
    boost::mysql::results result = co_await db_execute(conn, "START TRANSACTION");  
  
    // Print all employees  
    for (boost::mysql::row_view employee : result.rows())  
    {  
        print_employee(employee);  
    }  
  
    // Notify the MySQL server we want to quit, then close the underlying connection.  
    std::tie(ec) = co_await conn.async_close(diag, tuple_awaitable);  
    boost::mysql::throw_on_error(ec, diag);  
}  
  
void main_impl(int argc, char** argv)  
{  
    //if (argc != 4 && argc != 5)  
    //{  
    //    std::cerr << "Usage: " << argv[0] << " <username> <password> <server-hostname> [company-id]\n";  
    //    exit(1);  
    //}  
  
    const char* hostname = "127.0.0.1";  
  
    // The company_id whose employees we will be listing. This  
    // is user-supplied input, and should be treated as untrusted.  
    const char* company_id = argc == 5 ? argv[4] : "HGS";  
  
    // I/O context and connection. We use SSL because MySQL 8+ default settings require it.  
    boost::asio::io_context ctx;  
    //boost::asio::ssl::context ssl_ctx(boost::asio::ssl::context::tls_client);  
    boost::mysql::tcp_connection conn(ctx);  
  
    // Connection parameters  
    boost::mysql::handshake_params params(  
        "user",                // username  
        "user1234",                // password  
        "boost_mysql_examples"  // database to use; leave empty or omit the parameter for no  
                                // database  
    );  
  
    // Resolver for hostname resolution  
    boost::asio::ip::tcp::resolver resolver(ctx.get_executor());  
  
    // The entry point. We pass in a function returning  
    // boost::asio::awaitable<void>, as required.  
    boost::asio::co_spawn(  
        ctx.get_executor(),  
        [&conn, &resolver, params, hostname, company_id] {  
            return coro_main(conn, resolver, params, hostname, company_id);  
        },  
        // If any exception is thrown in the coroutine body, rethrow it.  
        [](std::exception_ptr ptr) {  
            if (ptr)  
            {  
                std::rethrow_exception(ptr);  
            }  
        }  
    );  
  
    // Calling run will execute the requested operations.  
    ctx.run();  
}  
  
int main(int argc, char** argv)  
{  
    try  
    {  
        main_impl(argc, argv);  
    }  
    catch (const boost::mysql::error_with_diagnostics& err)  
    {  
        // You will only get this type of exceptions if you use throw_on_error.  
        // Some errors include additional diagnostics, like server-provided error messages.  
        // Security note: diagnostics::server_message may contain user-supplied values (e.g. the  
        // field value that caused the error) and is encoded using to the connection's encoding  
        // (UTF-8 by default). Treat is as untrusted input.  
        std::cerr << "Error: " << err.what() << '\n'  
            << "Server diagnostics: " << err.get_diagnostics().server_message() << std::endl;  
        return 1;  
    }  
    catch (const std::exception& err)  
    {  
        std::cerr << "Error: " << err.what() << std::endl;  
        return 1;  
    }  
}  
```  
And the output:  
```  
sizeof(stmt) = 18  
stmt = 鹣l  
Error: er_parse_error [mysql.common-server:1064]  
Server diagnostics: You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '?睡?l' at line 1  
```  
If you change the code to:  
```  
    boost::mysql::results result = co_await db_execute(conn, (const char*)"START TRANSACTION");  
```  
The problem goes away, and the output:  
```  
sizeof(stmt) = 8  
stmt = START TRANSACTION  
```  
It seems to me that the problem has nothing to do with mysql library, because stmt is already rubbish when it reaches db_execute().  
  
But I still cannot figure out why.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-07-17 13:26:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638142049  

I'm investigating. The output you're receiving is likely related to Windows failing to figure out that you're outputting UTF-8. It's probably trying to decode your string using your active code page - try running `CHCP 65001` in the terminal before running the program. I'll keep you informed.

---

## Comment 4

> Username: liubing  
> Created at: 2023-07-17 13:35:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638159639  

Thank you. I tried ```CHCP 65001``` but it didn't make a difference. I already use /utf8 compiler option to compile the code.  
  
Also, I noticed the type T is interpreted differently in the two cases:  
1. It is interpreted as const char [18] in the first case.  
2. It is interpreted as const char* in the second case.  
  
Could it be like in the first case (const char [18]) converted in and out into the coroutine context, maybe the content is lost during the way? I'm no coroutine expert, I'm just guessing.  
  
However, what puzzled me is that if you don't wrap it up in db_execute(), but to call   
```  
    conn.async_execute("START TRANSACTION", ...);  
```  
directly, it will run without any problem.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-07-17 14:09:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638228105  

This is looking like a problem with MSVC and/or Boost.Asio when passing reference-to-array parameters to coroutines. An example on how to reproduce it:  
  
```  
  
template <class T, std::size_t N>  
boost::asio::awaitable<void> print(const T (&arr)[N])  
{  
    // arr points to garbage  
    co_await boost::asio::post(co_await boost::asio::this_coro::executor, boost::asio::use_awaitable);  
    for (int i = 0; i < N; ++i)  
    {  
        std::cout << arr[i] << std::endl;  
    }  
}  
  
boost::asio::awaitable<void> mytest()  
{  
    const int param[2] = {41, 42};  
    co_await print(param);  
}  
  
int main(int argc, char** argv)  
{  
    boost::asio::io_context ctx;  
    boost::asio::co_spawn(ctx.get_executor(), &mytest, [](std::exception_ptr ptr) {});  
    ctx.run();  
}  
```  
  
It is not related to Boost.MySQL. I'm going to keep investigating to direct you either to Asio or to MSVC. It doesn't happen on Linux/clang BTW.

---

## Comment 6

> Username: anarthal  
> Created at: 2023-07-17 14:12:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638233797  

As a wordaround for now, suffixing your strings with the `sv` string view literal works, too (the problem is only with plain old C arrays):  
  
```  
using namespace std::string_view_literals;  
  
// ...  
  
boost::mysql::results result = co_await db_execute(conn, "START TRANSACTION"sv);  
```

---

## Comment 7

> Username: liubing  
> Created at: 2023-07-17 14:25:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638261125  

Thank you very much!  
Looking forward to your investigation result.

---

## Comment 8

> Username: anarthal  
> Created at: 2023-07-17 15:04:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/171#issuecomment-1638332741  

I'm pretty sure it's a MSVC problem. Minimum example to reproduce:  
  
```  
#include <array>  
#include <coroutine>  
#include <iostream>  
  
struct Task  
{  
    struct promise_type  
    {  
        using Handle = std::coroutine_handle<promise_type>;  
        Task get_return_object() { return Task{Handle::from_promise(*this)}; }  
        std::suspend_always initial_suspend() { return {}; }  
        std::suspend_never final_suspend() noexcept { return {}; }  
        void return_void() {}  
        void unhandled_exception() {}  
    };  
    explicit Task(promise_type::Handle coro) : coro_(coro) {}  
    void destroy() { coro_.destroy(); }  
    void resume() { coro_.resume(); }  
  
private:  
    promise_type::Handle coro_;  
};  
  
Task myCoroutine(const int (&arr1)[2], const std::array<int, 2>& arr2)  
{  
    std::cout << arr1[0] << ", " << arr1[1] << std::endl;  
    std::cout << arr2[0] << ", " << arr2[1] << std::endl;  
    co_return;  
}  
  
constexpr const int arr1[2] = {41, 42};  
constexpr std::array<int, 2> arr2{41, 42};  
  
int main()  
{  
    auto c = myCoroutine(arr1, arr2);  
    c.resume();  
}  
```  
  
I've submitted a report to them with this repro.  
  
I'm afraid I can't do anything else on my side on this, so if you're happy with the result, I'll be closing the issue.  
  
Regards,  
Ruben.
