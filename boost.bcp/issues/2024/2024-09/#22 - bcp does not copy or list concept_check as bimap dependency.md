# #22 - bcp does not copy or list concept_check as bimap dependency [Open]

> Username: alexramzy  
> Created at: 2024-09-15 12:26:51 UTC  
> Updated at: 2024-09-15 12:35:39 UTC  
> Url: https://github.com/boostorg/bcp/issues/22  

### Problem description  
`bcp` **neither** copies (to the **output-path**) **nor** **--list**s `concept_check` dependency for `bimap`    
  
### Problem reproduction  
  
1. `cd $HOME`  
2. `git clone https://github.com/boostorg/boost.git boost`  
3. `cd boost`  
4. `git checkout boost-1.86.0`  
5. `git submodule update --init`  
6. `./bootstrap.sh`  
7. `./b2 tools/bcp`  
8. `./dist/bin/bcp --list bimap`  
9. Nothing from `concept_check` (only `libs/bimap/include/boost/bimap/detail/concept_tags.hpp`)  
10. `mkdir $HOME/boost_for_bimap`  
11. `./dist/bin/bcp bimap $HOME/boost_for_bimap`  
12. Try to compile simple C++ program using `#include "boost/bimap/bimap.hpp` providing following include directories to compiler (I used gcc 9.4.0 on Ubuntu 20.04 LTS but it should not matter):  
- $HOME/boost_for_bimap  
- $HOME/boost_for_bimap/libs/bimap/include  
13. Observe compile error:   
  
> In file included from $HOME/boost_for_bimap/libs/bimap/include/boost/bimap/bimap.hpp:61,  
                 from $HOME/<my_simple_program>.cpp:<line_number>,  
$HOME/boost_for_bimap/libs/bimap/include/boost/bimap/detail/bimap_core.hpp:31:10: fatal error: boost/concept_check.hpp: No such file or directory  
   31 | #include <boost/concept_check.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~  
compilation terminated.  
  
### Inconsistency with `boostdep`  
  
`./dist/bin/boostdep --list-dependencies bimap`  
  
**correctly** outputs `concept_check` as first **primary** dependency:  
  
> Primary dependencies for bimap:  
  
> concept_check:  
>     <boost/concept_check.hpp>  
>         from <boost/bimap/detail/bimap_core.hpp>  
>         from <boost/bimap/list_of.hpp>  
>         from <boost/bimap/multiset_of.hpp>  
>         from <boost/bimap/set_of.hpp>  
>         from <boost/bimap/unconstrained_set_of.hpp>  
>         from <boost/bimap/unordered_multiset_of.hpp>  
>         from <boost/bimap/unordered_set_of.hpp>  
>         from <boost/bimap/vector_of.hpp>
