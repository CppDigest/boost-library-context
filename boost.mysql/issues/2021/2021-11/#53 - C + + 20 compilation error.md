# #53 - C + + 20 compilation error [Closed]

> Username: tinyfos  
> Created at: 2021-11-09 11:40:22 UTC  
> Updated at: 2023-01-14 23:06:43 UTC  
> Closed at: 2023-01-14 23:06:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/53  

Boost version: 1.77  
  
Version 1.76 is the same error. C + + 14 or C + + 17 is still good  
  
However, C + + 20 is an error  
  
seriousness code explain project file that 's ok Disable display status  
  
error C3313 'result ': variable cannot have type' void ' ConsoleApplication3 D:\ConsoleApplication3\ConsoleApplication3\ConsoleApplication3.cpp one hundred and eight  
  
error C3536 'result ': cannot be used before initialization ConsoleApplication3 D:\ConsoleApplication3\ConsoleApplication3\ConsoleApplication3.cpp one hundred and nine  
  
error C2244 "Boost:: MySQL:: socket_connection < socketstream >:: async_connect": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\socket_ connection.hpp forty-nine  
  
error C2244 "Boost:: MySQL:: socket_connection < socketstream >:: async_close": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\socket_ connection.hpp eighty-eight  
  
error C3779 "Boost:: MySQL:: socket_connection < boost:: ASIO:: IP:: TCP:: socket >:: async_connect": to use a function that will return "auto", you must first define this function ConsoleApplication3 D:\mysql-master\include\boost\mysql\socket_ connection.hpp ninety-nine  
  
error C3779 "Boost:: MySQL:: socket_connection < boost:: ASIO:: IP:: TCP:: socket >:: async_close": to use a function that will return "auto", you must first define this function ConsoleApplication3 D:\mysql-master\include\boost\mysql\socket_ connection.hpp one hundred and sixty  
  
error C2244 "Boost:: MySQL:: resultset < stream >:: async_read_one": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\resultset.hpp one hundred and eighty-one  
  
error C2244 "Boost:: MySQL:: resultset < stream >:: async_read_many": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\resultset.hpp three hundred and twelve  
  
error C2244 "Boost:: MySQL:: resultset < stream >:: async_read_all": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\resultset.hpp three hundred and thirty-six  
  
error C2244 "Boost:: MySQL:: prepared_statement < stream >:: async_execute": unable to match the function definition with the existing statement ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\prepared_ statement.hpp one hundred and forty-four  
  
error C2244 "Boost:: MySQL:: prepared_statement < stream >:: async_close": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\prepared_ statement.hpp one hundred and ninety-three  
  
error C2244 "Boost:: MySQL:: connection < stream >:: async_quit": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\connection.hpp one hundred and eighty-two  
  
error C2244 "Boost:: MySQL:: connection < stream >:: async_query": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\connection.hpp ninety-seven  
  
error C2244 "Boost:: MySQL:: connection < stream >:: async_prepare_statement": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\connection.hpp one hundred and forty-four  
  
error C2244 "Boost:: MySQL:: connection < stream >:: async_handshake": unable to match the function definition with the existing declaration ConsoleApplication3 D:\mysql-master\include\boost\mysql\impl\connection.hpp forty-nine  
  
error C3779 "Boost:: MySQL:: connection < socketstream >:: async_query": to use a function that will return "auto", you must first define this function ConsoleApplication3 D:\mysql-master\include\boost\mysql\connection.hpp two hundred and eighty-eight

---

## Comment 1

> Username: anarthal  
> Created at: 2021-11-09 20:52:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/53#issuecomment-964533499  

Hi @tinyfos,  
  
Thanks for sharing the problem. I hope to look into this issue during this weekend, as the library needs an update to be compatible with Boost 1.77.  
  
Please keep also in mind that this library hasn't been accepted into Boost yet, and that there may be non-backwards compatible changes soon.  
  
I will keep you informed.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: tinyfos  
> Created at: 2021-11-10 03:55:38 UTC  
> Url: https://github.com/boostorg/mysql/issues/53#issuecomment-964762700  

I understand your suggestion and thank you for sharing. Your library idea is exactly what I need. I want to join my open source framework, but my framework is very eager to meet the C + + 20 collaboration, but it is compiled whether it is an independent version of ASIO or boost. However, I have a small suggestion. Some of the inside are actually supported by STL, so there is no need to rely too much on boost, Finally, I sincerely thank you for your dedication to the spirit of open source

---

## Comment 3

> Username: tinyfos  
> Created at: 2021-11-10 09:48:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/53#issuecomment-964955722  

Hi  
  
I temporarily disabled boost_ ASIO_ DISABLE_ CONCEPTS  
  
Or add boost:: MySQL to the declaration
