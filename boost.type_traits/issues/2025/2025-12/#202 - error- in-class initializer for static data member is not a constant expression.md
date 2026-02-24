# #202 - error: in-class initializer for static data member is not a constant expression [clang-diagnostic-error] [Open]

> Username: marek22k  
> Created at: 2025-12-08 14:04:58 UTC  
> Updated at: 2025-12-08 14:11:21 UTC  
> Url: https://github.com/boostorg/type_traits/issues/202  

Code:  
```cpp  
// NOLINTBEGIN(cppcoreguidelines-pro-type-member-init)  
MPingSender::Configuration::Configuration(const std::span<char *> args)  
{  
    boost::program_options::options_description options{"Configuration"};  
    // clang-format off  
    options.add_options()  
      ("help", "Help screen")  
      ("log-level", boost::program_options::value<boost::log::trivial::severity_level>(&this->_log_level)->default_value(boost::log::trivial::info), "Log level. Valid options are trace, debug, info, warning, error and fatal. Defaults to info.")  
      ("bind-address", boost::program_options::value<std::string>(&this->_bind_address)->required(), "Address to which the UDP socket is bound. Required.")  
      ("bind-port", boost::program_options::value<uint16_t>(&this->_bind_port)->default_value(4321), "Port to which the UDP socket is bound. Defaults to 4321.")  
      ("address", boost::program_options::value<std::string>(&this->_address)->default_value("ff2e::42"), "Address to send packets to. Defaults to ff2e::42")  
      ("port", boost::program_options::value<uint16_t>(&this->_port)->default_value(4321), "Address to which the packages are sent.")  
      ("ttl", boost::program_options::value<uint8_t>(&this->_ttl)->default_value(64), "TTTL or hop limit with which the packets are sent. Defaults to 64.")  
      ("interface-name", boost::program_options::value<std::string>(&this->_interface_name)->required(), "Name of the interface from which the packets are sent. Required.")  
    ;  
    // clang-format on  
  
    boost::program_options::command_line_parser parser{  
        static_cast<int>(args.size()), args.data()};  
    parser.options(options).allow_unregistered().style(  
        boost::program_options::command_line_style::allow_long |  
        boost::program_options::command_line_style::allow_sticky |  
        boost::program_options::command_line_style::long_allow_next);  
    const boost::program_options::parsed_options parsed_options = parser.run();  
  
    boost::program_options::variables_map vm;  
    store(parsed_options, vm);  
  
    this->_help = vm.contains("help");  
    if (this->_help)  
    {  
        std::clog << "mping-sender" << std::endl  
                  << std::endl  
                  << "Usage: mping-sender [configuration]" << std::endl  
                  << std::endl  
                  << options << std::endl;  
        std::exit(EXIT_SUCCESS); // NOLINT(concurrency-mt-unsafe)  
    }  
  
    notify(vm);  
  
    boost::log::core::get()->set_filter(boost::log::trivial::severity >=  
                                        this->_log_level);  
}  
  
// NOLINTEND(cppcoreguidelines-pro-type-member-init)  
```  
```cpp  
    class Configuration  
    {  
        public:  
            explicit Configuration(const std::span<char *> args);  
  
            [[nodiscard]] bool get_help() const noexcept;  
            [[nodiscard]] boost::log::trivial::severity_level  
                get_log_level() const noexcept;  
            [[nodiscard]] std::string get_bind_address() const noexcept;  
            [[nodiscard]] uint16_t get_bind_port() const noexcept;  
            [[nodiscard]] std::string get_address() const noexcept;  
            [[nodiscard]] uint16_t get_port() const noexcept;  
            [[nodiscard]] uint8_t get_ttl() const noexcept;  
            [[nodiscard]] std::string get_interface_name() const noexcept;  
  
            void apply_log_level() const;  
  
        private:  
            bool _help;  
            boost::log::trivial::severity_level _log_level;  
            std::string _bind_address;  
            uint16_t _bind_port;  
            std::string _address;  
            uint16_t _port;  
            uint8_t _ttl;  
            std::string _interface_name;  
    };  
```  
generates a clang-diagnostic-error:  
```  
>>> /usr/bin/clang-tidy --use-color -quiet -p /home/marek/git/mping-sender/build /home/marek/git/mping-sender/src/configuration.cpp  
190715 warnings and 2 errors generated.  
Error while processing /home/marek/git/mping-sender/src/configuration.cpp.  
381430 warnings and 4 errors generated.  
Error while processing /home/marek/git/mping-sender/src/configuration.cpp.  
/usr/include/boost/type_traits/is_signed.hpp:37:25: error: in-class initializer for static data member is not a constant expression [clang-diagnostic-error]  
   37 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
      |                         ^           ~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/type_traits/is_signed.hpp:45:60: note: in instantiation of template class 'boost::detail::is_signed_values<boost::log::trivial::severity_level>' requested here  
   45 |    BOOST_STATIC_CONSTANT(bool, value = (!(::boost::detail::is_signed_values<T>::minus_one  > boost::detail::is_signed_values<T>::zero)));  
      |                                                            ^  
/usr/include/boost/config/detail/suffix.hpp:410:72: note: expanded from macro 'BOOST_STATIC_CONSTANT'  
  410 | #     define BOOST_STATIC_CONSTANT(type, assignment) static const type assignment  
      |                                                                        ^  
/usr/include/boost/type_traits/is_signed.hpp:74:40: note: in instantiation of template class 'boost::detail::is_signed_helper<boost::log::trivial::severity_level>' requested here  
   74 |    BOOST_STATIC_CONSTANT(bool, value = type::value);  
      |                                        ^  
/usr/include/boost/config/detail/suffix.hpp:410:72: note: expanded from macro 'BOOST_STATIC_CONSTANT'  
  410 | #     define BOOST_STATIC_CONSTANT(type, assignment) static const type assignment  
      |                                                                        ^  
/usr/include/boost/type_traits/is_signed.hpp:79:85: note: in instantiation of template class 'boost::detail::is_signed_impl<boost::log::trivial::severity_level>' requested here  
   79 | template <class T> struct is_signed : public integral_constant<bool, boost::detail::is_signed_impl<T>::value> {};  
      |                                                                                     ^  
/usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:315:44: note: in instantiation of template class 'boost::is_signed<boost::log::trivial::severity_level>' requested here  
  315 |         typename boost::enable_if_c<boost::is_signed<Type>::value && !boost::is_enum<Type>::value, bool>::type  
      |                                            ^  
/usr/include/boost/lexical_cast/detail/converter_lexical.hpp:449:67: note: while substituting deduced template arguments into function template 'stream_in' [with Type = boost::log::trivial::severity_level]  
  449 |                 -> decltype(std::declval<optimized_src_stream&>().stream_in(std::declval<lcast::exact<T>>()), optimized_src_stream{});  
      |                                                                   ^  
/usr/include/boost/lexical_cast/detail/converter_lexical.hpp:454:46: note: while substituting explicitly-specified template arguments into function template 'detect_type'   
  454 |             using from_src_stream = decltype(detect_type<Source>(1));  
      |                                              ^  
/usr/include/boost/lexical_cast/try_lexical_convert.hpp:67:20: note: in instantiation of template class 'boost::detail::lexical_converter_impl<std::basic_string<char>, boost::log::trivial::severity_level>' requested here  
   67 |             return caster_type::try_convert(arg, result);  
      |                    ^  
/usr/include/boost/lexical_cast.hpp:42:41: note: in instantiation of function template specialization 'boost::conversion::detail::try_lexical_convert<std::basic_string<char>, boost::log::trivial::severity_level>' requested here  
   42 |         if (!boost::conversion::detail::try_lexical_convert(arg, result)) {  
      |                                         ^  
/usr/include/boost/program_options/value_semantic.hpp:202:46: note: in instantiation of function template specialization 'boost::lexical_cast<std::basic_string<char>, boost::log::trivial::severity_level>' requested here  
  202 |             m_default_value_as_text = boost::lexical_cast<std::string>(v);  
      |                                              ^  
../src/configuration.cpp:16:108: note: in instantiation of member function 'boost::program_options::typed_value<boost::log::trivial::severity_level>::default_value' requested here  
   16 |       ("log-level", boost::program_options::value<boost::log::trivial::severity_level>(&this->_log_level)->default_value(boost::log::trivial::info), "Log level. Valid options are trace, debug, info, warning, error and fatal. Defaults to info.")  
      |                                                                                                            ^  
/usr/include/boost/type_traits/is_signed.hpp:37:38: note: integer value -1 is outside the valid range of values [0, 7] for the enumeration type 'severity_level'  
   37 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
      |                                      ^  
/usr/include/boost/type_traits/is_unsigned.hpp:38:25: error: in-class initializer for static data member is not a constant expression [clang-diagnostic-error]  
   38 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
      |                         ^           ~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/type_traits/is_unsigned.hpp:45:58: note: in instantiation of template class 'boost::detail::is_unsigned_values<boost::log::trivial::severity_level>' requested here  
   45 |    BOOST_STATIC_CONSTANT(bool, value = (::boost::detail::is_unsigned_values<T>::minus_one > ::boost::detail::is_unsigned_values<T>::zero));  
      |                                                          ^  
/usr/include/boost/config/detail/suffix.hpp:410:72: note: expanded from macro 'BOOST_STATIC_CONSTANT'  
  410 | #     define BOOST_STATIC_CONSTANT(type, assignment) static const type assignment  
      |                                                                        ^  
/usr/include/boost/type_traits/is_unsigned.hpp:74:40: note: in instantiation of template class 'boost::detail::is_ununsigned_helper<boost::log::trivial::severity_level>' requested here  
   74 |    BOOST_STATIC_CONSTANT(bool, value = type::value);  
      |                                        ^  
/usr/include/boost/config/detail/suffix.hpp:410:72: note: expanded from macro 'BOOST_STATIC_CONSTANT'  
  410 | #     define BOOST_STATIC_CONSTANT(type, assignment) static const type assignment  
      |                                                                        ^  
/usr/include/boost/type_traits/is_unsigned.hpp:79:87: note: in instantiation of template class 'boost::detail::is_unsigned<boost::log::trivial::severity_level>' requested here  
   79 | template <class T> struct is_unsigned : public integral_constant<bool, boost::detail::is_unsigned<T>::value> {};  
      |                                                                                       ^  
/usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:319:44: note: in instantiation of template class 'boost::is_unsigned<boost::log::trivial::severity_level>' requested here  
  319 |         typename boost::enable_if_c<boost::is_unsigned<Type>::value && !boost::is_enum<Type>::value, bool>::type  
      |                                            ^  
/usr/include/boost/lexical_cast/detail/converter_lexical.hpp:449:67: note: while substituting deduced template arguments into function template 'stream_in' [with Type = boost::log::trivial::severity_level]  
  449 |                 -> decltype(std::declval<optimized_src_stream&>().stream_in(std::declval<lcast::exact<T>>()), optimized_src_stream{});  
      |                                                                   ^  
/usr/include/boost/lexical_cast/detail/converter_lexical.hpp:454:46: note: while substituting explicitly-specified template arguments into function template 'detect_type'   
  454 |             using from_src_stream = decltype(detect_type<Source>(1));  
      |                                              ^  
/usr/include/boost/lexical_cast/try_lexical_convert.hpp:67:20: note: in instantiation of template class 'boost::detail::lexical_converter_impl<std::basic_string<char>, boost::log::trivial::severity_level>' requested here  
   67 |             return caster_type::try_convert(arg, result);  
      |                    ^  
/usr/include/boost/lexical_cast.hpp:42:41: note: in instantiation of function template specialization 'boost::conversion::detail::try_lexical_convert<std::basic_string<char>, boost::log::trivial::severity_level>' requested here  
   42 |         if (!boost::conversion::detail::try_lexical_convert(arg, result)) {  
      |                                         ^  
/usr/include/boost/program_options/value_semantic.hpp:202:46: note: in instantiation of function template specialization 'boost::lexical_cast<std::basic_string<char>, boost::log::trivial::severity_level>' requested here  
  202 |             m_default_value_as_text = boost::lexical_cast<std::string>(v);  
      |                                              ^  
../src/configuration.cpp:16:108: note: in instantiation of member function 'boost::program_options::typed_value<boost::log::trivial::severity_level>::default_value' requested here  
   16 |       ("log-level", boost::program_options::value<boost::log::trivial::severity_level>(&this->_log_level)->default_value(boost::log::trivial::info), "Log level. Valid options are trace, debug, info, warning, error and fatal. Defaults to info.")  
      |                                                                                                            ^  
/usr/include/boost/type_traits/is_unsigned.hpp:38:38: note: integer value -1 is outside the valid range of values [0, 7] for the enumeration type 'severity_level'  
   38 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
      |          
```  
Boost issue: https://github.com/boostorg/boost/issues/1107  
clang issue: https://github.com/llvm/llvm-project/issues/163517

---

## Comment 1

> Username: marek22k  
> Created at: 2025-12-08 14:11:21 UTC  
> Url: https://github.com/boostorg/type_traits/issues/202#issuecomment-3627126929  

@jzmaddock
