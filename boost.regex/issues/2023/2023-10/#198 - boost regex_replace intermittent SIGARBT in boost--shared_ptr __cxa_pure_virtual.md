# #198 - [Not Issue]boost regex_replace intermittent SIGARBT in boost::shared_ptr __cxa_pure_virtual [Closed]

> Username: linholmes  
> Created at: 2023-10-22 13:29:23 UTC  
> Updated at: 2023-12-13 03:03:07 UTC  
> Closed at: 2023-12-13 03:03:07 UTC  
> Url: https://github.com/boostorg/regex/issues/198  

I use boost_174, jemalloc 5.2.1   
**The crash backtrace is**  
#0  0x00007f6f5d766387 in raise () from /lib64/libc.so.6  
boostorg/boost#1  0x00007f6f5d767a78 in abort () from /lib64/libc.so.6  
boostorg/boost#2  0x00007f6f5e076a95 in __gnu_cxx::__verbose_terminate_handler() () from /lib64/libstdc++.so.6  
boostorg/boost#3  0x00007f6f5e074a06 in ?? () from /lib64/libstdc++.so.6  
boostorg/boost#4  0x00007f6f5e074a33 in std::terminate() () from /lib64/libstdc++.so.6  
boostorg/boost#5  0x00007f6f5e07559f in __cxa_pure_virtual () from /lib64/libstdc++.so.6  
boostorg/boost#6  0x0000000001181c97 in boost::detail::sp_counted_base::release (this=0x7f6f534835c0) at /opt/boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:120  
boostorg/boost#7  0x0000000001181d23 in boost::detail::shared_count::~shared_count (this=0x7f6f539941c0, __in_chrg=<optimized out>)  
    at /opt/boost/include/boost/smart_ptr/detail/shared_count.hpp:432  
boostorg/boost#8  0x0000000001412ade in boost::shared_ptr<boost::re_detail_107400::named_subexpressions>::~shared_ptr (this=0x7f6f539941b8, __in_chrg=<optimized out>)  
    at /opt/boost/include/boost/smart_ptr/shared_ptr.hpp:335  
boostorg/boost#9  0x0000000001412b6c in boost::match_results<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > > >::~match_results (this=0x7f6f53994180, __in_chrg=<optimized out>) at /opt/boost/include/boost/regex/v4/match_results.hpp:119  
boostorg/boost#10 0x000000000148c416 in boost::regex_iterator_implementation<__gnu_cxx::__normal_iterator<char const*, std::string>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::~regex_iterator_implementation (this=0x7f6f53994180, __in_chrg=<optimized out>) at /opt/boost/include/boost/regex/v4/regex_iterator.hpp:40  
boostorg/boost#11 0x000000000148c436 in boost::checked_delete<boost::regex_iterator_implementation<__gnu_cxx::__normal_iterator<char const*, std::string>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > >  
    (x=0x7f6f53994180) at /opt/boost/include/boost/core/checked_delete.hpp:36  
boostorg/boost#12 0x000000000148f152 in boost::detail::sp_counted_impl_p<boost::regex_iterator_implementation<__gnu_cxx::__normal_iterator<char const*, std::string>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > >::dispose (this=0x7f6f53dccc40) at /opt/boost/include/boost/smart_ptr/detail/sp_counted_impl.hpp:89  
boostorg/boost#13 0x0000000001181c97 in boost::detail::sp_counted_base::release (this=0x7f6f53dccc40) at /opt/boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:120  
boostorg/boost#14 0x0000000001181d23 in boost::detail::shared_count::~shared_count (this=0x7ffc4e5a06e8, __in_chrg=<optimized out>)  
    at /opt/boost/include/boost/smart_ptr/detail/shared_count.hpp:432  
boostorg/boost#15 0x000000000148996c in boost::shared_ptr<boost::regex_iterator_implementation<__gnu_cxx::__normal_iterator<char const*, std::string>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > >::~shared_ptr (this=0x7ffc4e5a06e0, __in_chrg=<optimized out>) at /opt/boost/include/boost/smart_ptr/shared_ptr.hpp:335  
boostorg/boost#16 0x000000000148b129 in boost::shared_ptr<boost::regex_iterator_implementation<__gnu_cxx::__normal_iterator<char const*, std::string>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > >::reset (this=0x7ffc4e5a07b0) at /opt/boost/include/boost/smart_ptr/shared_ptr.hpp:687  
boostorg/boost#17 0x000000000148a20d in boost::regex_iterator<__gnu_cxx::__normal_iterator<char const*, std::string>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::regex_iterator (this=0x7ffc4e5a07b0,   
    a=47 '/', b=0 '\000', re=..., m=boost::regex_constants::format_first_only) at /opt/boost/include/boost/regex/v4/regex_iterator.hpp:111  
boostorg/boost#18 0x0000000001495666 in boost::regex_replace<boost::re_detail_107400::string_out_iterator<std::string>, __gnu_cxx::__normal_iterator<char const*, std::string>, boost::regex_traits<char, boost::cpp_regex_traits<char> >, char, std::string> (out=..., first=47 '/', last=0 '\000', e=..., fmt="/", flags=boost::regex_constants::format_first_only)  
    at /opt/boost/include/boost/regex/v4/regex_replace.hpp:46  
boostorg/boost#19 0x0000000001494d31 in boost::regex_replace<boost::regex_traits<char, boost::cpp_regex_traits<char> >, char, std::string> (s="/si_er/frontend/dwr/table/init.action", e=..., fmt="/",   
    flags=boost::regex_constants::format_first_only) at /opt/boost/include/boost/regex/v4/regex_replace.hpp:80  
  
  
**Frame info**  
(gdb) frame 9  
boostorg/boost#9  0x0000000001412b6c in boost::match_results<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > > >::~match_results (this=0x7f6f53994180, __in_chrg=<optimized out>) at /opt/boost/include/boost/regex/v4/match_results.hpp:119  
119    ~match_results(){}  
(gdb) p *this  
$25 = {m_subs = std::vector of length 3, capacity 3 = {  
    {<std::pair<__gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > >, __gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > > >> = {first = 0 '\000', second = 0 '\000'}, matched = false},   
    {<std::pair<__gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > >, __gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > > >> = {first = 47 '/', second = 46 '.'}, matched = true},   
    {<std::pair<__gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > >, __gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > > >> = {first = 46 '.', second = 0 '\000'}, matched = false}}, m_base = 47 '/',   
  m_null = {<std::pair<__gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > >, __gnu_cxx::__normal_iterator<char const*, std::basic_string<char, std::char_traits<char>, std::allocator<char> > > >> = {first = <error reading variable: Cannot access memory at address 0x0>, m_named_subs = {px = 0x7f6f5380cf80, pn = {pi_ = 0x7f6f534835c0}},   
  m_last_closed_paren = 0, m_is_singular = true}  
(gdb) down  
boostorg/boost#8  0x0000000001412ade in boost::shared_ptr<boost::re_detail_107400::named_subexpressions>::~shared_ptr (this=0x7f6f539941b8, __in_chrg=<optimized out>)  
    at /opt/boost/include/boost/smart_ptr/shared_ptr.hpp:335  
335 template<class T> class shared_ptr  
(gdb) p this  
$26 = (boost::shared_ptr<boost::re_detail_107400::named_subexpressions> * const) 0x7f6f539941b8  
(gdb) p *this  
$27 = {px = 0x7f6f5380cf80, pn = {pi_ = 0x7f6f534835c0}}  
(gdb) down  
boostorg/boost#7  0x0000000001181d23 in boost::detail::shared_count::~shared_count (this=0x7f6f539941c0, __in_chrg=<optimized out>)  
    at /opt/boost/include/boost/smart_ptr/detail/shared_count.hpp:432  
432         if( pi_ != 0 ) pi_->release();  
(gdb) p pi_  
$28 = (boost::detail::sp_counted_base *) 0x7f6f534835c0  
(gdb) p *pi_  
$29 = {_vptr.sp_counted_base = 0x2099fb0 <vtable for boost::detail::sp_counted_base+16>, use_count_ = 0, weak_count_ = 0}  
(gdb) p *this  
$30 = {pi_ = 0x7f6f534835c0}  
(gdb) down  
boostorg/boost#6  0x0000000001181c97 in boost::detail::sp_counted_base::release (this=0x7f6f534835c0) at /opt/boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:120  
120             dispose();  
(gdb) p *this  
$31 = {_vptr.sp_counted_base = 0x2099fb0 <vtable for boost::detail::sp_counted_base+16>, use_count_ = 0, weak_count_ = 0}  
  
  
**break code**  
release  m_named_subs pi_  
boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:120  
  
    void release() // nothrow  
    {  
        if( atomic_decrement( &use_count_ ) == 0 )  
        {  
            dispose(); //__cxa_pure_virtual  
            weak_release();  
        }  
    }  
  
I have written a demo to reproduce the issue. In my demo, the m_named_subs will not be released because use_count_ > 0.  
  
I want to know if 'm_named_subs' should not be released. Also, why does '__cxa_pure_virtual' occur during 'boost::detail::sp_counted_base::release'?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-10-24 15:39:35 UTC  
> Url: https://github.com/boostorg/regex/issues/198#issuecomment-1777507452  

>  Also, why does '__cxa_pure_virtual' occur during 'boost::detail::sp_counted_base::release'?  
  
I think that's the crux of the issue, __cxa_pure_virtual should never ever be called in a program.  I would start by updating to the latest Boost release, and if that doesn't fix things, my hunch would be that your program has object lifetime issues (order of construction/destruction resulting in a call being made into an already destructed object).

---

## Comment 2

> Username: linholmes  
> Created at: 2023-10-30 04:45:05 UTC  
> Url: https://github.com/boostorg/regex/issues/198#issuecomment-1784472440  

Thanks, No issue here, but when another thread calls exit and triggers __run_exit_handlers, at the same time, my regex, which is a static variable running in the main thread and performing a regex_search, leads to the __cxa_pure_virtual error.
