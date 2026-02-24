# #34 - This code will fail on FreeBSD [Closed]

> Username: Pryancito  
> Created at: 2018-11-17 16:16:01 UTC  
> Updated at: 2025-01-12 16:43:29 UTC  
> Closed at: 2025-01-12 16:43:28 UTC  
> Url: https://github.com/boostorg/locale/issues/34  

std::string Utils::make_string(const std::string &nickname, const std::string& fmt, ...) {  
    generator gen;  
    locale loc;  
    gen.add_messages_path("lang");  
    gen.add_messages_domain("zeus");  
      
    if (nickname != "")  
		loc = gen(NickServ::GetLang(nickname));  
	else if (!config->Getvalue("language").empty())  
		loc = gen(config->Getvalue("language"));  
	else  
		loc = gen("en");  
	  
	std::string msg = translate(fmt).str(loc);  
  
	char buffer[512];  
	va_list args;  
	va_start (args, fmt);  
	vsnprintf (buffer, 512, msg.c_str(), args);  
	va_end (args);  
	return buffer;  
}

---

## Comment 1

> Username: Pryancito  
> Created at: 2018-11-17 17:23:30 UTC  
> Url: https://github.com/boostorg/locale/issues/34#issuecomment-439632926  

Program terminated with signal SIGSEGV, Segmentation fault.  
#0  0x000000080318d9e8 in vtable for __cxxabiv1::__si_class_type_info ()  
   from /lib/libcxxrt.so.1  
(gdb) bt  
#0  0x000000080318d9e8 in vtable for __cxxabiv1::__si_class_type_info ()  
   from /lib/libcxxrt.so.1  
#1  0x0000000000667b71 in __dynamic_cast ()  
#2  0x00000000006c424d in std::locale::facet::_M_cow_shim(std::locale::id const*) const  
    ()  
#3  0x000000000066c289 in std::locale::_Impl::_M_install_facet(std::locale::id const*, std::locale::facet const*) ()  
#4  0x0000000801443097 in std::locale::locale<boost::locale::impl_icu::collate_impl<char> >(std::locale const&, boost::locale::impl_icu::collate_impl<char>*) ()  
   from /usr/home/ircd/zeusircd/boost-compiled/lib/libboost_locale.so.1.68.0  
#5  0x0000000801441b86 in boost::locale::impl_icu::create_collate(std::locale const&, boost::locale::impl_icu::cdata const&, unsigned int) ()  
   from /usr/home/ircd/zeusircd/boost-compiled/lib/libboost_locale.so.1.68.0  
#6  0x00000008014599dd in boost::locale::impl_icu::icu_localization_backend::install(std::locale const&, unsigned int, unsigned int) ()  
   from /usr/home/ircd/zeusircd/boost-compiled/lib/libboost_locale.so.1.68.0  
#7  0x00000008014222c5 in boost::locale::localization_backend_manager::impl::actual_backend::install(std::locale const&, unsigned int, unsigned int) ()  
   from /usr/home/ircd/zeusircd/boost-compiled/lib/libboost_locale.so.1.68.0  
#8  0x000000080141cfdc in boost::locale::generator::generate(std::locale const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) const ()  
   from /usr/home/ircd/zeusircd/boost-compiled/lib/libboost_locale.so.1.68.0  
#9  0x000000080141d719 in boost::locale::generator::generate(std::__cxx11::basic_string<c--Type <RET> for more, q to quit, c to continue without paging--  
har, std::char_traits<char>, std::allocator<char> > const&) const ()  
   from /usr/home/ircd/zeusircd/boost-compiled/lib/libboost_locale.so.1.68.0  
#10 0x000000000065e835 in boost::locale::generator::operator() (this=0x7fffffffcae8,   
    id="en_US.UTF-8") at ../boost-compiled/include/boost/locale/generator.hpp:203  
#11 0x000000000065e1b9 in Utils::make_string (nickname="",   
    fmt="You have started wrong the ircd. For help check: %s -h") at utils.cpp:90  
#12 0x000000000051a2bc in main (argc=1, argv=0x7fffffffeb10) at main.cpp:64

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-01-12 16:43:28 UTC  
> Url: https://github.com/boostorg/locale/issues/34#issuecomment-2585825294  

Closing this old issue. If this is still a problem with the latest version please open a new issue.
