# #23 - One test fails to compile after boostorg/msm submodule msm updated from fc3671 to 2286d1 [Closed]

> Username: spacelg  
> Created at: 2019-10-17 09:39:17 UTC  
> Updated at: 2024-02-13 09:18:03 UTC  
> Closed at: 2024-02-13 09:18:03 UTC  
> Url: https://github.com/boostorg/msm/issues/23  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test fails to compile after boostorg/msm submodule msm updated from fc3671 to 2286d1 [boost/msm@35f9d94](https://github.com/boostorg/boost/commit/35f9d942ca306b1b328cac2a33b481589d165c52). Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\msm\test  
  
[log_x64_test_67.log](https://github.com/boostorg/msm/files/3738493/log_x64_test_67.log)  
  
ErrorMessage:  
libs\msm\test\TestConstructor.cpp(264): error C2440: '<function-style-cast>': cannot convert from 'const boost::msm::msm_terminal<Expr>' to '`anonymous-namespace'::player_::Playing'  
        with  
        [  
            Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<const boost::msm::back::define_states_creation<boost::proto::exprns_::is_proto_expr> &,boost::msm::msm_terminal<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const `anonymous-namespace'::player_::Playing_::Song1 &>,0>>>,2>  
        ]  
libs\msm\test\TestConstructor.cpp(264): note: No constructor could take the source type, or constructor overload resolution was ambiguous

---

## Comment 1

> Username: QuellaZhang  
> Created at: 2019-12-20 09:23:29 UTC  
> Url: https://github.com/boostorg/msm/issues/23#issuecomment-567855251  

Hello, I encountered the following error with same file on the latest Boost version 68cc708, could you help look at this?  
  
[log_x64_test_67.log](https://github.com/boostorg/msm/files/3987586/log_x64_test_67.log)  
  
TestConstructor.cpp  
libs\msm\test\TestConstructor.cpp(264): error C2440: '<function-style-cast>': cannot convert from 'const boost::msm::msm_terminal<Expr>' to '`anonymous-namespace'::player_::Playing'  
        with  
        [  
            Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<const boost::msm::back::define_states_creation<boost::proto::exprns_::is_proto_expr> &,boost::msm::msm_terminal<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const `anonymous-namespace'::player_::Playing_::Song1 &>,0>>>,2>  
        ]  
libs\msm\test\TestConstructor.cpp(264): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
libs\msm\test\TestConstructor.cpp(264): error C2064: term does not evaluate to a function taking 1 arguments

---

## Comment 2

> Username: henry-ch  
> Created at: 2024-02-13 09:17:48 UTC  
> Url: https://github.com/boostorg/msm/issues/23#issuecomment-1940866422  

Fixed. No more runner displays the issue.
