# #26 - Compilation error with c++17 (GCC) or higher. [Open]

> Username: youngwolf-project  
> Created at: 2022-05-31 06:56:21 UTC  
> Updated at: 2022-06-08 07:26:17 UTC  
> Url: https://github.com/boostorg/lambda/issues/26  

template<typename _Can, typename _Predicate>  
void do_something_to_all(_Can& __can, const _Predicate& __pred) {for (BOOST_AUTO(iter, __can.begin()); iter != __can.end(); ++iter) __pred(*iter);}  
  
template\<typename _Can\>  
size_t get_size_in_byte(const _Can& __can)  
{  
	size_t size_in_byte = 0;  
	do_something_to_all(__can, size_in_byte += boost::lambda::bind(&_Can::value_type::size, boost::lambda::_1));  
	return size_in_byte;  
}  
  
std::list\<std::string\> can;  
can.emplace_back("123");  
can.emplace_back("456");  
printf("%zu\n", get_size_in_byte(can));

---

## Comment 1

> Username: youngwolf-project  
> Created at: 2022-05-31 08:00:27 UTC  
> Url: https://github.com/boostorg/lambda/issues/26#issuecomment-1141794857  

The reason is that lambda::bind cannot bind a function that has _GLIBCXX_NOEXCEPT attribute, before c++17, it can.
