# #1592 - message code invalid (must be MC) [Closed]

> Username: mikepage  
> Created at: 2019-04-23 22:03:38 UTC  
> Updated at: 2019-04-23 22:36:08 UTC  
> Closed at: 2019-04-23 22:36:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1592  

### Version of Beast  
Version 1.69  
  
### Steps necessary to reproduce the problem  
This exception is thrown when subscribing to the pusher websocket connection. It could be this service uses non standard HTTP codes? [Documentation](https://pusher.com/docs/channels/library_auth_reference/pusher-websockets-protocol#websocket-connection)  
  
```  
terminate called after throwing an instance of 'std::invalid_argument'  
  what():  message code invalid (must be MC)  
```  
  
main.ccp file  
```  
#include <boost/algorithm/hex.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/stream.hpp>  
#include <boost/lexical_cast.hpp>  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
  
#include <openssl/hmac.h>  
  
#include <chrono>  
#include <cstdlib>  
#include <iostream>  
#include <iterator>  
#include <map>  
#include <sstream>  
#include <string>  
#include <thread>  
#include <vector>  
  
using tcp = boost::asio::ip::tcp;  
namespace ssl = boost::asio::ssl;  
namespace websocket = boost::beast::websocket;  
  
using namespace std;  
  
class TerminalService  
{  
public:  
	TerminalService(  
		boost::asio::io_context& ioc,  
		ssl::context& ctx,  
		std::string const& port,  
		std::string const& tid,  
		std::string const& key,  
		std::string const& secret) :  
		resolver_(ioc),  
		ws_(ioc, ctx),  
		port_(port),  
		tid_(tid),  
		key_(key),  
		secret_(secret),  
		channel_("private-terminal-" + tid)  
	{  
		// Create websocket client  
		try  
		{  
			// Look up the domain name  
			auto const results = resolver_.resolve("ws-eu.pusher.com", "443");  
  
			// Make the connection on the IP address we get from a lookup  
			boost::asio::connect(ws_.next_layer().next_layer(), results.begin(), results.end());  
  
			// Perform the SSL handshake  
			ws_.next_layer().handshake(ssl::stream_base::client);  
  
			// Perform the websocket handshake  
			ws_.handshake("wss://ws-eu.pusher.com", "/app/" + key_ + "?protocol=7&client=cpp");  
  
			// This buffer will hold the incoming message  
			boost::beast::multi_buffer b;  
  
			// Read messages and parse json  
			for (;;) {  
				ws_.read(b);  
  
				// Read and output message  
				std::string msg = boost::beast::buffers_to_string(b.data());  
				std::cout << msg << std::endl;  
  
				// Clear buffer  
				b.consume(b.size());  
  
				// Parse json  
				boost::property_tree::ptree pt;  
				boost::iostreams::array_source as(&msg[0], msg.size());  
				boost::iostreams::stream<boost::iostreams::array_source> is(as);  
				boost::property_tree::read_json(is, pt);  
				boost::property_tree::ptree props = pt.get_child("");  
  
				// Set event type and data  
				std::string eventType = "";  
				std::string eventData = "";  
  
				// Get properties  
				for (const auto& prop : props) {  
  
					// Set event  
					if (prop.first == "event") {  
						eventType = pt.get<std::string>("event");  
					}  
  
					// Set data  
					if (prop.first == "data") {  
						eventData = pt.get<std::string>("data");  
  
						// Parse json data  
						boost::property_tree::ptree ptData;  
						boost::iostreams::array_source asData(&eventData[0], eventData.size());  
						boost::iostreams::stream<boost::iostreams::array_source> isData(asData);  
						boost::property_tree::read_json(isData, ptData);  
						boost::property_tree::ptree child = ptData.get_child("");  
  
						// Set event data  
						std::map<std::string,std::string> data;  
  
						for (const auto& item : child) {  
							data[item.first] = ptData.get<std::string>(item.first);  
						}  
  
						// Hit the subscribe  
						if (eventType == "pusher:connection_established") {  
							this->subscribe(data["socket_id"]);  
						}  
					}  
				}  
			}  
  
			// Close the WebSocket connection  
			ws_.close(websocket::close_code::normal);  
		} catch(std::exception const& e) {  
			std::cerr << "Error: " << e.what() << std::endl;  
		}  
	}  
  
	void subscribe(std::string const& socket)  
	{  
		// Write json data  
		boost::property_tree::ptree pt;  
		boost::property_tree::ptree data;  
  
		// Get auth string  
		std::string auth = this->auth(key_, secret_, socket, channel_);  
  
		data.put("auth", auth);  
		data.put("channel", channel_);  
  
		pt.put("event", "pusher:subscribe");  
		pt.add_child("data", data);  
  
		std::stringstream ss;  
		boost::property_tree::json_parser::write_json(ss, pt);  
		std::string json = ss.str();  
  
		std::cout << json << std::endl;  
		ws_.write(boost::asio::buffer(json));  
	}  
  
	std::string auth(  
		std::string const& key,  
		std::string const& secret,  
		std::string const& socket,  
		std::string const& channel)  
	{  
		std::string signature = socket + ":" + channel;  
  
		std::vector<uint8_t> secretVector(secret.begin(), secret.end());  
		std::vector<uint8_t> signatureVector(signature.begin(), signature.end());  
  
		unsigned int len = 32;  
		unsigned char hash[32];  
		size_t secretSize = secretVector.size();  
		size_t signatureSize = signatureVector.size();  
  
		HMAC_CTX hmac;  
		HMAC_CTX_init(&hmac);  
		HMAC_Init_ex(&hmac, (unsigned char*)secretVector.data(), secretSize, EVP_sha256(), NULL);  
		HMAC_Update(&hmac, (unsigned char*)signatureVector.data(), signatureSize);  
		HMAC_Final(&hmac, hash, &len);  
		HMAC_CTX_cleanup(&hmac);  
  
		std::vector<uint8_t> v((uint8_t*)hash,(uint8_t*)hash+32);  
  
		std::string digest;  
		digest.reserve(v.size() * 2);  
  
		static constexpr char hex[] = "0123456789abcdef";  
  
		for (uint8_t c : v) {  
			digest.push_back(hex[c / 16]);  
			digest.push_back(hex[c % 16]);  
		}  
  
		return key + ":" + digest;  
	}  
  
private:  
	tcp::resolver resolver_;  
	websocket::stream<ssl::stream<tcp::socket>> ws_;  
	std::string const& port_;  
	std::string const& tid_;  
	std::string const& key_;  
	std::string const& secret_;  
	std::string const& channel_;  
};  
  
// Main service  
int main(int argc, char* argv[])  
{  
	// Set configuration values  
	if(argc != 5)  
	{  
		std::cerr <<  
			"Usage: terminal-server <port> <tid> <key> <secret>\n";  
		return EXIT_FAILURE;  
	}  
  
	auto const port = argv[1];  
	auto const tid = argv[2];  
	auto const key = argv[3];  
	auto const secret = argv[4];  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc;  
  
	// The SSL context is required, and holds certificates  
	ssl::context ctx{ssl::context::tlsv12_client};  
  
	// Run terminal service  
	TerminalService terminalService(ioc, ctx, port, tid, key, secret);  
  
	return EXIT_SUCCESS;  
}  
```  
  
### All relevant compiler information  
  
Makefile below  
  
```  
CC = g++  
CPPFLAGS += -I include -I /usr/local/include -I /usr/include  
CXXFLAGS += -std=c++1y -Wall -g -D_GLIBCXX_USE_CXX11_ABI=0  
  
LDFLAGS += -L/usr/local/lib \  
           -lboost_date_time \  
           -lboost_thread \  
           -lboost_system \  
           -pthread \  
           -ldl \  
           -lssl \  
           -lcrypto  
  
bin/terminal-server: main.o  
	$(CXX) $(CXXFLAGS) $(CPPFLAGS) $^ $(LDFLAGS) -o $@  
  
clean:  
	rm -f bin/terminal-server main.o  
```  
  
Dockerfile for building  
  
```  
FROM centos:7  
ENV container docker  
  
VOLUME /project  
WORKDIR /project  
  
RUN yum -y update  
RUN yum -y install gcc gcc-c++ make  
RUN yum -y install qt5-qtbase-devel qt5-qtbase-common qt5-qtserialport  
RUN yum -y install wget which  
  
RUN cd ~ && wget https://dl.bintray.com/boostorg/release/1.69.0/source/boost_1_69_0.tar.gz && \  
    mkdir /opt/boost && \  
    tar -xvzf boost_1_69_0.tar.gz -C /opt/boost  
RUN cd /opt/boost/boost_1_69_0 && \  
    ./bootstrap.sh --without-icu --with-libraries=system,thread,coroutine,date_time,program_options,regex,serialization,filesystem,test && \  
    ./b2 clean && ./b2 headers && ./b2 install -j8 --disable-icu  
  
RUN yum -y install libudev-devel openssl-devel  
  
RUN ldconfig  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-23 22:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1592#issuecomment-485993121  

Which line leads to an exception?

---

## Comment 2

> Username: mikepage  
> Created at: 2019-04-23 22:14:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1592#issuecomment-485994576  

> Which line leads to an exception?  
  
This should be ws_.read(b); based on my logging. There is no logger attached so this is the best guess based on the output of the application and the pusher debugging console.

---

## Comment 3

> Username: mikepage  
> Created at: 2019-04-23 22:36:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1592#issuecomment-485999670  

After decompiling another project and boost dependent library I noticed this exception is not thrown in beast. I'm sorry, beast works just great! I'm going to migrate to version 1.70 to get all latest improvements.
