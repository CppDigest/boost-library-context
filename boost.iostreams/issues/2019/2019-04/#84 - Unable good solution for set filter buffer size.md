# #84 - Unable good solution for set filter buffer size [Open]

> Username: heretic13  
> Created at: 2019-04-17 08:00:27 UTC  
> Updated at: 2025-10-20 17:26:24 UTC  
> Url: https://github.com/boostorg/iostreams/issues/84  

Hello.  
  
I want to make a filter for more beautiful text output. To do this, I need to collect a large amount of data, make beauty and output to std :: cout. Ideally, I should only process data when flush () is used;  
  
I could not do it !!!  
  
Here is an example.  
  
`  
#include <iostream>  
  
// I don't like this  
//#define BOOST_IOSTREAMS_DEFAULT_FILTER_BUFFER_SIZE  1024  
  
#include <boost/iostreams/filtering_stream.hpp>  
  
struct toupper_filter  
{  
	typedef char                             char_type;  
	typedef boost::iostreams::multichar_output_filter_tag  category;  
  
	template<typename Sink>  
	std::streamsize write(Sink& snk, const char* s, std::streamsize n)  
	{  
// point to interest n=128. But i need more large (many kbs). Idealy i need to enter this only after flush()  
		std::streamsize rest = n;  
  
		for (; rest; --rest, s++)  
		{  
			int c = toupper(*s);  
  
			bool bOk = boost::iostreams::put(snk, c);  
  
			if (!bOk)  
				break;  
		}  
  
		return n - rest;  
	}  
};  
  
int main()  
{  
	namespace io = boost::iostreams;  
  
	io::filtering_ostream out;  
  
	out.push(toupper_filter());  
	out.push(std::cout);  
  
	// This does not work!!!  
	out.set_filter_buffer_size(1024);  
  
	// dont use std::endl  
	for (size_t x = 0; x < 100; ++x)  
	{  
		out << "something\n";  
		out << "anything\n";  
	}  
  
	out.flush();  
  
	return 0;  
}  
`  
out.set_filter_buffer_size(1024); -  this not work!  
  
Change BOOST_IOSTREAMS_DEFAULT_FILTER_BUFFER_SIZE  is work, but it affected to all boost::iostream library.  I think it is a bad idea.

---

## Comment 1

> Username: tokboxcarter  
> Created at: 2025-10-20 17:26:24 UTC  
> Url: https://github.com/boostorg/iostreams/issues/84#issuecomment-3423062654  

https://wolf-clustering.github.io/landau/
