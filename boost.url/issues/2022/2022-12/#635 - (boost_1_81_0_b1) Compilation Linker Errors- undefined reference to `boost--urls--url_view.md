# #635 - (boost_1_81_0_b1) Compilation Linker Errors: undefined reference to `boost::urls::url_view [Closed]

> Username: WarrenN1  
> Created at: 2022-12-09 01:42:22 UTC  
> Updated at: 2022-12-09 02:03:03 UTC  
> Closed at: 2022-12-09 01:50:58 UTC  
> Url: https://github.com/boostorg/url/issues/635  

General Overview: I keep running into a linker problem where there are undefined references to boost::urls functions via the linker such as:  
```  
/opt/rh/gcc-toolset-11/root/usr/bin/ld: /home/warrenniles/Desktop/project/webserver/server.cpp:199: undefined reference to `boost::urls::parse_relative_ref(boost::core::basic_string_view<char>)'  
  
/opt/rh/gcc-toolset-11/root/usr/bin/ld: server.o: in function `boost::urls::url_view::url_view(boost::urls::url_view const&)':  
/home/warrenniles/Desktop/project/webserver1/external_libraries/include/boost/url/url_view.hpp:222: undefined reference to `boost::urls::url_view::url_view(boost::urls::url_view_base const&)'  
  
/opt/rh/gcc-toolset-11/root/usr/bin/ld: server.o: in function `boost::variant2::detail::variant_storage_impl<std::integral_constant<bool, true>, boost::urls::url_view, boost::system::error_code>::variant_storage_impl<>(std::integral_constant<unsigned long, 0ul>)':  
/home/warrenniles/Desktop/project/webserver1/external_libraries/include/boost/variant2/variant.hpp:670: undefined reference to `boost::urls::url_view::url_view()'  
```  
My question is how I can go about fixing this problem and/or what I am doing wrong with my setup? Trying to create a boost beats, urls, and cassandra plug and play webserver. I have included basic server.hpp structure as well as my makefile.  
  
Here is the information on the files:  
server.hpp  
```  
...  
#include <boost/url.hpp>  
#include <boost/url/url.hpp>  
#include <boost/url/url_view.hpp>  
using namespace boost::urls;  
...  
```  
and in the makefile  
makefile  
```  
#Compiler  
CC = g++  
#C++ Version Number  
VERSION = 2a  
# compiler flags:  
#  -g	 - this flag adds debugging information to the executable file  
#  -Wall  - this flag is used to turn on most compiler warnings  
CXXFLAGS  = -std=c++$(VERSION) -g -Wall  
LFLAGS    = -std=c++$(VERSION) -g -Wall -c  
  
# Libraries - Where boost and other external library include and libs are located  
INC = $(shell pwd)/external_libraries/include/  
LIB = $(shell pwd)/external_libraries/lib/  
FLAGS = -lboost_coroutine -lboost_context -lboost_chrono -lcrypto -lssl -lboost_system -pthread -fcoroutines -lcassandra  
all: main  
  
main : main.o server.o cassandra/cql.o  
	$(CC) $(CXXFLAGS) main.o server.o cassandra/cql.o $(foreach inc,$(INC), -I$(inc)) $(foreach lib,$(LIB), -L$(lib)) $(FLAGS) -o main  
  
main.o : ssl/server_certificate.hpp main.cpp  
	$(info Compiling Main.cpp)  
	$(CC) $(LFLAGS) main.cpp $(foreach inc,$(INC), -I$(inc)) $(foreach lib,$(LIB), -L$(lib)) $(FLAGS) -o main.o  
  
  
server.o : server_config.hpp server.hpp server.cpp  
	$(info Compiling Server.cpp)  
	$(CC) $(LFLAGS) server.cpp $(foreach inc,$(INC), -I$(inc)) $(foreach lib,$(LIB), -L$(lib)) $(FLAGS) -o server.o  
  
  
cassandra/cql.o : cassandra/cql.hpp cassandra/cql.cpp  
	$(info Compiling cql.cpp)  
	$(CC) $(LFLAGS) cassandra/cql.cpp $(foreach inc,$(INC), -I$(inc)) $(foreach lib,$(LIB), -L$(lib)) $(FLAGS) -o cassandra/cql.o  
  
  
cassandra/orm.o : cassandra/orm.cpp  
	$(info Compiling orm.cpp)  
	$(CC) $(LFLAGS) cassandra/orm.cpp cassandra/orm.hpp $(foreach inc,$(INC), -I$(inc)) $(foreach lib,$(LIB), -L$(lib)) $(FLAGS) -lcassandra  
  
clean :  
	$(RM) main main.o server.o cassandra/cql.o cassandra/orm.o  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-09 01:43:17 UTC  
> Url: https://github.com/boostorg/url/issues/635#issuecomment-1343726121  

Wheres `-lboost_url`?

---

## Comment 2

> Username: WarrenN1  
> Created at: 2022-12-09 01:50:51 UTC  
> Url: https://github.com/boostorg/url/issues/635#issuecomment-1343730635  

I swear I'm not this dumb normally. Its been a long coding session. Thanks again Vinnie. Really embarrassed with this one lol.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-09 01:53:51 UTC  
> Updated at: 2022-12-09 02:03:03 UTC  
> Url: https://github.com/boostorg/url/issues/635#issuecomment-1343732815  

> I swear I'm not this dumb normally.  
  
If you want to atone, we would be very interested to hear your experiences with using the library since it is brand new and we made many novel API choices which may or may not have been ideal.... tales about your good experiences or bad experiences are welcomed.
