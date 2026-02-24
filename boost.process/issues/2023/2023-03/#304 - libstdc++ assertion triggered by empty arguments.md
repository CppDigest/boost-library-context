# #304 - libstdc++ assertion triggered by empty arguments [Closed]

> Username: reddwarf69  
> Created at: 2023-03-24 12:04:28 UTC  
> Updated at: 2023-06-28 12:24:18 UTC  
> Closed at: 2023-06-28 12:24:18 UTC  
> Url: https://github.com/boostorg/process/issues/304  

If you build  
```  
#include <boost/process/system.hpp>  
  
int main() {  
    boost::process::system("/bin/true", "");  
}  
```  
with -D_GLIBCXX_ASSERTIONS.  
  
You get the following error when running it  
```  
$ ./test  
/usr/include/c++/11/bits/basic_string.h:1115: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::reference std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::front() [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::reference = char&]: Assertion '!empty()' failed.  
Aborted (core dumped)  
```  
  
Backtrace:  
```  
#0  0x00007fdea8896e5c in __pthread_kill_implementation () from /lib64/libc.so.6  
#1  0x00007fdea8846a76 in raise () from /lib64/libc.so.6  
#2  0x00007fdea88307fc in abort () from /lib64/libc.so.6  
#3  0x00007fdea8bbee80 in std::__glibcxx_assert_fail(char const*, int, char const*, char const*) () from /lib64/libstdc++.so.6  
#4  0x0000000000408dd7 in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::front() ()  
#5  0x00000000004083b9 in boost::process::detail::posix::exe_cmd_init<char>::make_cmd() ()  
#6  0x000000000041d1dd in void boost::process::detail::posix::exe_cmd_init<char>::on_setup<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >(boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >&) ()  
#7  0x000000000041b43c in void boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >::operator()<boost::process::detail::posix::exe_cmd_init<char> >(boost::process::detail::posix::exe_cmd_init<char>&) const ()  
#8  0x0000000000419a7d in void boost::fusion::detail::for_each_linear<boost::fusion::joint_view_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, 1>, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 3>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 3>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 3>, boost::process::detail::is_initializer<mpl_::arg<-1> > >, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > > >(boost::fusion::joint_view_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, 1>, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 3>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > const&, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 3>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, 3>, boost::process::detail::is_initializer<mpl_::arg<-1> > > const&, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >&, mpl_::bool_<false>) ()  
#9  0x000000000041846b in void boost::fusion::detail::for_each_dispatch<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >, boost::fusion::forward_traversal_tag>(boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > >&, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >&, boost::fusion::forward_traversal_tag) ()  
#10 0x000000000041661f in void boost::fusion::detail::for_each<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > > >(boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > >&, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >&, mpl_::bool_<false>) ()  
#11 0x00000000004146c8 in boost::enable_if<boost::fusion::traits::is_sequence<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >, void>::type boost::fusion::for_each<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > > >(boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > >&, boost::process::detail::posix::on_setup_t<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >) ()  
#12 0x0000000000412de9 in boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::invoke(mpl_::bool_<false>, mpl_::bool_<false>) ()  
--Type <RET> for more, q to quit, c to continue without paging--   
#13 0x000000000041196f in boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::operator()() ()  
#14 0x0000000000410522 in boost::process::child boost::process::detail::basic_execute_impl<char, char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_>(char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&&) ()  
#15 0x000000000040f1da in boost::process::child boost::process::detail::execute_impl<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_>(char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&&) ()  
#16 0x000000000040daf0 in boost::process::child::child<char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_>(char const (&) [10], char const (&) [1], boost::process::detail::posix::sig_init_&&) ()  
#17 0x000000000040bde8 in int boost::process::detail::system_impl<boost::asio::io_context, char const (&) [10], char const (&) [1]>(std::integral_constant<bool, false>, std::integral_constant<bool, false>, char const (&) [10], char const (&) [1]) ()  
#18 0x0000000000409ec3 in int boost::process::system<char const (&) [10], char const (&) [1]>(char const (&) [10], char const (&) [1]) ()  
#19 0x00000000004045a4 in main ()  
(gdb)  
```  
  
"Kind" of related, FWIW, things like  
```  
boost::process::system("")  
```  
will segfault even without -D_GLIBCXX_ASSERTIONS. It's not 100% clear to me from the interface whether that, or even worse,  
```  
boost::process::system(std::vector<std::string>{})  
```  
is a contract violation, or something boost::process is supposed to handle returning an error.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-24 12:26:23 UTC  
> Url: https://github.com/boostorg/process/issues/304#issuecomment-1482720241  

Thanks for reporting this. I'll work on a fix soon - I think this is actually exception worthy.  
  
In the meantime, I'd recommend you use v2 instead. the `system` interface was trying to be too smart (and build command string etc.), so I am working to solve this by just redoing it. I'll deprecate v1 probably in 1.84.  
  
https://www.boost.org/doc/libs/1_81_0/doc/html/boost/process/v2/execute.html  
  
Just verified, it handles this correctly:  
  
https://gcc.godbolt.org/z/W3661vaxT
