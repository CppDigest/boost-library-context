# #176 - How to exit from a generator without returning a value [Closed]

> Username: josuegomes  
> Created at: 2024-04-23 20:39:42 UTC  
> Updated at: 2024-04-24 14:09:25 UTC  
> Closed at: 2024-04-24 14:08:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/176  

I'm converting this code that uses Lewis Baker's cppcoro library to use Boost.Cobalt  
  
  
```cpp  
#include <cppcoro/generator.hpp>  
  
#include <iostream>  
#include <fstream>  
#include <string>  
  
cppcoro::generator<std::string> read_script_file(std::ifstream& script)  
{  
	std::string line;  
	while (std::getline(script, line)) {  
		if (line.empty()) {  
			continue;  
		}  
		if (line[0] == '#') {  
			continue;  
		}  
		co_yield line;  
	}  
}  
  
void print_script_file()  
{  
	std::ifstream ifs("script");  
  
	for (const auto line : read_script_file(ifs)) {  
		std::cout << line << "\n";  
	}  
}  
```  
  
And this is the Boost.Cobalt version:  
  
```cpp  
#include <boost/cobalt.hpp>  
  
#include <fstream>  
#include <iostream>  
#include <string>  
  
boost::cobalt::generator<std::string> read_script_file(std::ifstream& script)  
{  
	std::string line;  
	while (std::getline(script, line)) {  
		boost::algorithm::trim(line);  
		if (line.empty()) {  
			continue;  
		}  
		if (line[0] == '#') {  
			continue;  
		}  
		co_yield line;  
	}  
	co_return "--exit";  
}  
  
void print_script_file()  
{  
	std::ifstream ifs("script");  
  
	while (true) {  
		const auto line = co_await read_script_file(ifs);  
		if (line == "--exit") {  
			break;  
		}  
		std::cout << line << "\n";  
	}  
}  
```  
  
The cppcoro version is much more elegant as it doesn't need to return a "--exit" string to finish the  
generator.  
  
Is there a better way to write the Boost.Cobalt version?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-23 21:02:23 UTC  
> Updated at: 2024-04-23 21:03:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/176#issuecomment-2073438490  

You can query the generator object:  
https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#generator-outline  
```C++  
#include <boost/cobalt.hpp>  
  
using namespace boost;  
  
cobalt::generator<int> my_generator()  
{  
  for (int i = 0; i < 10; i++)  
    co_yield i;  
  co_return 10;  
}  
  
cobalt::main co_main(int argc, char* argv[])  
{  
  // create the generator  
  auto g = my_generator();  
  while (g)  
    printf("Generator %d\n", co_await g);  
  co_return 0;  
}  
  
```

---

## Comment 2

> Username: josuegomes  
> Created at: 2024-04-23 21:47:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/176#issuecomment-2073519923  

Modified to:  
  
```cpp  
boost::cobalt::generator<std::string> read_script_file(std::ifstream& script)  
{  
	std::string line;  
	while (std::getline(script, line)) {  
		boost::algorithm::trim(line);  
		if (line.empty()) {  
			continue;  
		}  
		if (line[0] == '#') {  
			continue;  
		}  
		co_yield line;  
	}  
}  
  
void print_script_file()  
{  
	std::ifstream ifs("script");  
  
	auto g = read_script_file(ifs);  
	while (g) {  
		const auto line = co_await g;  
		std::cout << line << "\n";  
	}  
}  
```  
  
Now, I get an `cobalt::generator returned void` exception.  
  
Changed it to:  
  
```cpp  
boost::cobalt::generator<std::string> read_script_file(std::ifstream& script)  
{  
	std::string line;  
	while (std::getline(script, line)) {  
		boost::algorithm::trim(line);  
		if (line.empty()) {  
			continue;  
		}  
		if (line[0] == '#') {  
			continue;  
		}  
		co_yield line;  
	}  
	co_return {};  
}  
```  
  
Gets rid of the exception. But I don't want to return this empty string.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-04-24 00:15:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/176#issuecomment-2073694105  

Short answer: you can't.  
  
That's limitation of C++ coroutines - you can't provide a void and a value return at the same time (skipping co_return of a value is UB).   
  
So I can't provide both option, and in my opinion the current API let's you easily add an `optional` or just skip the last value in your loop (which is what I would do btw.)  
  
The difference with cppcoro is that my generators are asynchronous, i.e. not only can you co_await them, but they can off do other things. Thus I don't just resume them to see if they're done (as incrementing the iterator would do) but it actually suspends and resumes the generator. There needs to be a value there, the `if (g)` check can't resume the generator as it can't resume.  
  
But, let me illustrate the usage I have in mind (requires io_uring to be enabled on linux), which might make the asynchronicity a bit more clear:  
  
```cpp  
cobalt::generator<boost::system::result<std::string_view>> read_lines(asio::stream_file & f)  
{  
  std::string buffer;  
  while (f.is_open())  
  {  
    auto [ec, n] = co_await  
        asio::async_read_until(f, asio::dynamic_buffer(buffer), '\n',  
                               asio::as_tuple(cobalt::use_op));  
  
    // no need to copy, just point to the buffer  
    std::string_view ln{buffer.c_str(), n}; // -1 to skip the line  
    ln = boost::algorithm::trim_copy(ln);  
  
    if (!ln.empty())  
      co_yield ln;  
  
    if (ec)  
      co_return ec ;  
  
    buffer.erase(0, n);  
  }  
  
  co_return asio::error::broken_pipe;  
}  
  
cobalt::main co_main(int argv, char** argv)  
{  
  asio::stream_file sf{co_await cobalt::this_coro::executor,  
                       argv[1], // skipping the check here for brevity.  
                       asio::stream_file::read_only};  
  
  BOOST_COBALT_FOR( // would be for co_await(auto value : read_lines(sf)) if standardized  
      auto line,  
      read_lines(sf))  
  {  
    if (line.has_error() && line.error() != asio::error::eof)  
      std::cerr << "Error occured: " << line.error() << std::endl;  
    else if (line.has_value())  
      std::cout << "Read line '" << *line << "'" << std::endl;  
  }  
  
  co_return 0;  
}  
```  
  
The main feature here is that you can have multiple `read_lines` on a single thread that will all make progress at the same time.

---

## Comment 4

> Username: josuegomes  
> Created at: 2024-04-24 14:09:24 UTC  
> Url: https://github.com/boostorg/cobalt/issues/176#issuecomment-2075044475  

Thank you. It's much clearer now. I appreciate your patience.
