# #168 - Const-qualify read and comparison methods of boost::date_time::time_itr + boost::date_time::date_itr_base [Closed]

> Username: trueqbit  
> Created at: 2020-08-24 21:18:44 UTC  
> Updated at: 2021-05-03 17:17:53 UTC  
> Closed at: 2021-05-03 17:17:53 UTC  
> Url: https://github.com/boostorg/date_time/issues/168  

I was wondering why the read and comparison methods of `boost::date_time::time_itr` + `boost::date_time::date_itr_base` (files boost/date_time/time_iterator.hpp and boost/date_time/date_iterator.hpp) aren't const-qualified.  
  
Specifically:  
  
    template<class time_type>  
    class time_itr {  
    public:  
      time_type operator*() {return current_;}  
      time_type* operator->() {return &current_;}  
      bool operator<  (const time_type& t) {return current_ < t;}  
      bool operator<= (const time_type& t) {return current_ <= t;}  
      bool operator!=  (const time_type& t) {return current_ != t;}  
      bool operator== (const time_type& t) {return current_ == t;}  
      bool operator>  (const time_type& t) {return current_ > t;}  
      bool operator>= (const time_type& t) {return current_ >= t;}  
    private:  
      time_type current_;  
    };  
  
    template<class date_type>  
    class date_itr_base {  
    public:  
      date_type operator*() {return current_;}  
      date_type* operator->() {return &current_;}  
      bool operator<  (const date_type& d) {return current_ < d;}  
      bool operator<= (const date_type& d) {return current_ <= d;}  
      bool operator>  (const date_type& d) {return current_ > d;}  
      bool operator>= (const date_type& d) {return current_ >= d;}  
      bool operator== (const date_type& d) {return current_ == d;}  
      bool operator!= (const date_type& d) {return current_ != d;}  
    private:  
      date_type current_;  
    };  
  
  
Is there any reason not to const-qualify them? They don't modify their state,  and I presume they shouldn't.  
It would be useful as I could then make my/our code more const-correct.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-08-24 22:14:43 UTC  
> Url: https://github.com/boostorg/date_time/issues/168#issuecomment-679393727  

yes, that looks like a bug to me. I suspect this is a little used feature of the library which would explain why this went unnoticed for 15+ years.

---

## Comment 2

> Username: trueqbit  
> Created at: 2020-08-27 20:30:29 UTC  
> Updated at: 2020-08-27 20:39:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/168#issuecomment-682175075  

Well, I discovered an interesting application in regard to iteration over time and date. Now that I see the library in active develop and fix mode, I thought I was addressing this issue.
