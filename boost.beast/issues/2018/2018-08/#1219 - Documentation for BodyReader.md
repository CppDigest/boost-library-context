# #1219 - Documentation for BodyReader [Closed]

> Username: computerquip-streamlabs  
> Created at: 2018-08-07 22:57:27 UTC  
> Updated at: 2018-08-08 22:53:45 UTC  
> Closed at: 2018-08-08 22:53:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1219  

I noticed when I was implementing a custom BodyReader that when put was called, it wasn't obtaining new data on subsequent calls. I couldn't figure out how the hell to accomplish this until I started searching for alternative ways of parsing the body. Turns out the documentation for this is in [here](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/put.html) despite being unrelated to BodyReader in practice, especially when it barely mentions the two on any given page  
  
If the two have the same behavior, perhaps a concept abstracting that interface could be created and then the documentation be unified for the two.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-08 00:51:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1219#issuecomment-411248568  

**BodyReader** and `basic_parser` are quite different although they have similarly named members (`put`). The documentation you linked is something that users rarely need to call, if ever. Why aren't you simply using one of the overloads of `read` or `async_read`? Can you please describe the problem you encountered in a little more detail, so I can determine what changes need to be made, if any? Thanks.

---

## Comment 2

> Username: computerquip-streamlabs  
> Created at: 2018-08-08 18:42:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1219#issuecomment-411510266  

I'm using the Body concept for the purpose of parsing and serialization of a simple text file where a full entry is delimited by a newline. I see [async_read_some](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html) but it seems to require I pass a parser. I can pass the default parser (base_parser) and parse the message out of the buffer each time the read handler is called, then consume only the bytes parsed. I also appear to be able to pass my own "derived" parser implementation. So there's to be three ways to do the same thing and upon looking into it, I still can't decide which one is best. I'm inclined to keep my Body concept implementation simply because I've already written it.   
  
I'd also like to mention how hard it is to navigate the documentation in general. For instance, just finding the signature for `async_read_some`, being a free standing function, is actually hard to find and seems to be behind a hidden page (which is one reason I didn't use it, given I didn't think it existed). I actually found that in the headers and then used google to find the page. Am I just not looking in the right place? Perhaps an overall reference page like asio has would be useful.

---

## Comment 3

> Username: computerquip-streamlabs  
> Created at: 2018-08-08 19:03:45 UTC  
> Updated at: 2018-08-08 19:09:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1219#issuecomment-411517090  

I don't mean to be nit picky. This documentation is some of the hardest to navigate in my opinion. It doesn't clearly connect the concepts I feel and it's easy to get led astray. It's not clear how to use `async_read_some` or that it even exists. It's not clear how to work with a derived parser using an asynchronous stream. It's hard to figure out where you would use the differing concepts. Some of the frustration I can't really explain very well but it's worse than the usual Boost documentation and I've been led down the rabbit hole I feel like every single turn I've made in this library. Once everything works, it has worked well. But it's just the initial learning curve, where apparently you can implement an entire concept for parsing without realizing it's not necessary. This documentation, where some pages are behind hidden pages for some reason, others are behind weird padded pages, and related concepts can't be found on pages where it matters, doesn't help that learning curve. For instance, why is `async_read_some` not documented in [Stream Operations](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/message_stream_operations.html) when it's actually important?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-08-08 19:21:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1219#issuecomment-411522124  

The intent of **BodyReader** is to allow the implementation to interact with user-defined body container types.  
  
> `async_read_some`...seems to require I pass a parser  
  
`async_read_some` requires a reference to an instance of the `basic_parser` class template, not a reference to a `parser`.  
  
> I can pass the default parser (base_parser)  
  
I'm not sure what you mean by "default parser" or "base_parser". Neither of those are found in Beast.  
  
Can you please post the source code to your **Body** type, including all the function definitions?  
  
> Perhaps an overall reference page like asio has would be useful.  
  
The documentation has exactly that ("Reference" in the table of contents):  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/quickref.html

---

## Comment 5

> Username: computerquip-streamlabs  
> Created at: 2018-08-08 19:31:20 UTC  
> Updated at: 2018-08-08 20:02:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1219#issuecomment-411524818  

Not sure how I missed that reference at the bottom there so many times. I even used the index there (though far less useful in navigation when you're not already associated with everything).   
  
> I'm not sure what you mean by "default parser" or "base_parser". Neither of those are found in Beast.  
  
By base parser / default parser, I meant basic_parser.   
  
Body Implementation (writer still missing):  
```  
template <class Map>  
class basic_manifest_body {  
public:  
	using value_type = Map;  
  
	class reader;  
};  
  
//####################################  
//# Manifest Body Parser/Reader  
//####################################  
template <class Map>  
class basic_manifest_body<Map>::reader {  
	value_type &body;  
  
public:  
	template<bool isRequest, class Fields>  
	reader(http::header<isRequest, Fields> &h, value_type &body);  
  
	void init(  
	  boost::optional<std::uint64_t> const &content_length,  
	  boost::system::error_code &ec  
	);  
  
	template<class ConstBufferSequence>  
	size_t put(  
	  ConstBufferSequence const &buffers,  
	  boost::system::error_code &ec);  
  
	template <class ConstBuffer>  
	size_t handle_buffer(  
	  ConstBuffer const &buffer,  
	  boost::system::error_code &ec);  
  
	size_t handle_matches(boost::cmatch &type);  
  
	void finish(boost::system::error_code& ec);  
};  
  
template <class Map>  
template <bool isRequest, class Fields>  
basic_manifest_body<Map>::reader::reader(  
  http::header<isRequest, Fields>& header,  
  value_type& body  
) : body(body) {  
	boost::ignore_unused(header);  
}  
  
template <class Map>  
void basic_manifest_body<Map>::reader::init(  
  boost::optional<std::uint64_t> const& content_length,  
  boost::system::error_code& ec  
) {  
	boost::ignore_unused(content_length);  
  
	ec.assign(0, ec.category());  
}  
  
template <class Map>  
size_t basic_manifest_body<Map>::reader::handle_matches(boost::cmatch &matches)  
{  
	std::string checksum(matches[1].first, matches[1].length());  
	std::string file(matches[2].first, matches[2].length());  
	  
	/* Yo dawg, I heard you like move semantics. */  
	body.emplace(move(std::make_pair(move(checksum), move(file))));  
  
	return matches.length();  
}  
  
template <class Map>  
template <class ConstBuffer>  
size_t basic_manifest_body<Map>::reader::handle_buffer(  
  ConstBuffer const &buffer,  
  boost::system::error_code &ec  
) {  
	/* Parse line by line until we can't parse no more.  
	 * TODO: Hardcoded for SHA-256 checksums. */  
	static const boost::regex manifest_regex("([A-Fa-f0-9]{64}) ([^\r\n]+)\r?\n");  
  
	boost::cmatch matches;  
  
	bool regex_result;  
	size_t accum = 0;  
	  
	for (;;) {  
		regex_result = boost::regex_search(  
			(const char *)buffer.data() + accum,  
			(const char *)buffer.data() + buffer.size(),  
			matches, manifest_regex);  
  
		if (!regex_result) break;  
  
		accum += handle_matches(matches);  
	}  
  
	if (buffer.size() != accum)  
		ec = http::error::need_more;  
  
	return accum;  
}  
  
template <class Map>  
template<class ConstBufferSequence>  
size_t basic_manifest_body<Map>::reader::put(  
  ConstBufferSequence const &buffers,  
  boost::system::error_code &ec  
) {  
	size_t accum = 0;  
  
	for (  
	  auto buffer_it = boost::asio::buffer_sequence_begin(buffers);  
	  buffer_it != boost::asio::buffer_sequence_end(buffers);  
	  ++buffer_it  
	) {  
		accum += handle_buffer(*buffer_it, ec);  
	}  
  
	return accum;  
}  
  
template <class Map>  
void basic_manifest_body<Map>::reader::finish(boost::system::error_code &ec)  
{  
	ec = {};  
}  
  
using manifest_body = basic_manifest_body< std::unordered_map<std::string, std::string> >;  
```  
  
I then have some handling code that looks like this:  
```  
	auto &socket = request_ctx->socket;  
	auto &response_buf = request_ctx->response_buf;  
	auto &response = request_ctx->response;  
  
	auto read_handler =   
	[&client_ctx, request_ctx]  
	(auto i, auto e) {  
		handle_manifest_response(i, e, client_ctx, request_ctx);  
	};  
  
	http::async_read(socket, response_buf, response, read_handler);  
```  
  
where `response_buf` is of type `beast::flat_buffer` and response is of type `http::response<manifest_body>`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-08-08 21:32:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1219#issuecomment-411559371  

> why is async_read_some not documented in Stream Operations when it's actually important?  
  
You wrote "Stream Operations" but the docs actually state "Message Stream Operations:"  
  
<https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/message_stream_operations.html>  
  
Those functions form the message-oriented interfaces, since they transact entire messages at once. `async_read_some` obviously does not transact a complete message per call. You can find it in the "Parser Stream Operations" section:  
  
<https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/parser_stream_operations.html>
