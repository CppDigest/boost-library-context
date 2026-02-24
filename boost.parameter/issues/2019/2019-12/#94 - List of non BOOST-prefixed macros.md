# #94 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:01:55 UTC  
> Updated at: 2019-12-18 13:46:14 UTC  
> Closed at: 2019-12-18 13:46:14 UTC  
> Url: https://github.com/boostorg/parameter/issues/94  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./libs/parameter/test/basics.hpp:#ifndef BASICS_050424_HPP  
./libs/parameter/test/basics.hpp:#define BASICS_050424_HPP  
./boost/parameter/keyword.hpp:#ifndef KEYWORD_050328_HPP  
./boost/parameter/keyword.hpp:#define KEYWORD_050328_HPP  
./boost/parameter/aux_/unwrap_cv_reference.hpp:#ifndef UNWRAP_CV_REFERENCE_050328_HPP  
./boost/parameter/aux_/unwrap_cv_reference.hpp:#define UNWRAP_CV_REFERENCE_050328_HPP  
./boost/parameter/aux_/parameter_requirements.hpp:#ifndef PARAMETER_REQUIREMENTS_050331_HPP  
./boost/parameter/aux_/parameter_requirements.hpp:#define PARAMETER_REQUIREMENTS_050331_HPP  
./boost/parameter/aux_/arg_list.hpp:#ifndef ARG_LIST_050329_HPP  
./boost/parameter/aux_/arg_list.hpp:#define ARG_LIST_050329_HPP  
./boost/parameter/aux_/default.hpp:#ifndef DEFAULT_050329_HPP  
./boost/parameter/aux_/default.hpp:#define DEFAULT_050329_HPP  
./boost/parameter/aux_/yesno.hpp:#ifndef YESNO_050328_HPP  
./boost/parameter/aux_/yesno.hpp:#define YESNO_050328_HPP  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-18 13:46:14 UTC  
> Url: https://github.com/boostorg/parameter/issues/94#issuecomment-567037710  

Resolved in 0f548424a5f966fadfa7a21a759c835729cbc009.
