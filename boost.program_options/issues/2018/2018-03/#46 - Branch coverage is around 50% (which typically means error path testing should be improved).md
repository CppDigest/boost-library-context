# #46 - Branch coverage is around 50% (which typically means error path testing should be improved) [Closed]

> Username: jeking3  
> Created at: 2018-03-07 18:10:01 UTC  
> Updated at: 2022-06-16 14:38:02 UTC  
> Closed at: 2022-06-16 13:50:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/46  

```  
+lcov --gcov-tool=gcov-7 --rc lcov_branch_coverage=1 --list coverage.info  
Reading tracefile coverage.info  
                                               |Lines      |Functions|Branches    
Filename                                       |Rate    Num|Rate  Num|Rate   Num  
================================================================================  
[/home/travis/build/jeking3/boost-root/]  
boost/program_options/detail/cmdline.hpp       | 100%     1| 100%   1|50.0%    6  
boost/program_options/detail/config_file.hpp   |95.2%    21|77.8%  18|50.0%   86  
boost/program_options/detail/convert.hpp       | 100%     5| 100%   2|62.5%   16  
boost/program_options/detail/parsers.hpp       | 100%    37| 100%  13|56.1%   82  
boost/program_options/detail/value_semantic.hpp|91.3%    69|79.2%  53|29.5%  484  
boost/program_options/environment_iterator.hpp | 100%    19| 100%   4|53.8%   26  
boost/program_options/eof_iterator.hpp         | 100%    18| 100%  15|64.3%   14  
boost/program_options/errors.hpp               |94.9%    99|73.1%  78|42.2%  166  
boost/program_options/option.hpp               | 100%     7| 100%   8|39.7%   68  
boost/program_options/options_description.hpp  | 100%     1|50.0%   2|28.6%   14  
boost/program_options/parsers.hpp              | 100%     5| 100%   6|50.0%   12  
boost/program_options/positional_options.hpp   | 100%     1| 100%   1|50.0%    4  
boost/program_options/value_semantic.hpp       | 100%    67|84.7% 118|56.2%  208  
boost/program_options/variables_map.hpp        | 100%    17|89.5%  19|40.0%   25  
libs/program_options/src/cmdline.cpp           |92.8%   334|95.8%  24|62.3%  778  
libs/program_options/src/config_file.cpp       |97.2%    72| 100%   8|57.3%  206  
libs/program_options/src/convert.cpp           |93.9%    33| 100%  13|40.6%   64  
libs/program_options/s...ptions_description.cpp|93.4%   257|93.9%  33|56.2%  582  
libs/program_options/src/parsers.cpp           | 100%    58|93.8%  16|51.0%  210  
libs/program_options/src/positional_options.cpp| 100%    16| 100%   4|70.0%   20  
libs/program_options/src/split.cpp             |81.8%    11|50.0%   4|27.0%  100  
libs/program_options/src/value_semantic.cpp    |81.8%   192|85.7%  28|45.4%  608  
libs/program_options/src/variables_map.cpp     |78.0%   100|66.7%  12|56.4%  259  
================================================================================  
                                         Total:|91.9%  1440|85.2% 480|50.3% 4038  
```  
  
Build job:  
https://travis-ci.org/jeking3/program_options/jobs/350413747

---

## Comment 1

> Username: vprus  
> Created at: 2022-06-16 13:50:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/46#issuecomment-1157685126  

Given no plans for any active development, I don't plan to improve branch coverage in tests.

---

## Comment 2

> Username: andry81  
> Created at: 2022-06-16 14:38:02 UTC  
> Url: https://github.com/boostorg/program_options/issues/46#issuecomment-1157736504  

Just for instance, there is a much better approach with parse from an external help file:  
  
https://github.com/docopt/docopt.cpp  
  
The `boost/program_options` is not much convenient to use and definitely has design limit to write the help file in the source instead of outside the source. When options is not much it is not a problem, but when it increase in size it becomes a problem.
