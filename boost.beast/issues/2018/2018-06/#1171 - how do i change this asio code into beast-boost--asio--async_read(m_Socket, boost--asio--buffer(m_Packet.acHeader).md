# #1171 - how do i change this asio code into beast?boost::asio::async_read(m_Socket, boost::asio::buffer(m_Packet.acHeader) [Closed]

> Username: opengpu  
> Created at: 2018-06-29 00:11:35 UTC  
> Updated at: 2018-08-05 07:30:58 UTC  
> Closed at: 2018-08-05 07:30:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1171  

`  
#define HeaderLength 8  
	union  
	{  
		char           acHeader[HeaderLength];  
		struct { int32 iMsgID, iMsgLength; };  
	};  
	std::vector<char> vcBody;  
  
void TcpSession::do_read_header()  
{  
	auto self(shared_from_this());  
	**boost::asio::async_read(m_Socket, boost::asio::buffer(m_Packet.acHeader),**  
		[this, self](const boost::system::error_code& error, std::size_t bytes_transferred)  
	{  
		if (!error)  
		{  
			**ASSERT(bytes_transferred == Packet::HeaderLength);**  
			int32 iMsgID = ntohl(m_Packet.iMsgID);  
			int32 iMsgLength = ntohl(m_Packet.iMsgLength);  
			if (IS_VALID(iMsgID))  
			{  
				if (iMsgLength > 0)  
				{  
					**m_Packet.vcBody.resize(iMsgLength);**  
					do_read_body(iMsgID);  
				}  
...  
}  
void TcpSession::do_read_body(int32 iMsgID)  
{  
	auto self(shared_from_this());  
	**boost::asio::async_read(m_Socket, boost::asio::buffer(m_Packet.vcBody),**  
		[this, self, iMsgID](const boost::system::error_code& error, std::size_t bytes_transferred)  
	{  
		if (!error)  
		{  
			**ASSERT(bytes_transferred == m_Packet.vcBody.size());**  
			google::protobuf::Message* pMsg = CreateMessage(iMsgID);  
			if (pMsg)  
			{  
				if (pMsg->IsInitialized())  
				{  
					//if (pMsg->ParseFromString())  
					//if (pMsg->ParseFromIstream())  
					if (pMsg->ParseFromArray(m_Packet.vcBody.data(), m_Packet.vcBody.size()))  
					{  
					}  
...  
}  
`  
////////////////////////////////////////////////////////////////////////////////////  
  
can i change this asio code into beast like this as following?  
////////////////////////////////////////////////////////////////////////////////////  
`  
protected:  
	boost::beast::websocket::stream<boost::asio::ip::tcp::socket> ws_;  
	boost::asio::strand<boost::asio::io_context::executor_type> strand_;  
	//boost::beast::multi_buffer buffer_;  
	Packet m_Packet;  
	int32 m_iMsgID;`  
  
  
void session::do_read_header()  
{  
	// Read a message into our buffer  
	**ws_.async_read_some(  
		boost::asio::buffer(m_Packet.acHeader),**  
		boost::asio::bind_executor(  
			strand_,  
			std::bind(  
				&session::on_read_header,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2)));  
}  
void session::on_read_header(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
	// This indicates that the session was closed  
	if (ec == boost::beast::websocket::error::closed)  
	{  
		std::cout << "INFO: @on_read_header, (ec == websocket::error::closed)" << std::endl;  
		return;  
	}  
	if (!ec)  
	{  
		**ASSERT(bytes_transferred == Packet::HeaderLength);**  
		int32 iMsgID = ntohl(m_Packet.iMsgID);  
		int32 iMsgLength = ntohl(m_Packet.iMsgLength);  
		if (IS_VALID(iMsgID))  
		{  
			if (iMsgLength > 0)  
			{  
                                **m_Packet.vcBody.resize(iMsgLength);**  
				do_read_body(iMsgID);  
			}  
...  
}  
void session::do_read_body(int32 iMsgID)  
{  
	m_iMsgID = iMsgID;  
	// Read a message into our buffer  
	**ws_.async_read_some(  
		boost::asio::buffer(m_Packet.vcBody),**  
		boost::asio::bind_executor(  
			strand_,  
			std::bind(  
				&session::on_read_body,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2)));  
}  
void session::on_read_body(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
	// This indicates that the session was closed  
	if (ec == boost::beast::websocket::error::closed)  
	{  
		std::cout << "INFO: @on_read_body, (ec == websocket::error::closed)" << std::endl;  
		return;  
	}  
	if (!ec)  
	{  
		**ASSERT(bytes_transferred == m_Packet.vcBody.size());**  
		google::protobuf::Message* pMsg = CreateMessage(m_iMsgID);  
		if (pMsg)  
		{  
			if (pMsg->IsInitialized())  
			{  
				//if (pMsg->ParseFromString())  
				//if (pMsg->ParseFromIstream())  
				if (pMsg->ParseFromArray(m_Packet.vcBody.data(), m_Packet.vcBody.size()))  
				{//FIXME!  
				}  
...  
}  
`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-29 00:18:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-401210727  

I have no idea what you're trying to do here.

---

## Comment 2

> Username: opengpu  
> Created at: 2018-06-29 00:22:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-401211297  

boost::asio::async_read(m_Socket, boost::asio::buffer(m_Packet.acHeader)...  
beast have no such API...  
thanks you for reply so quickly!  
my msgPacket is two int(id & size) and then dynamicBuffer. so i want to first read these two int, and then use this size to set the dynamicBuffer size, and then read it.

---

## Comment 3

> Username: opengpu  
> Created at: 2018-06-29 00:32:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-401212833  

sorry, first time issue, so i dont know whether here is like forum that can ask questions.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-29 01:18:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-401219204  

Are you trying to use WebSocket?

---

## Comment 5

> Username: opengpu  
> Created at: 2018-06-29 02:08:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-401225958  

yes,the asio code is for TCP. and the beast code is for WebSocket. i am using beast as my server network module.

---

## Comment 6

> Username: opengpu  
> Created at: 2018-06-29 06:32:44 UTC  
> Updated at: 2018-06-29 06:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-401261396  

thank you!

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-07-29 07:26:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-408657703  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-08-05 07:30:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1171#issuecomment-410501738  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
