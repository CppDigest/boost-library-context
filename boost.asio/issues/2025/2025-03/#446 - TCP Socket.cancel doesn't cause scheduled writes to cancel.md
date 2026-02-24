# #446 - TCP Socket.cancel doesn't cause scheduled writes to cancel [Open]

> Username: Tasty213  
> Created at: 2025-03-03 15:51:40 UTC  
> Updated at: 2025-03-19 08:30:17 UTC  
> Url: https://github.com/boostorg/asio/issues/446  

Hi,  
  
I'm trying to cancel any pending writes to a socket once realising the socket on the other end has gone away after not responding to messages. I was hoping to use [`basic_stream_socket::cancel`](https://live.boost.org/doc/libs/1_87_0/doc/html/boost_asio/reference/basic_stream_socket/cancel/overload1.html) but after calling it i can still poll the io context and get it to trigger writes i had queued up. I've produced the following unit test demonstrating what i mean.  
  
```  
  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_MAIN  
#define BOOST_TEST_MODULE "C++ Unit Tests for Application"  
#include <boost/test/unit_test.hpp>  
#include <boost/lexical_cast.hpp>  
#include <boost/system/detail/errc.hpp>  
#include <boost/system/detail/error_code.hpp>  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/data/test_case.hpp>  
#include <boost/test/framework.hpp>  
#include <boost/asio.hpp>  
  
using namespace boost::unit_test::framework;  
  
BOOST_AUTO_TEST_SUITE(BoostSocketTest)  
  
BOOST_AUTO_TEST_CASE(OnClearCallsCancelsIOContext) {  
  // Arrange  
  
  // Create a socket and listen on a port  
  boost::asio::io_context child_io_context;  
  boost::asio::ip::tcp::socket child_socket(child_io_context);  
  boost::asio::ip::tcp::endpoint child_endpoint(boost::asio::ip::tcp::v4(), 5001);  
  boost::asio::ip::tcp::acceptor child_acceptor(child_io_context);  
  child_acceptor.open(child_endpoint.protocol());  
  child_acceptor.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
  child_acceptor.bind(child_endpoint);  
  child_acceptor.listen();  
    
  bool accepted = false;  
  child_acceptor.async_accept(  
    child_socket,  
    [&accepted](boost::system::error_code ec)  
    {  
      if (ec) {  
        BOOST_FAIL("failed to listen on child socket");  
      } else {  
        accepted = true;  
      }  
      return;  
    });  
  
  
  // Create another socket and connect it to the other one  
  boost::asio::io_context parent_io_context;  
  boost::asio::ip::tcp::socket parent_socket(parent_io_context);  
  boost::asio::ip::tcp::resolver parent_resolver(parent_io_context);  
  bool connected = false;  
  parent_resolver.async_resolve(  
    "localhost", "5001",  
    [&connected, &parent_socket](const boost::system::error_code ec, boost::asio::ip::tcp::resolver::results_type results)  
    {  
        BOOST_TEST(ec == boost::system::errc::success, "failed to resolve");  
        boost::asio::async_connect(parent_socket, results,  
            [&connected](const boost::system::error_code& ec, const boost::asio::ip::tcp::endpoint& )  
            {  
                BOOST_TEST(ec == boost::system::errc::success, "failed to connect");  
                connected = true;  
            });  
    });  
    
  // Wait for the parent socket to connect to the   
  // child socket once completed the IO Contexts   
  // will have no work left to do so will set their  
  // state to stopped  
  while (!accepted or !connected) {  
    child_io_context.poll();  
    parent_io_context.poll();  
  }  
  
  BOOST_TEST((accepted && connected), "failed to connect or accept as such cannot simulate interrupted write");  
  
  // Queue a message to be sent from the parent socket to the child socket  
  std::string test_data = "test";  
  bool callback_was_called = false;  
  boost::asio::async_write(parent_socket, boost::asio::buffer((uint8_t*)(test_data.data()), test_data.size()),  
      [&callback_was_called](boost::system::error_code ec, size_t) {  
        callback_was_called = true;  
        // Check the reccieved error code is a connection aborted one  
        BOOST_TEST(ec == boost::system::errc::connection_aborted, "callback was not called with boost operation aborted");  
        return;  
      });  
  
  // Restart the IO context as there is more work to do  
  // and fail the test case if it fails to restart  
  parent_io_context.restart();  
  if(parent_io_context.stopped()){  
    BOOST_FAIL("failed to restart parent io context");  
  }  
  
  // Act - cancel the message that was queued to be sent  
  // this should call the callback and mean there is no  
  // work to do at the next poll  
  parent_socket.cancel();  
  
  // Assert - that if you poll the io context it doesn't   
  // send and that the callback was called, if it was  
  // it will already have checked it reccieved a connection  
  // aborted error  
  auto executed = parent_io_context.poll();  
  BOOST_TEST(executed == 0, "Expected the executor to have done no work");  
  BOOST_TEST(callback_was_called == true);  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
Compiled with  
`g++ -o BoostSocket_test -ansi -W -Wall -Wextra -Wno-non-virtual-dtor -std=c++23 -pthread -DNDEBUG -fPIC -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE=1 -ggdb -DBOOST_BIND_GLOBAL_PLACEHOLDERS -lboost_unit_test_framewor  
k BoostSocket_test.cpp`  
Fails the test as such  
  
```  
[root@9f0cfab1ff7b asio]# g++ -o BoostSocket_test -ansi -W -Wall -Wextra -Wno-non-virtual-dtor -std=c++23 -pthread -DNDEBUG -fPIC -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE=1 -ggdb -DBOOST_BIND_GLOBAL_PLACEHOLDERS -lboost_unit_test_framework BoostSocket_test.cpp  
[root@9f0cfab1ff7b asio]# chmod +x BoostSocket_test  
[root@9f0cfab1ff7b asio]# ./BoostSocket_test   
Running 1 test case...  
BoostSocket_test.cpp(83): error: in "BoostSocketTest/OnClearCallsCancelsIOContext": callback was not called with boost operation aborted  
BoostSocket_test.cpp(104): error: in "BoostSocketTest/OnClearCallsCancelsIOContext": Expected the executor to have done no work  
  
*** 2 failures are detected in the test module "C++ Unit Tests for Application"  
```  
  
I've probably just misunderstood what kind of operations get cancelled by a cancel function call, any advice or pointers would be appreciated.  
  
Thanks in advance  
George

---

## Comment 1

> Username: mscottmueller  
> Created at: 2025-03-14 16:55:53 UTC  
> Url: https://github.com/boostorg/asio/issues/446#issuecomment-2725245723  

Cancelling async_write should result in boost::asio::error::operation_aborted as opposed to boost::system::errc::connection_aborted.

---

## Comment 2

> Username: Tasty213  
> Created at: 2025-03-19 08:30:16 UTC  
> Url: https://github.com/boostorg/asio/issues/446#issuecomment-2735722335  

Hi, thanks for spotting that. I've updated the unit test but it's still failing and the "error code" passed to the async write callback is "Success".  
  
Updated unit test is  
  
```  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_MAIN  
#define BOOST_TEST_MODULE "C++ Unit Tests for Application"  
  
  
#include <boost/test/unit_test.hpp>  
#include <boost/lexical_cast.hpp>  
#include <boost/system/detail/errc.hpp>  
#include <boost/system/detail/error_code.hpp>  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/data/test_case.hpp>  
#include <boost/test/framework.hpp>  
#include <boost/asio.hpp>  
  
using namespace boost::unit_test::framework;  
  
BOOST_AUTO_TEST_SUITE(BoostSocketTest)  
  
BOOST_AUTO_TEST_CASE(OnClearCallsCancelsIOContext) {  
  // Arrange  
  
  // Create a socket and listen on a port  
  boost::asio::io_context child_io_context;  
  boost::asio::ip::tcp::socket child_socket(child_io_context);  
  boost::asio::ip::tcp::endpoint child_endpoint(boost::asio::ip::tcp::v4(), 5001);  
  boost::asio::ip::tcp::acceptor child_acceptor(child_io_context);  
  child_acceptor.open(child_endpoint.protocol());  
  child_acceptor.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
  child_acceptor.bind(child_endpoint);  
  child_acceptor.listen();  
  
  bool accepted = false;  
  child_acceptor.async_accept(  
    child_socket,  
    [&accepted](boost::system::error_code ec)  
    {  
      if (ec) {  
        BOOST_FAIL("failed to listen on child socket");  
      } else {  
        accepted = true;  
      }  
      return;  
    });  
  
  
  // Create another socket and connect it to the other one  
  boost::asio::io_context parent_io_context;  
  boost::asio::ip::tcp::socket parent_socket(parent_io_context);  
  boost::asio::ip::tcp::resolver parent_resolver(parent_io_context);  
  bool connected = false;  
  parent_resolver.async_resolve(  
    "localhost", "5001",  
    [&connected, &parent_socket](const boost::system::error_code ec, boost::asio::ip::tcp::resolver::results_type results)  
    {  
        BOOST_TEST(ec == boost::system::errc::success, "failed to resolve");  
        boost::asio::async_connect(parent_socket, results,  
            [&connected](const boost::system::error_code& ec, const boost::asio::ip::tcp::endpoint& )  
            {  
                BOOST_TEST(ec == boost::system::errc::success, "failed to connect");  
                connected = true;  
            });  
    });  
  
  // Wait for the parent socket to connect to the  
  // child socket once completed the IO Contexts  
  // will have no work left to do so will set their  
  // state to stopped  
  while (!accepted or !connected) {  
    child_io_context.poll();  
    parent_io_context.poll();  
  }  
  
  BOOST_TEST((accepted && connected), "failed to connect or accept as such cannot simulate interrupted write");  
  
  // Queue a message to be sent from the parent socket to the child socket  
  std::string test_data = "test";  
  bool callback_was_called = false;  
  boost::asio::async_write(parent_socket, boost::asio::buffer((uint8_t*)(test_data.data()), test_data.size()),  
      [&callback_was_called](boost::system::error_code ec, size_t) {  
        callback_was_called = true;  
        // Check the reccieved error code is a connection aborted one  
        BOOST_TEST(ec == boost::asio::error::operation_aborted, "callback was not called with boost operation aborted error was " + ec.message());  
        return;  
      });  
  
  // Restart the IO context as there is more work to do  
  // and fail the test case if it fails to restart  
  parent_io_context.restart();  
  if(parent_io_context.stopped()){  
    BOOST_FAIL("failed to restart parent io context");  
  }  
  
  // Act - cancel the message that was queued to be sent  
  // this should call the callback and mean there is no  
  // work to do at the next poll  
  parent_socket.cancel();  
  
  // Assert - that if you poll the io context it doesn't  
  // send and that the callback was called, if it was  
  // it will already have checked it reccieved a connection  
  // aborted error  
  auto executed = parent_io_context.poll();  
  BOOST_TEST(executed == 0, "Expected the executor to have done no work");  
  BOOST_TEST(callback_was_called == true);  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
  
again compiled with  
`g++ -o BoostSocket_test -ansi -W -Wall -Wextra -Wno-non-virtual-dtor -std=c++23 -pthread -DNDEBUG -fPIC -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE=1 -ggdb -DBOOST_BIND_GLOBAL_PLACEHOLDERS -lboost_unit_test_framework BoostSocket_test.cpp`  
  
Thanks
