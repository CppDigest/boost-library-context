# #643 - Boost syslog backend with IPV6 is not working (boost 1.74 version) [Closed]

> Username: sathishk-r  
> Created at: 2022-04-22 14:03:59 UTC  
> Updated at: 2022-04-22 14:10:47 UTC  
> Closed at: 2022-04-22 14:10:47 UTC  
> Url: https://github.com/boostorg/boost/issues/643  

On Linux platform, I am getting invalid argument error while sending the data to syslog server using boost syslog backend.  
  
from the strace i observed that socket is getting created with ipv6 family, but bind is happening with ipv4 on the ipv6 socket. Please find the below strace output.  
  
socket(PF_INET6, SOCK_DGRAM, IPPROTO_UDP) = 6  
epoll_ctl(4, EPOLL_CTL_ADD, 6, {EPOLLIN|EPOLLPRI|EPOLLERR|EPOLLHUP|EPOLLET, {u32=11136784, u64=11136784}}) = 0  
setsockopt(6, SOL_SOCKET, SO_REUSEADDR, [1], 4) = 0  
bind(6, {sa_family=AF_INET, sin_port=htons(0), sin_addr=inet_addr("0.0.0.0")}, 16) = -1 EINVAL (Invalid argument)  
  
Please find the below sample code  
  
```  
boost::asio::ip::address addr;  
      
	addr = boost::asio::ip::make_address("::1");  
  
        // Create a syslog sink  
        shared_ptr< sinks::synchronous_sink< sinks::syslog_backend > > sink(  
            new sinks::synchronous_sink< sinks::syslog_backend >(keywords::facility =  
                    sinks::syslog::local0, //< the logging facility >  
            keywords::use_impl = sinks::syslog::udp_socket_based, //< the built-in socket-based implementation should be used >  
            keywords::ip_version = (addr.is_v6() ? sinks::ip_versions::v6 : sinks::ip_versions::v4)));  
  
        sink->set_formatter  
        (  
            expr::format("syslog.exe: %1%: %2%")  
                % expr::attr< unsigned int >("RecordID")  
                % expr::smessage  
        );  
  
        // We'll have to map our custom levels to the syslog levels  
        sinks::syslog::custom_severity_mapping< severity_levels > mapping("Severity");  
        mapping[normal] = sinks::syslog::info;  
        mapping[warning] = sinks::syslog::warning;  
        mapping[error] = sinks::syslog::critical;  
  
        sink->locked_backend()->set_severity_mapper(mapping);  
  
        // Set the remote address to sent syslog messages to  
        sink->locked_backend()->set_target_address(addr);  
  
        // Add the sink to the core  
        logging::core::get()->add_sink(sink);  
  
        // Add some attributes too  
        logging::core::get()->add_global_attribute("RecordID", attrs::counter< unsigned int >());  
  
        // Do some logging  
        src::severity_logger< severity_levels > lg(keywords::severity = normal);  
        BOOST_LOG_SEV(lg, normal) << "A syslog record with normal level";  
        BOOST_LOG_SEV(lg, warning) << "A syslog record with warning level";  
        BOOST_LOG_SEV(lg, error) << "A syslog record with error level";  
  
```  
  
Please let me know if i am doing anything wrong in my code. This same code works fine if i use ipv4 address.
