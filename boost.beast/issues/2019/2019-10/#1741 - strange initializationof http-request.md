# #1741 - strange  initializationof http:request [Closed]

> Username: zaley2000  
> Created at: 2019-10-22 07:54:08 UTC  
> Updated at: 2019-11-13 07:53:54 UTC  
> Closed at: 2019-11-13 07:53:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1741  

I am a newbie beast . This is my code. The function returns directly after initialization of http request.  My BOOST version is 1.71  and beast version is 266.   
  
  
void CHttpClient::executeWithRetry(CHttpMethod* method)  
{  
	CURI uri = method->getURI();  
	string hostName = uri.getHost();  
	int port = uri.getPort();  
	bool ret = connect(hostName, port);  
	if (!ret)  
		return;  
  
	/////  
	string target = uri.getPath();  
	string version = method->httpVersion;  
	int ver = 11;  
	string user_agent = method->getRequestHeader(CHttpConst::USER_AGENT);  
	string cache_control = method->getRequestHeader(CHttpConst::CACHE_CONTROL);  
	string content_length = method->getRequestHeader(CHttpConst::CONTENT_LENGTH);  
	string pragma = method->getRequestHeader(CHttpConst::PRAGMA);  
  
	try  
	{  
               // here is strange.    
		beast::http::request<beast::http::string_body> req{ beast::http::verb::get, target, ver };			//   **????????????????????????????**  
  
		req.set(beast::http::field::user_agent, user_agent);  
		req.set(beast::http::field::cache_control, cache_control);  
		req.set(beast::http::field::pragma, pragma);  
		req.set(beast::http::field::content_length, content_length);  
		req.set(beast::http::field::host, host);  
		beast::http::write(stream, req);  
	}  
	catch (std::exception& e)  
	{  
		return;  
	}  
  
	beast::flat_buffer buffer;  
	beast::http::response<beast::http::string_body> res;					  
	beast::http::read(stream, buffer, res);  
  
	string sContent = res.body();  
	int status = res.result_int();  
	float sVer = res.version()/10.0;  
	string phrase = to_string(res.reason());  
  
	boost::format fmt("HTTP/%1.1f %d %s\r\n");  
	fmt % sVer % status % phrase;  
	method->statusLine = fmt.str();  
	method->statusCode = status;  
	for (auto const& field : res)  
	{  
		string name = to_string(field.name()).data();  
		string value = to_string(field.value());  
		method->addRequestHeader(name, version);  
	}  
}

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-22 20:29:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1741#issuecomment-545140005  

What does "The function returns directly" mean?

---

## Comment 2

> Username: zaley2000  
> Created at: 2019-10-23 05:52:35 UTC  
> Updated at: 2019-10-23 06:01:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1741#issuecomment-545278916  

In the vs2015 debug mode, after  single step of this line completed, the vs2015 execution position jumps directly to the function location. No problem appear when  executiing directly outside of vs2015 IDE.  
This does not seem to always appear. There is no such problem in simple code, such as the example provided by beast. I suspect that the VS2015 debugger may have a problem when the template is expanded.
