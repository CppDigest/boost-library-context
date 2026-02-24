# #140 - Empty strings are passed to report_event func [Open]

> Username: KostikovIlya  
> Created at: 2021-01-27 21:45:50 UTC  
> Updated at: 2021-01-27 21:45:50 UTC  
> Url: https://github.com/boostorg/log/issues/140  

I've built an example of the event_log ("example/event_log").  
But it doesn't work.   
Instead of   
`The drive C: has low free disk space. At least 2097152 Mb of free space is recommended`  
 I've got   
`The drive  has low free disk space. At least  Mb of free space is recommended.`.  
  
There is an array of empty strings in the report_event call: `  
WORD wNumStrings = 2; const char** lpStrings = { "", ""}.   
`  
Maybe, it is related to:  
```cpp  
 void operator() (record_view const& rec, insertion_list& insertions) const  
  {  
      std::size_t size = m_Formatters.size();  
      insertions.resize(size);  
      for (std::size_t i = 0; i < size; ++i)  
      {  
          typename formatter_type::stream_type strm(insertions[i]);  
          m_Formatters[i](rec, strm);  
          strm.flush();  
      }  
  }  
```  
After calling `strm.flush();` `insertions[i]` is empty,
