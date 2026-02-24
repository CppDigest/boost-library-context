# #2087 - 关于解析器中的body_limit设置提问 [Open]

> Username: Yinpinghua  
> Created at: 2020-09-14 06:51:44 UTC  
> Updated at: 2024-06-06 05:34:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2087  

麻烦问下，当用chunked 传输，就可以检测body_limit的限制，而非chunked传输就不检测body_limit限制

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-14 06:59:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-691858886  

我会检查。 听起来像个虫子。  
  
----------------------------------------------------------------  
  
I will check. It sounds like a bug.

---

## Comment 2

> Username: Yinpinghua  
> Created at: 2020-09-14 07:12:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-691865531  

嗯，谢谢您啊

---

## Comment 3

> Username: Yinpinghua  
> Created at: 2020-09-15 09:15:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-692584721  

麻烦，问下这个bug将会在v1.75版本上修复嘛？

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-09-25 14:06:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-698949393  

I have written extra tests for basic_parser and I can find no problem with the operation of body_limit.  
  
Would you be able to share a minimal, complete program with me that demonstrates the issue?  
  
-----------------------------------------  
  
我已经为basic_parser编写了额外的测试，并且body_limit的操作没有问题。  
  
您可以与我共享一个最小的完整程序来演示该问题吗？

---

## Comment 5

> Username: Yinpinghua  
> Created at: 2020-10-22 07:47:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-714302129  

这不对吧，我之前调试过源码，还是有这问题的

---

## Comment 6

> Username: Yinpinghua  
> Created at: 2020-10-22 07:49:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-714302855  

http::async_read_header 先调用这个函数，然后再调用http::async_read 这个函数

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-10-22 08:25:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-714323556  

您可以与我共享一个最小的完整程序来演示该问题吗？

---

## Comment 8

> Username: Yinpinghua  
> Created at: 2020-10-22 08:26:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-714324400  

我这边是个代理服务

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-10-22 08:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-714326177  

请让您的客户分享一个“最小可编译的可验证示例”来说明问题。  
  
您可以通过以下方式将代码括在反引号中，以在此问题的注释中发布代码：  
  
```  
void test()  
{  
  do_something();  
}  
```

---

## Comment 10

> Username: Yinpinghua  
> Created at: 2020-10-22 08:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-714328553  

boost::optional<http::request_parser<http::buffer_body>> server_parser_;  
  beast::flat_buffer server_buffer_;  
  
void http_session::do_read_https_header()  
{  
	// Set the timeout.  
	beast::get_lowest_layer(tcp_stream_).expires_after(std::chrono::seconds(30));  
	// Read a request  
	server_parser_.emplace();  
	//  
	server_parser_->header_limit(10 * 1024 * 1024);  
	server_parser_->body_limit(500 * 1024 * 1024);  
  
	http::async_read_header(tcp_stream_, server_buffer_, *server_parser_,  
		boost::beast::bind_front_handler(  
			[self = shared_from_this()](const beast::error_code& ec, size_t byte_read){  
		try  
		{  
			beast::get_lowest_layer(self->tcp_stream_).expires_never();  
			...  
	}));  
}  
  
void http_session::do_read_http_body()  
{  
  
	beast::get_lowest_layer(tcp_stream_).expires_after(std::chrono::seconds(30));  
	memset(server_recv_buf_, 0, sizeof(server_recv_buf_));  
	server_parser_->get().body().data = server_recv_buf_;  
	server_parser_->get().body().size = sizeof(server_recv_buf_);  
  
	http::async_read(  
		tcp_stream_,  
		server_buffer_,  
		*server_parser_,  
		boost::beast::bind_front_handler(  
			[self = shared_from_this()](const beast::error_code& ec, std::size_t bytes_transferred){  
		beast::get_lowest_layer(self->tcp_stream_).expires_never();  
  
		if (ec) {  
			if (ec != beast::http::error::need_buffer) {  
				self->do_client_stream_eof();  
				self->do_tcp_stream_eof();  
				self->fail(ec, " http_session::do_read_http_body");  
				return;  
			}  
		}  
  
		self->client_send_msg_.body().data = self->server_recv_buf_;  
		self->client_send_msg_.body().size =  
			sizeof(self->server_recv_buf_) - self->server_parser_->get().body().size;  
		self->client_send_msg_.body().more = !self->server_parser_->is_done();  
  
		if (self->is_client_ssl_) {  
			self->client_send_write_https_body();  
		}  
		else {  
			self->client_send_write_http_body();  
		}  
	}));  
}

---

## Comment 11

> Username: ashtum  
> Created at: 2023-12-31 10:37:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-1872916333  

@Yinpinghua Is this still open?

---

## Comment 12

> Username: Yinpinghua  
> Created at: 2024-01-09 01:09:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-1882109891  

yes  
  
  
  
------------------&nbsp;原始邮件&nbsp;------------------  
发件人: "Mohammad ***@***.***&gt;;   
发送时间: 2024年1月8日(星期一) 晚上10:25  
收件人: ***@***.***&gt;;   
抄送: ***@***.***&gt;; ***@***.***&gt;;   
主题: Re: [boostorg/beast] 关于解析器中的body_limit设置提问 (#2087)  
  
  
  
  
  
   
Closed #2087 as not planned.  
   
—  
Reply to this email directly, view it on GitHub, or unsubscribe.  
You are receiving this because you were mentioned.Message ID: ***@***.***&gt;

---

## Comment 13

> Username: ashtum  
> Created at: 2024-01-26 07:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2087#issuecomment-1911625680  

Could you please provide a minimal, reproducible code? I have no idea what the problem is, as the provided code doesn't reveal any issues.
