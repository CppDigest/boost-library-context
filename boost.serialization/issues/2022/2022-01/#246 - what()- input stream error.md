# #246 - what():  input stream error? [Closed]

> Username: nickhuangxinyu  
> Created at: 2022-01-02 16:21:24 UTC  
> Updated at: 2022-01-03 09:30:17 UTC  
> Closed at: 2022-01-03 09:25:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/246  

i want to serialize a singleton class:  
  
```  
// static_player.h  
#pragma once  
#include <map>  
#include <unordered_set>  
#include <vector>  
#include <memory>  
  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <boost/serialization/unordered_map.hpp>  
#include <boost/serialization/unordered_set.hpp>  
#include <boost/serialization/map.hpp>  
  
#include "util/file_util.h"  
#include "struct/struct_util.hpp"  
  
namespace util {  
  namespace trade_util {  
    typedef std::unordered_map<std::string, std::unordered_map<std::string, std::unordered_map<std::string, double>>> NTKD; // map of name_ticker_key_value  
    typedef std::unordered_map<std::string, std::unordered_map<std::string, std::unordered_map<std::string, std::string>>> NTKS; // map of name_ticker_key_value  
      
    typedef std::unordered_map<std::string, std::map<int32_t, std::unordered_map<std::string, std::unordered_map<std::string, double>> > > NDTKD;  
    typedef std::unordered_map<std::string, std::map<int32_t, std::unordered_map<std::string, std::unordered_map<std::string, std::string>> > > NDTKS;  
      
    class StaticPlayer {  
     private:  
      StaticPlayer();  
      StaticPlayer(const StaticPlayer&) = delete;  
      StaticPlayer(const StaticPlayer&&) = delete;  
      virtual ~StaticPlayer();  
   
      class DM {  
       public:  
        DM() {}  
        DM(NTKD * numeric_data, NTKS * string_data) : numeric_data_(numeric_data), string_data_(string_data) {}  
        virtual ~DM() = default;  
        inline double get_numeric(const std::string & name, const std::string& ticker, const std::string& key) const;  
        inline std::string get_string(const std::string & name, const std::string& ticker, const std::string& key) const;  
      private:  
        NTKD* numeric_data_;  
        NTKS* string_data_;  
        friend class boost::serialization::access;  
        template<class Archive>  
        void serialize(Archive & ar, const unsigned int version) {  
          ar & numeric_data_;  
          ar & string_data_;  
        }  
      };  
  
     public:  
      static StaticPlayer& Inst() { static StaticPlayer um; return um; }  
      double query_numeric(const std::string&name, const std::string&ticker, int32_t date, const std::string& key);  
      std::string query_string(const std::string&name, const std::string&ticker, int32_t date, const std::string& key);  
  
      const std::unordered_set<std::string> & get_tickers() const { return tickers_; }  
      const std::unordered_set<std::string> & get_chains() const { return chains_; }  
  
      double ticksize(int32_t date, const std::string& ticker);  // merge two spec  
      double fee(int32_t date, const std::string& ticker);  
      int32_t multiplier(int32_t date, const std::string& ticker);  
  
      inline bool find_date(int date) const { return umap_.find(date) == umap_.end(); }  
  
      void operator=(const StaticPlayer&) = delete;  
  
      DM* operator[](int32_t date);  
  
      std::string Rank2Ticker(int32_t date, const std::string& con, int32_t rank) const;  
      std::string ticker_translate(const std::string& ticker, int32_t date) const;  
      void load_config(const std::set<std::string> & fset);  
      void load_config(bool simple = false);  
  
     private:  
      double special_query(const std::string&ticker, int32_t date, const std::string& key);  
     private:  
      bool loaded_ = false;  
      std::map<int32_t, DM*> umap_;  
      // std::mutex mut_;  
      NDTKD numeric_data_; //  name : date : ticker : key : value  
      NDTKS string_data_; //  name : date : ticker : key : value  
      std::unordered_map<std::string, std::string> fill_na_method_;  // name : fill_na  
      std::map<int32_t, std::unordered_map<std::string, std::unordered_map<int32_t, std::string> >> rank_map_;  
      std::unordered_set<std::string> tickers_, chains_;  
  
      friend class boost::serialization::access;  
      template<class Archive>  
      void serialize(Archive & ar, const unsigned int version) {  
        ar & loaded_;  
        ar & umap_;  
        ar & numeric_data_;  
        ar & string_data_;  
        ar & fill_na_method_;  
        ar & rank_map_;  
        ar & tickers_;  
        ar & chains_;  
        // ar & mut_;  
      }  
    };  
  }  
}  
```  
  
// main.cpp  
```  
#include "./static_player.h"  
void write_univ() {  
  auto & u = util::trade_util::StaticPlayer::Inst();  
  u.load_config();  
  std::ofstream ofs("a");  
  boost::archive::text_oarchive oa(ofs);  
  oa << u;  
    
  std::ifstream ifs("a");  
  boost::archive::text_iarchive ia(ifs);  
  util::trade_util::StaticPlayer& u2 = (util::trade_util::StaticPlayer::Inst());  
  ia >> u2;   
  for (const auto & ticker : u2.get_tickers()) cout << ticker << endl;  
}  
  
int main() {  
  write_univ();  
}  
```  
  
when i run those code, it comes out error:  
```  
terminate called after throwing an instance of 'boost::archive::archive_exception'  
  what():  input stream error  
[1]    20341 abort (core dumped)  ./a.out  
  
```  
  
I have googled all document, but still dont figure out, could you help on this

---

## Comment 1

> Username: correaa  
> Created at: 2022-01-02 19:41:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/246#issuecomment-1003764905  

try adding a scope with brackets {} that separates writing from reading in write_univ function.

---

## Comment 2

> Username: robertramey  
> Created at: 2022-01-03 00:20:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/246#issuecomment-1003802251  

I concur. to this advice.   
  
Even better use  
  
`{`  
`std::ofstream ofs("a");`  
`boost::archive::text_oarchive oa(ofs);`  
` oa << u;`  
` } // use {} to be sure that file is closed`  
`{`  
`std::ifstream ifs("a");`  
`boost::archive::text_iarchive ia(ifs);`  
`util::trade_util::StaticPlayer& u2 = (util::trade_util::StaticPlayer::Inst());`  
`ia >> u2; `  
`}`  
  
To make sure that file is open when archive is closed.  
>

---

## Comment 3

> Username: correaa  
> Created at: 2022-01-03 09:30:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/246#issuecomment-1003966785  

@robertramey that is exactly what I meant. Thanks!
