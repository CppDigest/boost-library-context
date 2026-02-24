# #801 - Dose beast support the http version 1.0? [Closed]

> Username: aikuimail  
> Created at: 2017-09-30 09:14:43 UTC  
> Updated at: 2018-05-24 23:02:29 UTC  
> Closed at: 2018-05-24 23:02:29 UTC  
> Url: https://github.com/boostorg/beast/issues/801  

My server is using the http on version 1.0, and  when i change the filed version to 10 it will indicate "end of stream", i set the connect to "keep-alive", it fails yet.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-30 14:10:06 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-333311019  

In HTTP/1.0, servers must include the Content-Length field to indicate the length of the body. Otherwise, the end of the message is determined by the end of file. Beast delivers the `end_of_stream` error after writing a message to indicate that the connection should be closed. You can set Content-Length manually or you can use `message::prepare_payload` function to set it.  
  
If this does not solve your problem I will need to see a small piece of example code that demonstrates the issue.

---

## Comment 2

> Username: aikuimail  
> Created at: 2017-10-01 00:13:52 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-333344575  

Thanks for your help:) Another error is "bad version" when I use the http version 1.1 to post some jason data to the server, how can I solve this problem by beast?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-01 03:47:19 UTC  
> Updated at: 2017-10-01 03:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-333351390  

>Another error is "bad version"  
  
I cannot diagnose your problem from such a short description. I'm going to need more information than what you are providing. If you can post an example program that illustrates the problem, it will be very helpful.  
  
For example, note how the author of this issue provided a piece of code: https://github.com/boostorg/beast/issues/779#issuecomment-328865585

---

## Comment 4

> Username: aikuimail  
> Created at: 2017-10-09 02:23:56 UTC  
> Updated at: 2017-10-09 02:28:03 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-335056549  

Sorry that I has been on a 8 days vocation!This is my issues bellow:  
  
`auto const host = "192.168.31.163";  
		auto const port = "38888";  
		auto const target = "/api?";  
  
		string ip = "192.168.31.176";  
		string pcno = "aixiangkui-PC";  
		string netbarid = "33";  
		string sessionid = "483a8960-38a2-4580-812a-3f75c58695b3";  
		string no = "420222199109244833";  
  
		string data = RequestParam::On(ip, pcno, netbarid, sessionid);  
		string reponse_data;  
		  
  
		// Look up the domain name  
		auto const lookup = resolver.resolve({ host, port });  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::connect(socket, lookup);  
		std::string content = target + data;  
  
		//int ret = post(host, port, target, "", reponse_data);  
		//ss << reponse_data;  
		// Set up an HTTP GET request message  
		http::request<http::string_body> req{ http::verb::get, content, 11 };  
		req.set(http::field::host, host);  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
		// Send the HTTP request to the remote host  
		http::write(socket, req);  
  
		// This buffer is used for reading and must be persisted  
		boost::beast::flat_buffer buffer;  
  
		// Declare a container to hold the response  
		http::response<http::dynamic_body> res;  
		// Receive the HTTP response  
		http::read(socket, buffer, res);  
		// Gracefully close the socket  
		boost::system::error_code ec;  
		socket.shutdown(tcp::socket::shutdown_both, ec);  
  
		// not_connected happens sometimes  
		// so don't bother reporting it.  
		//  
		if (ec && ec != boost::system::errc::not_connected)  
			throw boost::system::system_error{ ec };  
  
		// If we get here then the connection is closed gracefully  
	}  
	//catch (std::exception const& e)  
	catch(boost::beast::system_error& se)  
	{  
		//std::cerr << "Error: " << e.what() << std::endl;  
		//return EXIT_FAILURE;  
		std::cerr << se.what() << std::endl;  
		socket.shutdown(tcp::socket::shutdown_send);  
	}`  
  
This is RequestParam::On:  
  
`std::string RequestParam::On(std::string ip, std::string pcno, std::string netbarid, std::string sessionid)   
{  
	//On request format:?={m:on,dt:{ip:xxx,pcno:A001,netbarid:xxx,sessionid:xxx}}  
	ptree pt, pt_11;  
	std::string prefix = "c=";  
	pt.put("m", "on");  
	pt_11.put("ip", ip);  
	pt_11.put("pcno", pcno);  
	pt_11.put("netbarid", netbarid);  
	pt_11.put("sessionid", sessionid);  
	pt.push_back(make_pair("dt", pt_11));  
	ostringstream os;  
	write_json(os, pt);  
	return prefix + os.str();  
}  
  
  
  
  
  
`  
  
Then the error indicates "bad version"

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-09 04:16:15 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-335064182  

What does the response coming from the server look like?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-01-06 01:14:38 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-355710904  

Is this issue still relevant?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:36 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-384003574  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-04-24 22:07:09 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-384095474  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-05-24 22:56:33 UTC  
> Url: https://github.com/boostorg/beast/issues/801#issuecomment-391890875  

This issue has been open for a while with no activity, has it been resolved?
