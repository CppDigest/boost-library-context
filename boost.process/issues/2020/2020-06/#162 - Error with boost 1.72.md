# #162 - Error with boost 1.72 [Open]

> Username: kuafuwang  
> Created at: 2020-06-07 01:51:51 UTC  
> Updated at: 2020-06-07 01:51:51 UTC  
> Url: https://github.com/boostorg/process/issues/162  

`	boost::asio::io_context io_context;  
	bp::async_pipe p1(io_context);  
	bp::async_pipe p2(io_context);  
	bp::system(  
		"test.exe",  
		bp::std_out > p2,  
		bp::std_in < p1,  
		io_context,  
		bp::on_exit([&](int exit, const std::error_code& ec_in)  
			{  
				p1.async_close();  
				p2.async_close();  
			})  
	);  
	std::vector<char> in_buf;  
	std::string value = "my_string";  
	boost::asio::async_write(p1, boost::asio::buffer(value), [](const boost::system::error_code&, std::size_t) {});  
	boost::asio::async_read(p2, boost::asio::buffer(in_buf), [](const boost::system::error_code&, std::size_t) {});  
	return 0;`  
  
compiler report :  
1>d:\workspace\sc_app_dev\tool\jc_kit\jcide\jcide_v5_kind\packages\boost.1.72.0.0\lib\native\include\boost\asio\impl\read.hpp(460): error C3646: “executor_type”: Unknown overwrite specifier  
1>d:\workspace\sc_app_dev\tool\jc_kit\jcide\jcide_v5_kind\packages\boost.1.72.0.0\lib\native\include\boost\asio\impl\read.hpp(460): error C4430:Quēshǎo lèixíng shuōmíng fú - jiǎdìng wèi int. Zhùyì: C++ bù zhīchí mòrèn int  
36/5000  
Missing type specifier-assumed to be int. Note: C++ does not support the default int
