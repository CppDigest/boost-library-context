# #103 Fix clang -Wdeprecated-copy warning in basic_regex_creator.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-02-28 02:08:55 UTC  
> Updated at: 2020-10-12 17:24:32 UTC  
> Merged at: 2020-10-12 17:24:32 UTC  
> Closed at: 2020-10-12 17:24:32 UTC  
> Url: https://github.com/boostorg/regex/pull/103  

/data/mwrep/res/osp/Boost/19-0-0-18/include/boost/regex/v4/basic_regex_creator.hpp:52:4: error: definition of implicit copy assignment operator for 'digraph<char>' is deprecated because it has a user-declared copy constructor [-Werror,-Wdeprecated-copy]  
   digraph(const digraph<charT>& d) : std::pair<charT, charT>(d.first, d.second){}  
   ^  
/data/mwrep/res/osp/Boost/19-0-0-18/include/boost/regex/v4/basic_regex_parser.hpp:1660:17: note: in implicit copy assignment operator for 'boost::re_detail_107000::digraph<char>' first required here  
         result = *m_position++;

---
