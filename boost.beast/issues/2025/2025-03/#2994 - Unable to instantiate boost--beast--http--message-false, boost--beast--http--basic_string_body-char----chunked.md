# #2994 - Unable to instantiate boost::beast::http::message<false, boost::beast::http::basic_string_body<char>>::chunked [Closed]

> Username: climb4  
> Created at: 2025-03-12 15:38:51 UTC  
> Updated at: 2025-06-22 20:28:39 UTC  
> Closed at: 2025-05-10 19:56:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2994  

Ever since I updated my installation multiple months ago I keep running into this compilation error, earlier this used to compile.  
I only work on this sporadically so I don't remember which version it used to work on but I'm currently on 1.87.0  
  
I basically have a variant of responses of multiple body types that I want to transform into a `message_generator`.  
I have to use a variant because I need access to the header fields between the variant being returned by a handler functor and sending the response off which is impossible with a `message_generator` afaik.  
  
```  
In file included from /usr/include/boost/intrusive/rbtree_algorithms.hpp:33:  
/usr/include/boost/intrusive/bstree_algorithms.hpp:758:22: error: no matching function for call to 'uncast'  
  758 |       node_ptr end = detail::uncast(header);  
      |                      ^~~~~~~~~~~~~~  
/usr/include/boost/intrusive/bstree.hpp:392:28: note: in instantiation of function template specialization 'boost::intrusive::bstree_algorithms<boost::intrusive::rbtree_node_traits<void *>>::find<boost::core::basic_string_view<char>, boost::intrusive::detail::key_nodeptr_comp<boost::beast::http::basic_fields<std::allocator<char>>::key_compare, boost::intrusive::bhtraits<boost::beast::http::basic_fields<std::allocator<char>>::element, boost::intrusive::rbtree_node_traits<void *>, boost::intrusive::normal_link, boost::intrusive::dft_tag, 3>, boost::move_detail::identity<boost::beast::http::basic_fields<std::allocator<char>>::element>>>' requested here  
  392 |          (node_algorithms::find(this->header_ptr(), key, this->key_node_comp(comp)), this->priv_value_traits_ptr());  
      |                            ^  
/usr/include/boost/beast/http/impl/fields.hpp:697:26: note: in instantiation of function template specialization 'boost::intrusive::bstbase2<boost::intrusive::bhtraits<boost::beast::http::basic_fields<std::allocator<char>>::element, boost::intrusive::rbtree_node_traits<void *>, boost::intrusive::normal_link, boost::intrusive::dft_tag, 3>, void, boost::beast::http::basic_fields<std::allocator<char>>::key_compare, boost::intrusive::RbTreeAlgorithms, void>::find<boost::core::basic_string_view<char>, boost::beast::http::basic_fields<std::allocator<char>>::key_compare>' requested here  
  697 |     auto const it = set_.find(  
      |                          ^  
/usr/include/boost/beast/http/impl/fields.hpp:688:12: note: in instantiation of member function 'boost::beast::http::basic_fields<std::allocator<char>>::find' requested here  
  688 |     return find(to_string(name));  
      |            ^  
/usr/include/boost/beast/http/impl/fields.hpp:503:21: note: in instantiation of member function 'boost::beast::http::basic_fields<std::allocator<char>>::find' requested here  
  503 |     auto const it = find(name);  
      |                     ^  
/usr/include/boost/beast/http/impl/fields.hpp:803:9: note: in instantiation of member function 'boost::beast::http::basic_fields<std::allocator<char>>::operator[]' requested here  
  803 |         (*this)[field::transfer_encoding]};  
      |         ^  
/usr/include/boost/beast/http/message.hpp:726:22: note: in instantiation of member function 'boost::beast::http::basic_fields<std::allocator<char>>::get_chunked_impl' requested here  
  726 |         return this->get_chunked_impl();  
      |                      ^  
/usr/include/boost/beast/http/impl/serializer.hpp:80:15: note: in instantiation of member function 'boost::beast::http::message<false, boost::beast::http::basic_string_body<char>>::chunked' requested here  
   80 |         if(m_.chunked())  
      |               ^  
/usr/include/boost/beast/http/impl/message_generator.hpp:50:13: note: in instantiation of function template specialization 'boost::beast::http::serializer<false, boost::beast::http::basic_string_body<char>>::next<boost::beast::http::message_generator::generator_impl<false, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char>>>::visit>' requested here  
   50 |         sr_.next(ec, visit{*this});  
      |             ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.2.1/../../../../include/c++/14.2.1/bits/unique_ptr.h:93:2: note: in instantiation of member function 'boost::beast::http::message_generator::generator_impl<false, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char>>>::prepare' requested here  
   93 |         delete __ptr;  
      |         ^  
...: note: in instantiation of function template specialization 'boost::beast::http::message_generator::message_generator<false, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char>>>' requested here  
   97 |                                 boost::beast::http::message_generator result_generator = std::visit([]<typename body_t>(boost::beast::http::response<body_t> response) -> boost::beast::http::message_generator { return {std::move(response)}; }, std::move(result_value));  
```  
  
Am I doing something wrong here?

---

## Comment 1

> Username: ashtum  
> Created at: 2025-03-12 15:52:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2718348409  

Could you please provide an example of your variant and how you are trying to access it?

---

## Comment 2

> Username: climb4  
> Created at: 2025-03-13 17:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2722202620  

The code in question looks like this:  
  
```c++  
  
using http_handler_result_t = std::optional<std::variant<  
		boost::beast::http::response<boost::beast::http::string_body>,  
		boost::beast::http::response<boost::beast::http::empty_body>,  
		boost::beast::http::response<boost::beast::http::dynamic_body>,  
		boost::beast::http::response<boost::beast::http::file_body>,  
		boost::beast::http::response<boost::beast::http::buffer_body>>>;  
  
http_handler_result_t handler_result = co_await invoke_handler(parser, stream, buffer);  
if (!handler_result.has_value())  
	co_return;  
auto result_value = *std::move(handler_result);  
auto needs_eof = std::visit(  
	[&](auto &response) {  
		if (response.need_eof())  
			return true;  
		response.set(boost::beast::http::field::keep_alive, "...");  
		response.set(boost::beast::http::field::connection, "Keep-Alive");  
		return false;  
	},  
	result_value);  
boost::beast::http::message_generator result_generator = std::visit([]<typename body_t>(boost::beast::http::response<body_t> response) -> boost::beast::http::message_generator { return {std::move(response)}; }, std::move(result_value));  
```  
  
Weirdly enough similar code often compiles, for example:  
  
```c++  
#include <boost/beast.hpp>  
  
int main()  
{  
	std::variant<  
		boost::beast::http::response<boost::beast::http::string_body>,  
		boost::beast::http::response<boost::beast::http::empty_body>,  
		boost::beast::http::response<boost::beast::http::dynamic_body>,  
		boost::beast::http::response<boost::beast::http::file_body>,  
		boost::beast::http::response<boost::beast::http::buffer_body>>  
		variant = {};  
	boost::beast::http::message_generator result_generator = std::visit([]<typename body_t>(boost::beast::http::response<body_t> response) -> boost::beast::http::message_generator { return {std::move(response)}; }, std::move(variant));  
	(void) result_generator;  
}  
```

---

## Comment 3

> Username: ashtum  
> Created at: 2025-03-14 05:29:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2723591102  

What compiler flags are you using? Without a reproducible example, it's difficult to determine the issue.

---

## Comment 4

> Username: ashtum  
> Created at: 2025-04-27 16:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2833534657  

@climb4, Any update on this?

---

## Comment 5

> Username: climb4  
> Created at: 2025-06-13 15:52:48 UTC  
> Updated at: 2025-06-13 15:54:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2970814210  

Hi @ashtum   
Sorry for the delay.  
  
I was finally able to reproduce the error in a separate project.  
  
The error only occurs when the call in question is located in a template inside of a module interface unit.  
  
I was able to reduce the code to these two files:  
  
```c++  
//main.cpp  
import TESTM;  
  
int main()  
{  
	test::run(5);  
}  
```  
  
```c++  
//test.cppm  
module;  
#include <boost/beast.hpp>  
export module test;  
  
export namespace test {  
	template<typename arg_t>  
	void run(arg_t &&arg)  
	{  
		using http_response_t = std::variant<  
			boost::beast::http::response<boost::beast::http::empty_body>,  
			boost::beast::http::response<boost::beast::http::string_body>,  
			boost::beast::http::response<boost::beast::http::dynamic_body>,  
			boost::beast::http::response<boost::beast::http::file_body>,  
			boost::beast::http::response<boost::beast::http::buffer_body>>;  
		  
		(void) arg;  
		http_response_t resp;  
		auto message = std::visit(  
			[]<typename body_t>(boost::beast::http::response<body_t> response) {  
				return boost::beast::http::message_generator{std::move(response)};  
			},  
			std::move(resp));  
		(void) message;  
	}  
} // namespace test  
```  
  
I'm using clang 19.1.7 with the following command line:  
`/usr/bin/clang++  -I/usr/lib/clang/19/include -g -std=gnu++23 -fcolor-diagnostics -Wall -Wextra -pedantic -Werror -MD -MT main.cpp.o -MF main.cpp.o.d @main.cpp.o.modmap -o main.cpp.o -c ./main.cpp`  
  
I'm using CMake, a matching CMakeLists.txt would look like this:  
```CMake  
cmake_minimum_required(VERSION 3.29)  
set(CMAKE_CXX_STANDARD 23)  
add_compile_options(-Wall -Wextra -pedantic -Werror)  
  
add_executable(TEST  
        main.cpp)  
target_sources(TEST PUBLIC FILE_SET CXX_MODULES FILES  
        test.cppm)  
```

---

## Comment 6

> Username: climb4  
> Created at: 2025-06-22 15:14:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2994270335  

Hi @ashtum   
Did you have time to try to reproduce the issue yet?  
Just want to make sure this isn't overlooked because I can't reopen the issue.

---

## Comment 7

> Username: ashtum  
> Created at: 2025-06-22 18:11:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2994356317  

> Hi [@ashtum](https://github.com/ashtum) Did you have time to try to reproduce the issue yet? Just want to make sure this isn't overlooked because I can't reopen the issue.  
  
Hi,  
This appears to be a bug in older versions of Clang. It has been fixed in Clang 20.1.0: https://godbolt.org/z/rb1nM59b5

---

## Comment 8

> Username: climb4  
> Created at: 2025-06-22 20:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2994#issuecomment-2994432181  

Thanks a lot!
