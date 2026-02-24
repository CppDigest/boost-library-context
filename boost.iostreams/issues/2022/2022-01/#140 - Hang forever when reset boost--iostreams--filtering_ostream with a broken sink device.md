# #140 - Hang forever when reset boost::iostreams::filtering_ostream with a broken sink device [Open]

> Username: grapland0  
> Created at: 2022-01-13 05:43:10 UTC  
> Updated at: 2022-08-04 17:09:16 UTC  
> Url: https://github.com/boostorg/iostreams/issues/140  

boost version: 1.78  
compiler: gcc version 11.2.0  
  
typical stack trace:  
```  
#0  boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > >::write (n=4096,  
    s=0x556a785d1000 "R`G\312 X\355\231r\220\256\306\257\022\202\065%W\261\260\342\236\245\277\277\364\332\240\320\253\253\037d\256\256V\036\256v.Ko\315w\326\360\261?\027\304\276\346\066\367\347\364>\257\177-\037+\354S\001$K\320g?\342\353 XmE=\004k\354l`a\357\177{\303\323\235\210}R(\373$\252\004/\302\344\337D\261\350\\rF\370\203\255\335\210}\006\215C_\035\264y\343y\207\v\367\371\265\035.\253,\003\220[\\D\226s\311\371G\217\060\347\342\312\346|\326\321\210\340\263\237\034}\354V\324 \334\246\303|\237~\267\300ns\335D\026\327x\\\207\335{?~\345b\304\264\200\337\345\315\303\214W\264\027\037tq\233\373\256"..., this=0x7ffc25e00910) at /usr/local/include/boost/iostreams/detail/adapter/non_blocking_adapter.hpp:41  
#1  boost::iostreams::detail::write_device_impl<boost::iostreams::output>::write<boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > > (n=<optimized out>, s=<optimized out>, t=..., t=...,  
    s=<optimized out>, n=<optimized out>) at /usr/local/include/boost/iostreams/write.hpp:117  
#2  boost::iostreams::write<boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > > (n=<optimized out>, s=<optimized out>, t=..., t=..., s=<optimized out>, n=<optimized out>)  
    at /usr/local/include/boost/iostreams/write.hpp:49  
#3  boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_compressor_impl<std::allocator<char> >, std::allocator<char> >::flush<boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > > (snk=...,  
    this=<optimized out>) at /usr/local/include/boost/iostreams/filter/symmetric.hpp:231  
#4  boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_compressor_impl<std::allocator<char> >, std::allocator<char> >::flush<boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > > (snk=...,  
    this=<optimized out>) at /usr/local/include/boost/iostreams/filter/symmetric.hpp:222  
#5  boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_compressor_impl<std::allocator<char> >, std::allocator<char> >::close<boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > > (  
    mode=<optimized out>, snk=..., this=<optimized out>) at /usr/local/include/boost/iostreams/filter/symmetric.hpp:177  
#6  boost::iostreams::basic_gzip_compressor<std::allocator<char> >::close<boost::iostreams::non_blocking_adapter<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > > (this=0x556a78638048, snk=..., m=<optimized out>)  
    at /usr/local/include/boost/iostreams/filter/gzip.hpp:258  
#7  0x0000556a7744218d in boost::iostreams::detail::close_impl<boost::iostreams::detail::two_sequence>::close<boost::iostreams::basic_gzip_compressor<std::allocator<char> >, boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > (  
    which=std::_S_out, snk=warning: RTTI symbol not found for class 'boost::iostreams::stream_buffer<boost::iostreams::detail::mode_adapter<boost::iostreams::output, std::ostream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>'  
..., t=...) at /usr/local/include/boost/iostreams/close.hpp:243  
#8  boost::iostreams::close<boost::iostreams::basic_gzip_compressor<std::allocator<char> >, boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > (which=std::_S_out, snk=warning: RTTI symbol not found for class 'boost::iostreams::stream_buffer<boost::iostreams::detail::mode_adapter<boost::iostreams::output, std::ostream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>'  
..., t=...) at /usr/local/include/boost/iostreams/close.hpp:117  
#9  boost::iostreams::detail::flt_wrapper_impl<boost::iostreams::any_tag>::close<boost::iostreams::basic_gzip_compressor<std::allocator<char> >, boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > (which=std::_S_out, dev=0x556a785d8080, f=...)  
    at /usr/local/include/boost/iostreams/detail/adapter/concept_adapter.hpp:243  
#10 boost::iostreams::detail::concept_adapter<boost::iostreams::basic_gzip_compressor<std::allocator<char> > >::close<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> > > (dev=0x556a785d8080, which=std::_S_out, this=<optimized out>)  
    at /usr/local/include/boost/iostreams/detail/adapter/concept_adapter.hpp:104  
#11 boost::iostreams::detail::indirect_streambuf<boost::iostreams::basic_gzip_compressor<std::allocator<char> >, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::close_impl (which=std::_S_out, this=<optimized out>)  
    at /usr/local/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:400  
#12 boost::iostreams::detail::indirect_streambuf<boost::iostreams::basic_gzip_compressor<std::allocator<char> >, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::close_impl (this=<optimized out>, which=std::_S_out)  
    at /usr/local/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:383  
#13 0x0000556a774a7b5e in boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> >::close (which=<optimized out>, this=0x556a78638000) at /usr/local/include/boost/iostreams/detail/streambuf/linked_streambuf.hpp:82  
#14 boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> >::close (which=std::_S_out, this=0x556a78638000) at /usr/local/include/boost/iostreams/detail/streambuf/linked_streambuf.hpp:70  
#15 boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::close (m=std::_S_out, b=0x556a78638000)  
    at /usr/local/include/boost/iostreams/chain.hpp:285  
#16 boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::closer::operator() (b=0x556a78638000,  
    this=<synthetic pointer>) at /usr/local/include/boost/iostreams/chain.hpp:300  
#17 boost::iostreams::detail::execute_foreach<std::_List_iterator<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> >*>, boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::closer>(std::_List_iterator<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> >*>, std::_List_iterator<boost::iostreams::detail::linked_streambuf<char, std::char_traits<char> >*>, boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::closer) [clone .constprop.0] (first=0x556a78638000, last=last@entry=0x2, op=..., op@entry=...) at /usr/local/include/boost/iostreams/detail/execute.hpp:121  
#18 0x0000556a774405c9 in boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::chain_impl::close (  
    this=0x556a785a1140) at /usr/local/include/boost/iostreams/chain.hpp:348  
#19 0x0000556a77444e02 in boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::reset (  
    this=0x556a785ce430) at /usr/local/include/boost/smart_ptr/shared_ptr.hpp:784  
#20 boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::output, char, std::char_traits<char>, std::allocator<char> > >::reset (this=0x556a785ce418) at /usr/local/include/boost/iostreams/chain.hpp:488  
```  
  
At `boost/iostreams/detail/adapter/non_blocking_adapter.hpp:43` I found the `iostreams::write` keeps return zero (due to the broken sink device), and it makes the loop starting at line 41 running forever.

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 18:46:31 UTC  
> Updated at: 2022-02-01 18:47:13 UTC  
> Url: https://github.com/boostorg/iostreams/issues/140#issuecomment-1027171200  

But that is exactly the purpose of the non_blocking_adapter?  
When a nonblocking sink returns 0 it should mean to try again later, and that's what it does.  
In case if a permanent error, the sink must in fact return -1. I've not found that documented, but the code seems clear and consistent about this...  
So why does the sink not return -1? If it can't know it's broken there is nothing that can be done really, since it's not possible to detect something is wrong.

---

## Comment 2

> Username: grapland0  
> Created at: 2022-03-09 19:29:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/140#issuecomment-1063282039  

According to https://www.boost.org/doc/libs/1_76_0/libs/iostreams/doc/functions/write.html , `iostreams::write` simply returns `t.rdbuf()->sputn(s, n)` in this case. `sputn` can only return zero or greater, which looks like not being able to exit at any write error.  
  
To detect whether the failure is permanent, do you think it should also check the fail/bad bit?

---

## Comment 3

> Username: rdoeffinger  
> Created at: 2022-03-09 20:39:33 UTC  
> Url: https://github.com/boostorg/iostreams/issues/140#issuecomment-1063350143  

It's a bit hard to say without knowing why the sink device would become "broken" and how it is designed to report that.  
It could throw an exception when it breaks.  
Or it could signal it via pubsync() returning -1, which maybe should be called if the write unexpectedly returns 0?  
Though I am not sure if the combination of streambuf and non-blocking is making sense in the first place.

---

## Comment 4

> Username: rdoeffinger  
> Created at: 2022-08-04 17:09:16 UTC  
> Url: https://github.com/boostorg/iostreams/issues/140#issuecomment-1205537068  

Looking at this again, https://www.boost.org/doc/libs/1_76_0/libs/iostreams/doc/guide/exceptions.html suggests that exceptions should be used to report errors.  
However iostream's own file_sink/source report read errors with -1 and write errors not at all.  
Does anyone have any idea/suggestion how this kind of issue could be fixed?  
E.g. full disk currently results in endless loop consuming 100% CPU.
