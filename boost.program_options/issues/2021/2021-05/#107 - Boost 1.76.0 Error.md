# #107 - Boost 1.76.0 Error [Closed]

> Username: shuvom-s  
> Created at: 2021-05-28 16:15:33 UTC  
> Updated at: 2021-07-10 19:02:47 UTC  
> Closed at: 2021-07-10 19:02:47 UTC  
> Url: https://github.com/boostorg/program_options/issues/107  

Hi,  
  
I am using Boost 1.76.0 on a remote machine.  My program options path is   
  
`BOOST_LIB_PO=/broad/cholab/shuvom/boost_1_76_0/lib/libboost_program_options.a`  
  
However, when I compile my code, I get the following error:   
  
`chunker_algorithm.cpp:(.text+0x1758): undefined reference to `boost::program_options::abstract_variables_map::operator[](std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) const'`  
  
Some people suggested online (e.g. https://www.sofa-framework.org/community/forum/topic/undefined-reference-to-boostprogram_optionsabstrac_variables_mapoperator/) that this may be a compiler issue.  However, I am using GCC-5.2 and Boost 1.76.0, which should be(?) compatible.  Where is this error being generated from?

---

## Comment 1

> Username: vprus  
> Created at: 2021-07-10 19:02:47 UTC  
> Url: https://github.com/boostorg/program_options/issues/107#issuecomment-877687808  

Hi,  
  
I suspect this issue is no longer relevant, but if it is, I wrote some suggestions on debugging linker issues at http://blog.vladimirprus.com/2009/06/linking-101.html  
  
I am going to close this issue as likely stale, but feel free to reopen should there be a defect in the library.
