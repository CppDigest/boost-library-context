# #364 - fatal error C1189: #error:  <future> is not supported when compiling with /clr or /clr:pure. [Closed]

> Username: Woah0420  
> Created at: 2020-10-11 22:39:19 UTC  
> Updated at: 2020-11-05 20:13:57 UTC  
> Closed at: 2020-11-05 20:13:57 UTC  
> Url: https://github.com/boostorg/asio/issues/364  

This is my code below and I don't understand why im getting this error  
  
using namespace System;  
using namespace System::Windows::Forms;  
  
#include "login.h"  
#include <boost/asio.hpp>  
  
void main()  
{  
	boost::asio::io_service ios;  
}
