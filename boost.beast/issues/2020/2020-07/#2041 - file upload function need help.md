# #2041 - file upload function need help [Closed]

> Username: iams13  
> Created at: 2020-07-29 05:45:58 UTC  
> Updated at: 2020-07-29 06:37:13 UTC  
> Closed at: 2020-07-29 06:37:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2041  

I try to add simple file upload function:  
file:  ./boost_1_73_0/boost/beast/http/impl/basic_parser.ipp  
in this position: `case state::start_line:`  
added the following code, omit some variable declaration and so on:  
  
		_tmp.assign(p, n);  
		split_strings(_tmp, vctstr, "\r\n");  
		for (size_t i = 0; i < vctstr.size(); ++i)  
		{  
			if (vctstr[i].find("HTTP/") != std::string::npos)  
			{  
				if (strncmp(vctstr[i].c_str(), "POST /xxxUpload", sizeof("POST /xxxUpload") - 1) == 0)  
				{  
					isupload = true;  
				}  
				split_strings(vctstr[i], vcttmp, " ");  
				_headermap["Url"] = vcttmp[1];  
				break;  
			}  
		}  
  
and in this position:   
template<bool isRequest>  
void  
basic_parser<isRequest>::  
parse_body(char const*& p,  
    std::size_t n, error_code& ec)  
  
    if (isupload)  
    {  
		//I used the coro http server example,the reason for processing uploads here is to write the file first  
		using upload protocol rfc1867 processing......  
		//my question is: how to call the incoming socket handle to send   
		//an HTTP message,or process HTTP messages as in the example  
    }  
    else  
    {  
		n = this->on_body_impl(string_view{ p,  
			beast::detail::clamp(len_, n) }, ec);  
    }

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-29 06:05:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2041#issuecomment-665452011  

I'm wondering whether you'd be better off by splitting the message receive into two calls:  
1. read the header with `async_read_header` https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
2. perform logic based on `header.target`  
3. read and processs the body (if any) with a sequence of calls to `async_read_some` https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html

---

## Comment 2

> Username: iams13  
> Created at: 2020-07-29 06:08:45 UTC  
> Updated at: 2020-07-29 06:31:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2041#issuecomment-665453081  

> I'm wondering whether you'd be better off by splitting the message receive into two calls:  
>   
> 1. read the header with `async_read_header` https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
> 2. perform logic based on `header.target`  
> 3. read and processs the body (if any) with a sequence of calls to `async_read_some` https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html  
  
thank you very much for your reply. I will try  
  
there is another question: if upload a very large file(EG 1TB), will the dynamic buffer be too large?
