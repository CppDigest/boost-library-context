# #319 - boost::tuples::tuple [Closed]

> Username: klasing  
> Created at: 2019-08-16 05:56:08 UTC  
> Updated at: 2019-08-18 05:46:23 UTC  
> Closed at: 2019-08-18 05:44:26 UTC  
> Url: https://github.com/boostorg/boost/issues/319  

After reading the article at [https://www.boost.org/doc/libs/1_67_0/libs/tuple/doc/html/tuple_users_guide.html](url)  
The following note is a problem for me.  
_Note that extracting tuples with std::string or C-style string elements does not generally work, since the streamed tuple representation may not be unambiguously parseable._  
What type should I use to unambiguously parse strings from a stream into a tuple?

---

## Comment 1

> Username: klasing  
> Created at: 2019-08-16 06:50:35 UTC  
> Url: https://github.com/boostorg/boost/issues/319#issuecomment-521906028  

issue is solved last line in my code example should be: ifs >> boost::tuples::set_delimiter('#') >> tlin; output now is: (Fri, 192.168.178.14:52832 TRACE / HTTP/1.1 HTTP/1.1 200 OK 2.507500)

---

## Comment 2

> Username: klasing  
> Created at: 2019-08-18 05:44:26 UTC  
> Updated at: 2019-08-18 05:46:23 UTC  
> Url: https://github.com/boostorg/boost/issues/319#issuecomment-522293158  

Jumping to a conclusion too fast. I now come to the conclusion that I cannot stream from a file directly into a tuple. So what I did is the following. Streaming tuples into a file, line by line. Reading the file, line by line, into a std::string. Isolate the tuple elements by string manipulation. With just old fashioned C-coding practice. Here is the 'reading' part of my code.  
`void parse_logfile(  
	boost::filesystem::ifstream& ifs,  
	const boost::filesystem::path& p,  
	std::vector<tuple_logging>& vector_with_tuples  
)  
{  
	typedef std::string one_line_from_logging;  
  
	one_line_from_logging str;  
	tuple_logging tlin;  
  
	// clear vector  
	vector_with_tuples.clear();  
  
	// open log file for reading  
	ifs.open(p);  
  
	// read one line from log file, until eof  
	while (std::getline(ifs, str))  
	{  
		size_t sBegin = 0, sEnd = 0;  
		// 1  
		// first character on a line is '(',  
		// start at sBegin = 1  
		sBegin = sEnd + 1;  
		sEnd = str.find('#', sBegin);  
		std::string current_gmt_ = str.substr(sBegin, sEnd - sBegin);  
		// 2  
		sBegin = sEnd + 1;  
		sEnd = str.find('#', sBegin);  
		std::string remote_endpoint_ = str.substr(sBegin, sEnd - sBegin);  
		// 3  
		sBegin = sEnd + 1;  
		sEnd = str.find('#', sBegin);  
		std::string request_ = str.substr(sBegin, sEnd - sBegin);  
		// 4  
		sBegin = sEnd + 1;  
		sEnd = str.find('#', sBegin);  
		std::string response_ = str.substr(sBegin, sEnd - sBegin);  
		// 5  
		sBegin = sEnd + 1;  
		// last character on a line is ')'  
		sEnd = str.find(')', sBegin);  
		std::string elapsed_time_ = str.substr(sBegin, sEnd - sBegin);  
  
		// create tuple from parsed log data out of file  
		tlin = boost::tuples::make_tuple(  
			current_gmt_,  
			remote_endpoint_,  
			request_,  
			response_,  
			elapsed_time_  
		);  
  
		// set tuple into vector  
		vector_with_tuples.push_back(tlin);  
	}  
  
	// close log file  
	ifs.close();  
}  
`
