# #163 - Problem with latest VS2017 update [Closed]

> Username: Kojoley  
> Created at: 2017-12-13 12:46:28 UTC  
> Updated at: 2017-12-13 22:45:28 UTC  
> Closed at: 2017-12-13 22:45:28 UTC  
> Url: https://github.com/boostorg/fusion/issues/163  

VS2017 version: 15.5.1  
cl version: 19.12.25830.2  
  
Fusion tests result in:  
```  
sequence\as_map.cpp(53): error C2668: 'boost::fusion::map<T,boost::fusion::pair<double,std::string>>::map': ambiguous call to overloaded function  
        with  
        [  
            T=boost::fusion::pair<int,char>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(99): note: could be 'boost::fusion::map<T,boost::fusion::pair<double,std::string>>::map<boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>>,>(boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>> &&)'  
        with  
        [  
            T=boost::fusion::pair<int,char>,  
            First=double  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(92): note: or       'boost::fusion::map<T,boost::fusion::pair<double,std::string>>::map<boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>>,>(const boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>> &)'  
        with  
        [  
            T=boost::fusion::pair<int,char>,  
            First=double  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(86): note: or       'boost::fusion::map<T,boost::fusion::pair<double,std::string>>::map<boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>>>(Sequence &&,boost::fusion::detail::enabler_)'  
        with  
        [  
            T=boost::fusion::pair<int,char>,  
            First=double,  
            Sequence=boost::fusion::vector<boost::fusion::pair<int,char>,boost::fusion::pair<double,const char (&)[4]>>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(79): note: or       'boost::fusion::map<T,boost::fusion::pair<double,std::string>>::map<boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>>>(Sequence &,boost::fusion::detail::enabler_)'  
        with  
        [  
            T=boost::fusion::pair<int,char>,  
            First=double,  
            Sequence=boost::fusion::vector<boost::fusion::pair<int,char>,boost::fusion::pair<double,const char (&)[4]>>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(72): note: or       'boost::fusion::map<T,boost::fusion::pair<double,std::string>>::map<boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>>>(const Sequence &,boost::fusion::detail::enabler_)'  
        with  
        [  
            T=boost::fusion::pair<int,char>,  
            First=double,  
            Sequence=boost::fusion::vector<boost::fusion::pair<int,char>,boost::fusion::pair<double,const char (&)[4]>>  
        ]  
sequence\as_map.cpp(53): note: while trying to match the argument list '(boost::fusion::vector<T,boost::fusion::pair<First,const char (&)[4]>>)'  
        with  
        [  
            T=boost::fusion::pair<int,char>,  
            First=double  
        ]  
```  
  
```  
sequence\as_map_assoc.cpp(64): error C2668: 'boost::fusion::map<T,boost::fusion::pair<char,char>>::map': ambiguous call to overloaded function  
        with  
        [  
            T=boost::fusion::pair<int,int>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(99): note: could be 'boost::fusion::map<T,boost::fusion::pair<char,char>>::map<boost::fusion::set<int,char>,>(First &&)'  
        with  
        [  
            T=boost::fusion::pair<int,int>,  
            First=boost::fusion::set<int,char>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(92): note: or       'boost::fusion::map<T,boost::fusion::pair<char,char>>::map<boost::fusion::set<int,char>,>(const First &)'  
        with  
        [  
            T=boost::fusion::pair<int,int>,  
            First=boost::fusion::set<int,char>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(86): note: or       'boost::fusion::map<T,boost::fusion::pair<char,char>>::map<boost::fusion::set<int,char>>(Sequence &&,boost::fusion::detail::enabler_)'  
        with  
        [  
            T=boost::fusion::pair<int,int>,  
            Sequence=boost::fusion::set<int,char>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(79): note: or       'boost::fusion::map<T,boost::fusion::pair<char,char>>::map<boost::fusion::set<int,char>>(Sequence &,boost::fusion::detail::enabler_)'  
        with  
        [  
            T=boost::fusion::pair<int,int>,  
            Sequence=boost::fusion::set<int,char>  
        ]  
..\..\..\boost/fusion/container/map/map.hpp(72): note: or       'boost::fusion::map<T,boost::fusion::pair<char,char>>::map<boost::fusion::set<int,char>>(const Sequence &,boost::fusion::detail::enabler_)'  
        with  
        [  
            T=boost::fusion::pair<int,int>,  
            Sequence=boost::fusion::set<int,char>  
        ]  
sequence\as_map_assoc.cpp(64): note: while trying to match the argument list '(boost::fusion::set<int,char>)'  
```  
```  
sequence\map_copy.cpp(45): error C2440: 'return': cannot convert from 'boost::fusion::map<boost::fusion::pair<k1,T>,boost::fusion::pair<k2,T>,boost::fusion::pair<k3,T>,boost::fusion::pair<k4,T>>' to 'boost::fusion::map<boost::fusion::pair<k1,double>,boost::fusion::pair<k2,double>,boost::fusion::pair<k3,double>,boost::fusion::pair<k4,double>>'  
        with  
        [  
            T=int  
        ]  
sequence\map_copy.cpp(45): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2017-12-13 22:04:20 UTC  
> Url: https://github.com/boostorg/fusion/issues/163#issuecomment-351539986  

Appveyor have updated the VS2017 to 15.5.1 https://www.appveyor.com/updates/2017/12/12/ so if you trigger a new build you will get these errors.
