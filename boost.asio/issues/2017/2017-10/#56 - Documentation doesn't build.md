# #56 - Documentation doesn't build [Closed]

> Username: pdimov  
> Created at: 2017-10-23 14:26:09 UTC  
> Updated at: 2017-10-23 20:40:45 UTC  
> Closed at: 2017-10-23 20:40:45 UTC  
> Url: https://github.com/boostorg/asio/issues/56  

See the log at https://circleci.com/gh/boostorg/boost/5493:  
  
```  
Generating Output File: /home/ubuntu/build/boost/bin.v2/libs/asio/doc/gcc-gnu-4.8/debug/threadapi-pthread/asio_doc.xml  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:32: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:32: warning: line breaks generate invalid boostbook (will only note first occurrence).  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:33: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:36: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:37: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:41: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:42: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:43: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:44: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:45: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:46: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:47: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessReadDevice.qbk:48: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:32: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:33: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:37: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:38: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:42: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:43: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:44: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:45: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:46: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:47: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:48: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncRandomAccessWriteDevice.qbk:49: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncReadStream.qbk:34: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncReadStream.qbk:35: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncReadStream.qbk:42: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncReadStream.qbk:43: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncWriteStream.qbk:34: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncWriteStream.qbk:35: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncWriteStream.qbk:40: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/requirements/AsyncWriteStream.qbk:41: warning: '\n' is deprecated, pleases use '[br]' instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:20: error: Unable to find file: requirements/ComposedConnectHandler.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:23: error: Unable to find file: requirements/ConvertibleToConstBuffer.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:24: error: Unable to find file: requirements/ConvertibleToMutableBuffer.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:25: error: Unable to find file: requirements/DatagramSocketService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:26: error: Unable to find file: requirements/DescriptorService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:31: error: Unable to find file: requirements/HandleService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:37: error: Unable to find file: requirements/ObjectHandleService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:39: error: Unable to find file: requirements/RandomAccessHandleService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:40: error: Unable to find file: requirements/RawSocketService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:43: error: Unable to find file: requirements/ResolverService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:44: error: Unable to find file: requirements/SeqPacketSocketService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:45: error: Unable to find file: requirements/SerialPortService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:51: error: Unable to find file: requirements/SignalSetService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:52: error: Unable to find file: requirements/SocketAcceptorService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:53: error: Unable to find file: requirements/SocketService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:54: error: Unable to find file: requirements/StreamDescriptorService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:55: error: Unable to find file: requirements/StreamHandleService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:56: error: Unable to find file: requirements/StreamSocketService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:62: error: Unable to find file: requirements/TimerService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:63: error: Unable to find file: requirements/WaitableTimerService.qbk  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:72: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:126: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:182: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:211: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:307: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:350: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:862: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:1231: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:1610: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:2173: error: Invalid number of arguments passed. Expecting: 3 argument(s), got: 2 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:2190: error: Invalid number of arguments passed. Expecting: 3 argument(s), got: 2 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:2204: error: Invalid number of arguments passed. Expecting: 3 argument(s), got: 2 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:2227: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:2568: error: Invalid number of arguments passed. Expecting: 2 argument(s), got: 1 argument(s) instead.  
/home/ubuntu/boost/libs/asio/doc/reference.qbk:3362: error: Invalid number of arguments passed. Expecting: 3 argument(s), got: 2 argument(s) instead.  
```  
  
and many more of the same.

---

## Comment 1

> Username: chriskohlhoff  
> Created at: 2017-10-23 20:40:45 UTC  
> Url: https://github.com/boostorg/asio/issues/56#issuecomment-338789822  

Fixed on develop.
