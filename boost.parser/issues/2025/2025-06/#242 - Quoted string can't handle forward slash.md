# #242 - Quoted string can't handle forward slash [Closed]

> Username: rafal-c  
> Created at: 2025-06-10 07:13:54 UTC  
> Updated at: 2025-07-27 02:29:59 UTC  
> Closed at: 2025-07-27 02:29:58 UTC  
> Url: https://github.com/boostorg/parser/issues/242  

I'm trying to parse `"GET /images/gif\ HTTP/1.0"`  as a `bp::quoted_string` and it fails to do so. The trace looks as follows:  
  
```  
(... up to this point no issues, we already matched "GET /images/g ...)  
              matched "i"  
            [end '\' >> char_("\"") | !char_("\"") >> char_[<<action>>]; input="f\ HTTP/"]  
            [begin '\' >> char_("\"") | !char_("\"") >> char_[<<action>>]; input="f\ HTTP/"]  
              [begin '\' >> char_("\"") | !char_("\"") >> char_; input="f\ HTTP/"]  
                [begin '\' >> char_("\""); input="f\ HTTP/"]  
                  [begin '\'; input="f\ HTTP/"]  
                    [begin char_('\'); input="f\ HTTP/"]  
                      no match  
                    [end char_('\'); input="f\ HTTP/"]  
                    no match  
                  [end '\'; input="f\ HTTP/"]  
                  no match  
                [end '\' >> char_("\""); input="f\ HTTP/"]  
                [begin !char_("\"") >> char_; input="f\ HTTP/"]  
                  [begin !char_("\""); input="f\ HTTP/"]  
                    [begin char_("\""); input="f\ HTTP/"]  
                      no match  
                    [end char_("\""); input="f\ HTTP/"]  
                    matched ""  
                    attribute: <<unprintable-value>>  
                  [end !char_("\""); input="f\ HTTP/"]  
                  [begin char_; input="f\ HTTP/"]  
                    matched "f"  
                    attribute: <<unprintable-value>>  
                  [end char_; input="\ HTTP/1"]  
                  matched "f"  
                  attribute: <<unprintable-value>>  
                [end !char_("\"") >> char_; input="\ HTTP/1"]  
                matched "f"  
                attribute: <<unprintable-value>>  
              [end '\' >> char_("\"") | !char_("\"") >> char_; input="\ HTTP/1"]  
              matched "f"  
            [end '\' >> char_("\"") | !char_("\"") >> char_[<<action>>]; input="\ HTTP/1"]  
            [begin '\' >> char_("\"") | !char_("\"") >> char_[<<action>>]; input="\ HTTP/1"]  
              [begin '\' >> char_("\"") | !char_("\"") >> char_; input="\ HTTP/1"]  
                [begin '\' >> char_("\""); input="\ HTTP/1"]  
                  [begin '\'; input="\ HTTP/1"]  
                    [begin char_('\'); input="\ HTTP/1"]  
                      matched "\"  
                      attribute: <<unprintable-value>>  
                    [end char_('\'); input=" HTTP/1."]  
                    matched "\"  
                    attribute: <<unprintable-value>>  
                  [end '\'; input=" HTTP/1."]  
                  [begin char_("\""); input=" HTTP/1."]  
                    no match  
                  [end char_("\""); input=" HTTP/1."]  
                  no match  
                [end '\' >> char_("\""); input="\ HTTP/1"]  
                [begin !char_("\"") >> char_; input="\ HTTP/1"]  
                  [begin !char_("\""); input="\ HTTP/1"]  
                    [begin char_("\""); input="\ HTTP/1"]  
                      matched "\"  
                      attribute: <<unprintable-value>>  
                    [end char_("\""); input=" HTTP/1."]  
                    no match  
                  [end !char_("\""); input="\ HTTP/1"]  
                  no match  
                [end !char_("\"") >> char_; input="\ HTTP/1"]  
                no match  
              [end '\' >> char_("\"") | !char_("\"") >> char_; input="\ HTTP/1"]  
              no match  
            [end '\' >> char_("\"") | !char_("\"") >> char_[<<action>>]; input="\ HTTP/1"]  
            matched "GET /ima"  
          [end *'\' >> char_("\"") | !char_("\"") >> char_[<<action>>]; input="\ HTTP/1"]  
          [begin '"'; input="\ HTTP/1"]  
            [begin char_('"'); input="\ HTTP/1"]  
              no match  
            [end char_('"'); input="\ HTTP/1"]  
            no match  
          [end '"'; input="\ HTTP/1"]  
          no match  
        [end *'\' >> char_("\"") | !char_("\"") >> char_[<<action>>] > '"'; input="GET /ima"]  
        no match  
      [end quoted_string('"'); input="GET /ima"]  
      no match

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-27 02:29:58 UTC  
> Url: https://github.com/boostorg/parser/issues/242#issuecomment-3123783902  

That's a weird title, given that the parser already successfully ate multiple forward slashes. :)  
  
The problem is the backslash, not the forward slashes.  The backslash needs to be doubled ("\\\\") if you want a single backslash to appear in the resulting string attribute.  You cannot escape a space, and yet you have done so in the string `"GET /images/gif\ HTTP/1.0"`.  Change it to `"GET /images/gif\\ HTTP/1.0"`.  If you don't want the backslash in the attribute, just drop the backslash altogether.
