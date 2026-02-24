# #1675 - http::request<Body, http::basic_fields<Allocator>>&& req [Closed]

> Username: klasing  
> Created at: 2019-08-06 21:07:57 UTC  
> Updated at: 2023-12-31 09:43:29 UTC  
> Closed at: 2023-12-31 09:43:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1675  

// Respond to a TRACE request  
	if (req.method() == http::verb::trace) {  
		std::cout << "-> TRACE message received\n";  
  
		http::string_body::value_type body;  
		std::string message_to_client = std::string("server is alive\n")  
			// TODO this has to be the header of the request  
			// e.g. TRACE / HTTP/1.1  
			// not yet accomplished, compiler complains:  
			// 'type cast': conversion from 'boost::beast::http::header<true,Fields> *  
			// ' to 'const _Alloc &' exists, but is inaccessible  
			+ static_cast<std::string>(req.base())  
			+ "\n"  
			+ static_cast<std::string>(req[http::field::host])  
			+ "\n"  
			+ static_cast<std::string>(req[http::field::user_agent]);  
		body = message_to_client;

---

## Comment 1

> Username: cmazakas  
> Created at: 2019-08-06 21:22:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-518849031  

It seems as though you're trying to set the header in the body of the message.  
  
Instead, just set the header fields directly via `req.set(...)`.  
  
I don't think `static_cast<std::string>(req.base())` is a valid conversion.  
  
Also, you need to call `req.preapre_payload();` after setting the body and before writing the request.

---

## Comment 2

> Username: klasing  
> Created at: 2019-08-06 21:32:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-518852175  

Yeah, now you mention it. I might be completely of track. The trace request from the client has to go into the payload of the response from the server. Thanks for bringing this insight!

---

## Comment 3

> Username: cmazakas  
> Created at: 2019-08-07 17:49:50 UTC  
> Updated at: 2019-08-07 17:51:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-519201924  

> The trace request from the client has to go into the payload of the response from the server.   
  
Oh, I see what you're trying to do better now.  
  
You might need something like this that converts a message to a string:  
https://wandbox.org/permlink/3yV3EwVQqryH81RZ

---

## Comment 4

> Username: klasing  
> Created at: 2019-08-07 18:05:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-519207971  

I solved this issue, more or less. This is what I did. So the string variable: message_to_client is what goes into the payload.  
  
// Respond to a TRACE request  
if (req.method() == http::verb::trace) {  
	std::cout << "-> TRACE message received\n";  
  
	http::string_body::value_type body;  
	std::string message_to_client = std::string("server is alive\n")  
		// not elegant, but it works  
		+ "TRACE / HTTP/" + ((req.version() == 10) ? "1.0" : "1.1")  
		+ "\n"  
		+ "Host: " + static_cast<std::string>(req[http::field::host])  
		+ "\n"  
		+ "User-Agent: " + static_cast<std::string>(req[http::field::user_agent]);  
	body = message_to_client;  
  
	auto const size = body.size();  
  
	http::response<http::string_body> res{ http::status::ok, req.version() };  
	res.set(http::field::date, beast::string_view(date_for_http_response()));  
	res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
	res.set(http::field::content_type, beast::string_view("message/http"));  
	res.content_length(size);  
	res.keep_alive(false);  
	res.body() = std::move(body);  
	res.prepare_payload();  
	return send(std::move(res));  
}  
  
I’m already on to the next issue. Namely, processing a form submit, for uploading a file. Issue number one: the file name is given in a field named: Content-Disposition, and this is a part of the payload received by the server. Issue number two: not only the file contents gets written to disk (on the server side) but also some stuff I never asked for. Here is what gets written to disk. The bold stuff is the actual file contents. It means that I have to string manipulate the payload in order to save the original file contents, with no more than that.  
  
------WebKitFormBoundaryfHLqcar2uBG3Jgn3  
Content-Disposition: form-data; name="file"; filename="bla.txt"  
Content-Type: text/plain  
  
bla bla  
more bla bla  
and even more bla bla  
------WebKitFormBoundaryfHLqcar2uBG3Jgn3--  
  
Thanks for your helpfulness, so far.  
  
Kind regards, martijn klasing  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: LeonineKing1199  
Verzonden: woensdag 7 augustus 2019 19:49  
Aan: boostorg/beast  
CC: klasing; Author  
Onderwerp: Re: [boostorg/beast] http::request<Body,http::basic_fields<Allocator>>&& req (#1675)  
  
The trace request from the client has to go into the payload of the response from the server.  
Oh, I see what you're trying to do better now.  
You might need something like this that converts a message to a string:  
https://wandbox.org/permlink/Al5PMBKsjBUHAvjw  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 5

> Username: klasing  
> Created at: 2019-08-07 18:47:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-519222976  

Leonine, this example you gave me, works like a charm. It’s an eyeopener for me. Thanks very much, I really appreciate it.  
Here’s the result from implementing the code. Great, I love it!  
  
remote endpoint 192.168.178.14:63368  
<<constructor>> session()  
-> TRACE message received  
TRACE / HTTP/1.1  
Host: 192.168.178.14  
User-Agent: Boost.Beast/248  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: LeonineKing1199  
Verzonden: woensdag 7 augustus 2019 19:49  
Aan: boostorg/beast  
CC: klasing; Author  
Onderwerp: Re: [boostorg/beast] http::request<Body,http::basic_fields<Allocator>>&& req (#1675)  
  
The trace request from the client has to go into the payload of the response from the server.  
Oh, I see what you're trying to do better now.  
You might need something like this that converts a message to a string:  
https://wandbox.org/permlink/Al5PMBKsjBUHAvjw  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 6

> Username: klasing  
> Created at: 2019-08-21 07:34:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523335502  

What can be done with a: http::response<http::string_body> or http::response<http::empty_body>, can't be done with a http::response<http::file_body> My compiler (inside Microsoft Studio   
Visual Community 2019) replies with:   
`  
error C2280:  'boost::beast::http::message<false,boost::beast::http::basic_file_body<boost::beast::file>,boost::beast::http::fields>::message(const boost::beast::http::message<false,boost::beast::http::basic_file_body<boost::beast::file>,boost::beast::http::fields> &)': attempting to reference a deleted function  
`

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-08-21 15:59:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523524639  

`file_body` is not copyable, can you show the code you are trying to compile?

---

## Comment 8

> Username: klasing  
> Created at: 2019-08-21 16:45:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523545496  

Yeah, thanks Vinnie. I noticed a file body is not copy able. Normally I would add a copy constructor and a copy assignment operator to my own build class. Which would solve the problem. But now I hesitate to dive into the boost::beast::response<http::file_body> class. I’m trying to log: requests, response, et cetera into a file by converting all the elements into std::string. Here’s my code. It starts with calling a function to do the conversion  
  
store_new_log(pRemoteEndpoint // speaks for itself, I suppose  
      , pTimer			   // ptr to a boost::timer::cpu_timer class  
	, requestLogmsg		   // a std::string with first line of request  
	, res				   // a boost::beast::response<http::file_body>  
);  
  
Then in the function store_new_log(), the conversion is coded like this.  
  
template <class T>  
inline void store_new_log(std::shared_ptr<std::string> pRemoteEndpoint  
	, std::shared_ptr<boost::timer::cpu_timer> pTimer  
	, std::string requestLogmsg  
	, T res  
)  
{  
	// turn the response message into a string  
	auto buff_res = beast::flat_buffer();  
	write_message_to_string(res, buff_res);  
	std::string responseLogmsg = filter_start_line(  
		beast::buffers_to_string(buff_res.data()));  
  
	// stop timer  
	pTimer->stop();  
	store_log(*pRemoteEndpoint  
		, requestLogmsg  
		, responseLogmsg  
		, pTimer->elapsed()  
	);  
}  
  
I templated the third parameter, to bring in a: boost::beast::response<http::empty_body>, boost::beast::response<http::string_body>, and boost::beast::response<http::file_body> type for conversion. This was my humble thinking. But it didn’t work out for the latter type.  
  
The part after // turn the response message into a string I got from Leonine. The filter_start_line() function contains:  
  
inline std::string filter_start_line(const std::string& req_message)  
{  
	// return first line of a request message  
	return req_message.substr(0, req_message.find('\r'));  
}  
  
The store_log() function brings its parameters to another .cpp that stores the log message into a file. I suppose that part is not very interesting, in this case. But here’s the code anyway.  
  
// should not be an inline function, don't ask me why  
void store_log(const std::string& remote_endpoint  
	, const std::string& request  
	, const std::string& response  
	, const boost::timer::cpu_times& timer_elapsed  
)  
{  
	// get current gmt  
	std::string current_gmt = date_for_http_response();  
	std::string elapsed_time = std::to_string(  
		timer_elapsed.wall / (double)1e6);  
	// store in tuple  
	tuple_logging tl{ current_gmt, remote_endpoint,  
		request, response, elapsed_time };  
	// write tuple to file  
	write_logfile(tl);  
  
	get_filesize_logfile();  
  
	// clear file if too big  
	clear_logfile_if_too_big();  
}  
  
   
Verzonden vanuit Mail voor Windows 10  
  
Van: Vinnie Falco  
Verzonden: woensdag 21 augustus 2019 17:59  
Aan: boostorg/beast  
CC: klasing; Author  
Onderwerp: Re: [boostorg/beast] http::request<Body,http::basic_fields<Allocator>>&& req (#1675)  
  
file_body is not copyable, can you show the code you are trying to compile?  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-08-21 16:47:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523546307  

Why not just use `operator<<` on the message, to `std::stringstream`?

---

## Comment 10

> Username: klasing  
> Created at: 2019-08-21 17:33:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523567426  

Right, nice suggestion. I will try, and get back to you. Thanks so far.  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: Vinnie Falco  
Verzonden: woensdag 21 augustus 2019 18:47  
Aan: boostorg/beast  
CC: klasing; Author  
Onderwerp: Re: [boostorg/beast] http::request<Body,http::basic_fields<Allocator>>&& req (#1675)  
  
Why not just use operator<< on the message, to std::stringstream?  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-08-21 19:28:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523614210  

Ah yes, I remember the problem now. Because the `file_body` stores the file descriptor in the message, the serialization operation is mutating (modifies the object). For this reason, the message cannot be passed by const reference. I worked some meta-programming voodoo to get it all to work, as you can see here:  
https://github.com/boostorg/beast/blob/6d614cf9a8c6d81258e309682621286bf9d74ec3/include/boost/beast/http/serializer.hpp#L67  
  
I am not very happy with how this turned out, and you have discovered a design flaw with it. I intend to address it in the upcoming "Big HTTP Refactor" but that won't happen until at least next year. In the meanwhile I'll keep this issue open as a reminder.

---

## Comment 12

> Username: klasing  
> Created at: 2019-08-21 19:32:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523615625  

Alright, Vinnie. Thanks for all the effort. Tomorrow I will study your article and see what I can do with it. For now, have a pleasant day. And I’ll be back to you.  
Greetz  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: Vinnie Falco  
Verzonden: woensdag 21 augustus 2019 21:28  
Aan: boostorg/beast  
CC: klasing; Author  
Onderwerp: Re: [boostorg/beast] http::request<Body,http::basic_fields<Allocator>>&& req (#1675)  
  
Ah yes, I remember the problem now. Because the file_body stores the file descriptor in the message, the serialization operation is mutating (modifies the object). For this reason, the message cannot be passed by const reference. I worked some meta-programming voodoo to get it all to work, as you can see here:  
https://github.com/boostorg/beast/blob/6d614cf9a8c6d81258e309682621286bf9d74ec3/include/boost/beast/http/serializer.hpp#L67  
I am not very happy with how this turned out, and you have discovered a design flaw with it. I intend to address it in the upcoming "Big HTTP Refactor" but that won't happen until at least next year. In the meanwhile I'll keep this issue open as a reminder.  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-08-21 20:05:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-523627353  

You can try writing your own Body type and see if you can do a better job than Beast currently does with `file_body`.  I would be interested in hearing the results of such an experiment.

---

## Comment 14

> Username: klasing  
> Created at: 2019-08-29 07:14:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1675#issuecomment-526057738  

What I did -so far- to meet my goal, is the following. In the file boost/beast/http/basic_file_body.hpp I rewrote the operator<< method. It was an explicitly deleted method. And you wrote there was a good reason for this. But I don’t understand this to well. So you have to be the judge about the consequences. Here’s my code.  
  
template<bool isRequest, class File, class Fields>  
std::ostream&  
operator<<(std::ostream& os, message<  
	    isRequest, basic_file_body<File>, Fields> const& msg)  
{  
	os << "HTTP/"  
		// not considering other protocols than HTTP/1.0 and HTTP/1.1  
		<< ((msg.base().version() == 10) ? "1.0" : "1.1") << ' '  
		<< msg.base().result_int() << ' '  
		<< msg.base().result() << '\n';  
	return os;  
}  
  
Now you can call the operator<< method with, e.g.  
  
// turn the response message into a string  
auto buff_res = beast::flat_buffer();  
write_message_to_string(fb_res, buff_res);  
std::string responseLogmsg = filter_start_line(  
	beast::buffers_to_string(buff_res.data()));  
std::cout << "response...: " << responseLogmsg << std::endl;  
  
Where the write_message_to_string()function is the one I got from Leonine.  
  
Now, rewriting a whole new boost::beast::http::response<file_body> type is a bit to far fetchted for me. But if you can help me to get along, I can surely try.  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: Vinnie Falco  
Verzonden: woensdag 21 augustus 2019 22:05  
Aan: boostorg/beast  
CC: klasing; Author  
Onderwerp: Re: [boostorg/beast] http::request<Body,http::basic_fields<Allocator>>&& req (#1675)  
  
You can try writing your own Body type and see if you can do a better job than Beast currently does with file_body. I would be interested in hearing the results of such an experiment.  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.
