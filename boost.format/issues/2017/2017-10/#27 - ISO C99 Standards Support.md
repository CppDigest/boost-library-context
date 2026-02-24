# #27 - ISO C99 Standards Support [Closed]

> Username: jeking3  
> Created at: 2017-10-16 19:28:23 UTC  
> Updated at: 2017-12-18 15:35:05 UTC  
> Closed at: 2017-10-18 01:07:24 UTC  
> Url: https://github.com/boostorg/format/issues/27  

I am working on identifying where boost::format and sprintf differ in behavior.  I wrote a matrix test comparing standard sprintf to boost::format for purposes of ensuring that boost::format handles all of the format strings that sprintf does, and we're currently at about 41% pass rate.  This matrix doesn't test every possible combination of format modifiers yet - just one of each was enough.  Results are very early, and I will be working on improving this.  
  
Here's some sample output from the test:  
```  
       Format String | RSLT | "printf"                              | "boost::format"                       | error info  
---------------------+------+---------------------------------------+---------------------------------------+--------------------------------------------------  
              % 1.5c | FAIL | "X"                                   | " "                                   | mismatch  
             % 1.20c | FAIL | "X"                                   | " "                                   | mismatch  
                % 2c | FAIL | " X"                                  | "  "                                  | mismatch  
               % 2.c | FAIL | " X"                                  | "  "                                  | mismatch  
...  
                 %5u |      | "4294954950"                          | "4294954950"                          |  
                %5.u |      | "4294954950"                          | "4294954950"                          |  
               %5.2u |      | "4294954950"                          | "4294954950"                          |  
               %5.5u |      | "4294954950"                          | "4294954950"                          |  
              %5.20u | FAIL | "00000000004294954950"                | "4294954950"                          | mismatch  
...  
              %020Lf |      | "6543211234567.891602"                | "6543211234567.891602"                |  
             %020.Lf |      | "00000006543211234568"                | "00000006543211234568"                |  
            %020.2Lf |      | "00006543211234567.89"                | "00006543211234567.89"                |  
            %020.5Lf |      | "06543211234567.89160"                | "06543211234567.89160"                |  
           %020.20Lf |      | "6543211234567.89160156250000000000"  | "6543211234567.89160156250000000000"  |  
                  %F | EXCP | "1234567.875000"                      | ""                                    | boost::bad_format_string: format-string is ill-formed  
...  
              %020LG |      | "0000000006.54321E+12"                | "0000000006.54321E+12"                |  
             %020.LG | FAIL | "0000000000000007E+12"                | "0000000006.54321E+12"                | mismatch  
            %020.2LG |      | "00000000000006.5E+12"                | "00000000000006.5E+12"                |  
            %020.5LG |      | "00000000006.5432E+12"                | "00000000006.5432E+12"                |  
           %020.20LG |      | "6543211234567.8916016"               | "6543211234567.8916016"               |  
  
```  
Failed Tests: 6707  
 Total Tests: 11401  
   Pass Rate: 41.2%

---

## Comment 1

> Username: jeking3  
> Created at: 2017-10-18 01:07:24 UTC  
> Url: https://github.com/boostorg/format/issues/27#issuecomment-337429591  

Read through the docs; sounds like the intention of the library is not to be a sprintf replacement (sad).   Closing this out.
