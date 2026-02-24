# #28 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:27:31 UTC  
> Updated at: 2020-02-18 02:59:53 UTC  
> Closed at: 2020-02-18 02:59:53 UTC  
> Url: https://github.com/boostorg/exception/issues/28  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost_1_72_0/boost/exception/all.hpp:#ifndef UUID_316FDA946C0D11DEA9CBAE5255D89593  
./boost_1_72_0/boost/exception/all.hpp:#define UUID_316FDA946C0D11DEA9CBAE5255D89593  
./boost_1_72_0/boost/exception/current_exception_cast.hpp:#ifndef UUID_7E83C166200811DE885E826156D89593  
./boost_1_72_0/boost/exception/current_exception_cast.hpp:#define UUID_7E83C166200811DE885E826156D89593  
./boost_1_72_0/boost/exception/detail/clone_current_exception.hpp:#ifndef UUID_81522C0EB56511DFAB613DB0DFD72085  
./boost_1_72_0/boost/exception/detail/clone_current_exception.hpp:#define UUID_81522C0EB56511DFAB613DB0DFD72085  
./boost_1_72_0/boost/exception/detail/error_info_impl.hpp:#ifndef UUID_CE6983AC753411DDA764247956D89593  
./boost_1_72_0/boost/exception/detail/error_info_impl.hpp:#define UUID_CE6983AC753411DDA764247956D89593  
./boost_1_72_0/boost/exception/detail/exception_ptr.hpp:#ifndef UUID_618474C2DE1511DEB74A388C56D89593  
./boost_1_72_0/boost/exception/detail/exception_ptr.hpp:#define UUID_618474C2DE1511DEB74A388C56D89593  
./boost_1_72_0/boost/exception/detail/is_output_streamable.hpp:#ifndef UUID_898984B4076411DD973EDFA055D89593  
./boost_1_72_0/boost/exception/detail/is_output_streamable.hpp:#define UUID_898984B4076411DD973EDFA055D89593  
./boost_1_72_0/boost/exception/detail/object_hex_dump.hpp:#ifndef UUID_6F463AC838DF11DDA3E6909F56D89593  
./boost_1_72_0/boost/exception/detail/object_hex_dump.hpp:#define UUID_6F463AC838DF11DDA3E6909F56D89593  
./boost_1_72_0/boost/exception/detail/shared_ptr.hpp:#ifndef UUID_837060E885AF11E68DA91D15E31AC075  
./boost_1_72_0/boost/exception/detail/shared_ptr.hpp:#define UUID_837060E885AF11E68DA91D15E31AC075  
./boost_1_72_0/boost/exception/detail/type_info.hpp:#ifndef UUID_C3E1741C754311DDB2834CCA55D89593  
./boost_1_72_0/boost/exception/detail/type_info.hpp:#define UUID_C3E1741C754311DDB2834CCA55D89593  
./boost_1_72_0/boost/exception/diagnostic_information.hpp:#ifndef UUID_0552D49838DD11DD90146B8956D89593  
./boost_1_72_0/boost/exception/diagnostic_information.hpp:#define UUID_0552D49838DD11DD90146B8956D89593  
./boost_1_72_0/boost/exception/enable_current_exception.hpp:#ifndef UUID_851700A4F7CF11E6B2EE06DD14915323  
./boost_1_72_0/boost/exception/enable_current_exception.hpp:#define UUID_851700A4F7CF11E6B2EE06DD14915323  
./boost_1_72_0/boost/exception/enable_error_info.hpp:#ifndef UUID_A0F7404AF7CF11E6908227DD14915323  
./boost_1_72_0/boost/exception/enable_error_info.hpp:#define UUID_A0F7404AF7CF11E6908227DD14915323  
./boost_1_72_0/boost/exception/errinfo_api_function.hpp:#ifndef UUID_DDFBB4546C1211DEA4659E9055D89593  
./boost_1_72_0/boost/exception/errinfo_api_function.hpp:#define UUID_DDFBB4546C1211DEA4659E9055D89593  
./boost_1_72_0/boost/exception/errinfo_at_line.hpp:#ifndef UUID_E7255CE26C1211DE85800C9155D89593  
./boost_1_72_0/boost/exception/errinfo_at_line.hpp:#define UUID_E7255CE26C1211DE85800C9155D89593  
./boost_1_72_0/boost/exception/errinfo_errno.hpp:#ifndef UUID_F0EE17BE6C1211DE87FF459155D89593  
./boost_1_72_0/boost/exception/errinfo_errno.hpp:#define UUID_F0EE17BE6C1211DE87FF459155D89593  
./boost_1_72_0/boost/exception/errinfo_file_handle.hpp:#ifndef UUID_F79E6EE26C1211DEB26E929155D89593  
./boost_1_72_0/boost/exception/errinfo_file_handle.hpp:#define UUID_F79E6EE26C1211DEB26E929155D89593  
./boost_1_72_0/boost/exception/errinfo_file_name.hpp:#ifndef UUID_FEE5120A6C1211DE94E8BC9155D89593  
./boost_1_72_0/boost/exception/errinfo_file_name.hpp:#define UUID_FEE5120A6C1211DE94E8BC9155D89593  
./boost_1_72_0/boost/exception/errinfo_file_open_mode.hpp:#ifndef UUID_056F1F266C1311DE8E74299255D89593  
./boost_1_72_0/boost/exception/errinfo_file_open_mode.hpp:#define UUID_056F1F266C1311DE8E74299255D89593  
./boost_1_72_0/boost/exception/errinfo_nested_exception.hpp:#ifndef UUID_45CC9A82B77511DEB330FC4956D89593  
./boost_1_72_0/boost/exception/errinfo_nested_exception.hpp:#define UUID_45CC9A82B77511DEB330FC4956D89593  
./boost_1_72_0/boost/exception/errinfo_type_info_name.hpp:#ifndef UUID_0E11109E6C1311DEB7EA649255D89593  
./boost_1_72_0/boost/exception/errinfo_type_info_name.hpp:#define UUID_0E11109E6C1311DEB7EA649255D89593  
./boost_1_72_0/boost/exception/error_info.hpp:#ifndef UUID_EE7ECCA0433B11E1923E37064924019B  
./boost_1_72_0/boost/exception/error_info.hpp:#define UUID_EE7ECCA0433B11E1923E37064924019B  
./boost_1_72_0/boost/exception/exception.hpp:#ifndef UUID_274DA366004E11DCB1DDFE2E56D89593  
./boost_1_72_0/boost/exception/exception.hpp:#define UUID_274DA366004E11DCB1DDFE2E56D89593  
./boost_1_72_0/boost/exception/get_error_info.hpp:#ifndef UUID_1A590226753311DD9E4CCF6156D89593  
./boost_1_72_0/boost/exception/get_error_info.hpp:#define UUID_1A590226753311DD9E4CCF6156D89593  
./boost_1_72_0/boost/exception/info.hpp:#ifndef UUID_8D22C4CA9CC811DCAA9133D256D89593  
./boost_1_72_0/boost/exception/info.hpp:#define UUID_8D22C4CA9CC811DCAA9133D256D89593  
./boost_1_72_0/boost/exception/info_tuple.hpp:#ifndef UUID_63EE924290FB11DC87BB856555D89593  
./boost_1_72_0/boost/exception/info_tuple.hpp:#define UUID_63EE924290FB11DC87BB856555D89593  
./boost_1_72_0/boost/exception/to_string.hpp:#ifndef UUID_7E48761AD92811DC9011477D56D89593  
./boost_1_72_0/boost/exception/to_string.hpp:#define UUID_7E48761AD92811DC9011477D56D89593  
./boost_1_72_0/boost/exception/to_string_stub.hpp:#ifndef UUID_E788439ED9F011DCB181F25B55D89593  
./boost_1_72_0/boost/exception/to_string_stub.hpp:#define UUID_E788439ED9F011DCB181F25B55D89593  
./boost_1_72_0/boost/exception_ptr.hpp:#ifndef UUID_FA5836A2CADA11DC8CD47C8555D89593  
./boost_1_72_0/boost/exception_ptr.hpp:#define UUID_FA5836A2CADA11DC8CD47C8555D89593  
./boost_1_72_0/boost/throw_exception.hpp:#ifndef UUID_AA15E74A856F11E08B8D93F24824019B  
./boost_1_72_0/boost/throw_exception.hpp:#define UUID_AA15E74A856F11E08B8D93F24824019B  
./boost_1_72_0/libs/exception/test/helper1.hpp:#ifndef UUID_4AEB7924CA2E11DC888A8C9355D89593  
./boost_1_72_0/libs/exception/test/helper1.hpp:#define UUID_4AEB7924CA2E11DC888A8C9355D89593  
./boost_1_72_0/libs/exception/test/helper2.hpp:#ifndef UUID_BC765EB4CA2A11DCBDC5828355D89593  
./boost_1_72_0/libs/exception/test/helper2.hpp:#define UUID_BC765EB4CA2A11DCBDC5828355D89593  
```
