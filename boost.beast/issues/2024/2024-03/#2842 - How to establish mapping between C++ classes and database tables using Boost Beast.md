# #2842 - How to establish mapping between C++ classes and database tables using Boost Beast. [Closed]

> Username: shivank1111  
> Created at: 2024-03-26 05:32:47 UTC  
> Updated at: 2024-04-01 04:27:37 UTC  
> Closed at: 2024-04-01 04:27:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2842  

Boost version: 1.83.0  
  
Hi,   
I used Gigabase to establish mapping between C++ classes and database tables while using Boost Asio(Version 1.63). But now I want to add in the latest boost library(1.83) but I am not able to add the class and Type descriptors.  
Here is the example that I used :  
  
  
REGISTER(Detail);  
	  
class Detail  
{  
	public:  
		std::string name = "";  
		std::string unitid = "";  
		TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
};   
  
This if I am looking to add in my current chat multi boost beast server code then it is not happening.  
Kindly help me with the changes such that the changed code would be compatible with latest boost beast library.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-26 07:12:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2842#issuecomment-2019539615  

Could you please explain what the issue is with Boost.Beast? I can't find anything related to Boost.Beast in the provided code.

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-03-26 12:26:00 UTC  
> Updated at: 2024-03-26 12:27:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2842#issuecomment-2020294961  

I have this chat multi code : https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi  
   
 In this code I have made one "main.cpp" file in which I have called relevant .cpp and .hpp files. P.S. : I have renamed the main.cpp file of chat-multi to boost_beast.cpp  
   
 I have also made one server_config_use.hpp file in which I have defined the class:  
   
 {  
 public:  
 std::string name = "";  
 std::string unitid = "";  
 TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
 };  
   
 And in main.cpp file I have added   
 REGISTER(Detail);  
   
 Now the issue is that If I compile using :  
 "g++ -std=c++17 main.cpp -o main -lpthread -lboost_system -lboost_filesystem"  
   
 Then I am getting this error:  
  from main.cpp:25:  
   
 server_config_use.hpp:31:17: error: expected identifier before ‘(’ token  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                  ^  
 server_config_use.hpp:31:21: error: expected ‘)’ before ‘(’ token  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                      ^  
 server_config_use.hpp:31:21: error: expected ‘)’ before ‘(’ token  
 server_config_use.hpp:31:22: error: ‘name’ is not a type  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                       ^~~~  
 server_config_use.hpp:31:28: error: ‘INDEXED’ has not been declared  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                             ^~~~~~~  
 server_config_use.hpp:31:35: error: expected ‘,’ or ‘...’ before ‘|’ token  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                    ^  
 server_config_use.hpp:31:42: error: ISO C++ forbids declaration of ‘TYPE_DESCRIPTOR’ with no type [-fpermissive]  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                           ^  
 server_config_use.hpp:31:42: error: ‘TYPE_DESCRIPTOR’ declared as function returning a function  
 server_config_use.hpp:31:48: error: ‘unitid’ is not a type  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                                 ^~~~~~  
 server_config_use.hpp:31:56: error: ‘INDEXED’ has not been declared  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                                         ^~~~~~~  
 server_config_use.hpp:31:63: error: expected ‘,’ or ‘...’ before ‘|’ token  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                                                ^  
 server_config_use.hpp:31:70: error: ISO C++ forbids declaration of ‘KEY’ with no type [-fpermissive]  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                                                       ^  
 server_config_use.hpp:31:70: error: expected ‘;’ at end of member declaration  
 server_config_use.hpp:31:71: error: expected unqualified-id before ‘)’ token  
  TYPE_DESCRIPTOR((KEY(name, INDEXED|HASHED),KEY(unitid, INDEXED|HASHED)));  
                                                                        ^  
   
 To get a detailed reference about how to establish mapping between C++ classes and database tables:  
 http://www.garret.ru/gigabase/GigaBASE.htm#cli

---

## Comment 3

> Username: shivank1111  
> Created at: 2024-03-26 12:27:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2842#issuecomment-2020297933  

SS of the error:  
  
![Error_1](https://github.com/boostorg/beast/assets/160120467/8bfa4672-00a6-41e3-8912-962f2f49291e)
