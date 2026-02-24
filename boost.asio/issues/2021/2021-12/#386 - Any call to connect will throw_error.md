# #386 - Any call to connect will throw_error [Open]

> Username: ksullivanL3  
> Created at: 2021-12-30 15:58:14 UTC  
> Updated at: 2021-12-30 16:07:01 UTC  
> Url: https://github.com/boostorg/asio/issues/386  

in boost/asio/impl/connect.hpp:line 115, throw_error will be called no matter if there is an error or not. Should the call to this method stay in the call on the previous line _forever_ so that this throw is never reached?  
  
Example code to reproduce:  
    boost::asio::io_context io;  
	boost::asio::ip::tcp::resolver resolver(io);  
	  
	std::string service_name("daytime");  
	boost::asio::ip::tcp::resolver::results_type endpoints =  
      resolver.resolve(argv[1], service_name);  
	    
	boost::asio::ip::tcp::socket socket(io);  
	std::cout << "Connecting socket with endpoint(s)." << std::endl;  
    boost::asio::connect(socket, endpoints);  // ERROR thrown here
