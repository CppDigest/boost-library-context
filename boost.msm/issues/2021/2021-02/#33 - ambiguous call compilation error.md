# #33 - ambiguous call compilation error [Closed]

> Username: AndrewBloom  
> Created at: 2021-02-24 18:36:34 UTC  
> Updated at: 2024-02-13 09:18:50 UTC  
> Closed at: 2024-02-13 09:18:50 UTC  
> Url: https://github.com/boostorg/msm/issues/33  

https://stackoverflow.com/questions/66354497/boost-msm-with-constructor-parameters-on-a-submachine?noredirect=1#comment117311440_66354497  
  
I don't understand anything about templates, but this line:  
  
mainSM() : msm::back::state_machine<mainSMFE>(msm::back::states_ << SubSM(sb)) { };  
  
raises this error that seems to me a bug: (the two functions overlap in this case)  
  
C:\Users\Andrea\source\repos\PlayerBoostTest\PlayerBoostTest\PlayerBoostTest.cpp(59): error C2668: 'boost::msm::back::state_machine<mainSMFE,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_>::state_machine': ambiguous call to overloaded function  
  C:\Users\Andrea\boost_1_75_0\boost_1_75_0\boost/msm/back/state_machine.hpp(1706): note: could be 'boost::msm::back::state_machine<mainSMFE,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_>::state_machine<boost::msm::msm_terminal<Expr>,,void>(const boost::msm::msm_terminal<Expr> &)'  
          with  
          [  
              Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<const boost::msm::back::define_states_creation<boost::proto::exprns_::is_proto_expr> &,boost::msm::msm_terminal<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const SubSM &>,0>>>,2>  
          ]  
  C:\Users\Andrea\boost_1_75_0\boost_1_75_0\boost/msm/back/state_machine.hpp(1611): note: or       'boost::msm::back::state_machine<mainSMFE,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_>::state_machine<boost::msm::msm_terminal<Expr>>(const boost::msm::msm_terminal<Expr> &,void *)'  
          with  
          [  
              Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<const boost::msm::back::define_states_creation<boost::proto::exprns_::is_proto_expr> &,boost::msm::msm_terminal<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const SubSM &>,0>>>,2>  
          ]  
  ..\..\..\PlayerBoostTest\PlayerBoostTest.cpp(59): note: while trying to match the argument list '(const boost::msm::msm_terminal<Expr>)'  
          with  
          [  
              Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<const boost::msm::back::define_states_creation<boost::proto::exprns_::is_proto_expr> &,boost::msm::msm_terminal<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const SubSM &>,0>>>,2>  
          ]  
  ninja: build stopped: subcommand failed.  
    
  Compiled with Visual Studio 2019

---

## Comment 1

> Username: killerbot242  
> Created at: 2023-03-22 16:25:34 UTC  
> Url: https://github.com/boostorg/msm/issues/33#issuecomment-1479887940  

has there any solution or workaround been found ?  
  
we have something similar with clang16 (all previous clang versions were ok), and on boost 1.81

---

## Comment 2

> Username: henry-ch  
> Created at: 2024-02-13 09:18:50 UTC  
> Url: https://github.com/boostorg/msm/issues/33#issuecomment-1940869338  

Duplicate. Fixed, no more runner displays the issue.
