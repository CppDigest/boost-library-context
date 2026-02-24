# #1626 - how can i send ptree value type of response with correct content length? [Closed]

> Username: yiivon  
> Created at: 2019-05-31 03:22:06 UTC  
> Updated at: 2019-07-07 18:31:08 UTC  
> Closed at: 2019-07-07 18:31:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1626  

At the server, while I really trying to implement the `using value_type = boost::property_tree::ptree;` as like as the official example, I try to write the use case code  
```  
auto const defalut_post =  
		[&req]()  
	{  
		http::response<http::ptree_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "application/json; charset=UTF-8");  
		res.keep_alive(req.keep_alive());  
		res.body().put("ab", "ccdd");  
		res.body().put("cd", 1234);  
		res.prepare_payload();  
		return res;  
	};  
```  
in class **http::ptree_body** implemented a class writer, its member function **get** stringify object _ptree_ to _std::string_ feed to **serializer** as that:  
```  
ptree_body::writer::get(...)  // stringify ptree object to string and return string to caller  
serializer::next(...)  
...  
detail::async_write_some_impl(...)  
...  
http::async_write(...)  
...  
```  
**But** `http::ptree_body::size` was invoked **before** all members of ptree_body's member functions by `res.prepare_payload();`.  
```  
struct ptree_body  
{  
	using value_type = boost::property_tree::ptree;  
  
	static  
		std::uint64_t  
		size(value_type const& body) {  
		return body.size();	// NOTE: body is type of ptree  
		/*  
			should i first stringify body to string, then return the length of string?  
			maybe okay, but if i can caching this string for writer's get,? this is a static function.  
		*/  
	}  
...  
}  
```  
  
So I wanna get a solution for that, it should make us more clearly to use or understand this library, I suggest that if there are more detail usage examples may be more helpful for using this library, and let this library's api to be more powerful and stable, thank you all the same:)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-05-31 14:29:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1626#issuecomment-497729119  

The _Body_ concept is not designed to render the entire body in memory at the moment of serialization. For your use-case, the _chunked_ transfer-encoding is more appropriate since it allows the ptree to be serialized incrementally (if the ptree supports it). If you really want to have the size of the serialized ptree known ahead of time, you have to serialize it yourself into a string, and use `http::string_body` instead, so that the content length is correct. I don't recommend this, since it negates one of the benefits of using a ptree body in the first place, which is that you don't have to serialize the entire ptree into memory in order to send it as the body of an HTTP message.

---

## Comment 2

> Username: yiivon  
> Created at: 2019-05-31 17:28:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1626#issuecomment-497794264  

Okay, thank you so much!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-06-30 18:15:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1626#issuecomment-507056136  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-07-07 18:31:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1626#issuecomment-509020996  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
