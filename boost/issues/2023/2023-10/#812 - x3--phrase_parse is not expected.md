# #812 - x3::phrase_parse   is not expected [Closed]

> Username: y138828  
> Created at: 2023-10-07 10:53:03 UTC  
> Updated at: 2023-10-08 00:30:41 UTC  
> Closed at: 2023-10-08 00:30:41 UTC  
> Url: https://github.com/boostorg/boost/issues/812  

Hello everyone：  
I expect to parse Lua syntax through x3, and I have the following results  
My question is, in this definition, [auto const expdef=simexp | (simexp>>binop>>simexp) | (unop>>simexp);]  
Why not choose (simexp>>binop>>simexp) when it fails。  
The Complete Syntax of Lua：https://www.lua.org/manual/5.3/manual.html#9  
  
```c  
<chunk>  
  <try>\n        function Lu</try>  
  <block>  
    <try>\n        function Lu</try>  
    <stat>  
      <try>\n        function Lu</try>  
      <funcname>  
        <try> LuaPrint(str)\n     </try>  
        <Name>  
          <try> LuaPrint(str)\n     </try>  
          <success>(str)\n            pr</success>  
          <attributes>[L, u, a, P, r, i, n, t]</attributes>  
        </Name>  
        <Name>  
          <try> LuaPrint(str)\n     </try>  
          <success>(str)\n            pr</success>  
          <attributes>[L, u, a, P, r, i, n, t]</attributes>  
        </Name>  
        <success>(str)\n            pr</success>  
      </funcname>  
      <funcbody>  
        <try>(str)\n            pr</try>  
        <parlist>  
          <try>str)\n            pri</try>  
          <namelist>  
            <try>str)\n            pri</try>  
            <Name>  
              <try>str)\n            pri</try>  
              <success>)\n            print(</success>  
              <attributes>[s, t, r]</attributes>  
            </Name>  
            <success>)\n            print(</success>  
          </namelist>  
          <success>)\n            print(</success>  
        </parlist>  
        <block>  
          <try>\n            print("</try>  
          <stat>  
            <try>\n            print("</try>  
            <label>  
              <try>print("[in lua]:" ..</try>  
              <fail/>  
            </label>  
            <functioncall>  
              <try>print("[in lua]:" ..</try>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <args>  
                <try>("[in lua]:" .. tost</try>  
                <explist>  
                  <try>"[in lua]:" .. tostr</try>  
                  <exp>  
                    <try>"[in lua]:" .. tostr</try>  
                    <simexp>  
                      <try>"[in lua]:" .. tostr</try>  
                      <literal_string>  
                        <try>"[in lua]:" .. tostr</try>  
                        <success> .. tostring(str))\n </success>  
                        <attributes>[[, i, n,  , l, u, a, ], :]</attributes>  
                      </literal_string>  
                      <success> .. tostring(str))\n </success>  
                    </simexp>  
                    <success> .. tostring(str))\n </success>  
                  </exp>  
                  <success> .. tostring(str))\n </success>  
                </explist>  
                <tableconstructor>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </tableconstructor>  
                <literal_string>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </literal_string>  
                <fail/>  
              </args>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <fail/>  
            </functioncall>  
            <varlist>  
              <try>print("[in lua]:" ..</try>  
              <var>  
                <try>print("[in lua]:" ..</try>  
                <Name>  
                  <try>print("[in lua]:" ..</try>  
                  <success>("[in lua]:" .. tost</success>  
                  <attributes>[p, r, i, n, t]</attributes>  
                </Name>  
                <success>("[in lua]:" .. tost</success>  
              </var>  
              <success>("[in lua]:" .. tost</success>  
            </varlist>  
            <fail/>  
          </stat>  
          <retstat>  
            <try>("[in lua]:" .. tost</try>  
            <fail/>  
          </retstat>  
          <stat>  
            <try>\n            print("</try>  
            <label>  
              <try>print("[in lua]:" ..</try>  
              <fail/>  
            </label>  
            <functioncall>  
              <try>print("[in lua]:" ..</try>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <args>  
                <try>("[in lua]:" .. tost</try>  
                <explist>  
                  <try>"[in lua]:" .. tostr</try>  
                  <exp>  
                    <try>"[in lua]:" .. tostr</try>  
                    <simexp>  
                      <try>"[in lua]:" .. tostr</try>  
                      <literal_string>  
                        <try>"[in lua]:" .. tostr</try>  
                        <success> .. tostring(str))\n </success>  
                        <attributes>[[, i, n,  , l, u, a, ], :]</attributes>  
                      </literal_string>  
                      <success> .. tostring(str))\n </success>  
                    </simexp>  
                    <success> .. tostring(str))\n </success>  
                  </exp>  
                  <success> .. tostring(str))\n </success>  
                </explist>  
                <tableconstructor>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </tableconstructor>  
                <literal_string>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </literal_string>  
                <fail/>  
              </args>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <fail/>  
            </functioncall>  
            <varlist>  
              <try>print("[in lua]:" ..</try>  
              <var>  
                <try>print("[in lua]:" ..</try>  
                <Name>  
                  <try>print("[in lua]:" ..</try>  
                  <success>("[in lua]:" .. tost</success>  
                  <attributes>[p, r, i, n, t]</attributes>  
                </Name>  
                <success>("[in lua]:" .. tost</success>  
              </var>  
              <success>("[in lua]:" .. tost</success>  
            </varlist>  
            <fail/>  
          </stat>  
          <success>("[in lua]:" .. tost</success>  
        </block>  
        <fail/>  
      </funcbody>  
      <fail/>  
    </stat>  
    <retstat>  
      <try>("[in lua]:" .. tost</try>  
      <fail/>  
    </retstat>  
    <stat>  
      <try>\n        function Lu</try>  
      <funcname>  
        <try> LuaPrint(str)\n     </try>  
        <Name>  
          <try> LuaPrint(str)\n     </try>  
          <success>(str)\n            pr</success>  
          <attributes>[L, u, a, P, r, i, n, t]</attributes>  
        </Name>  
        <Name>  
          <try> LuaPrint(str)\n     </try>  
          <success>(str)\n            pr</success>  
          <attributes>[L, u, a, P, r, i, n, t]</attributes>  
        </Name>  
        <success>(str)\n            pr</success>  
      </funcname>  
      <funcbody>  
        <try>(str)\n            pr</try>  
        <parlist>  
          <try>str)\n            pri</try>  
          <namelist>  
            <try>str)\n            pri</try>  
            <Name>  
              <try>str)\n            pri</try>  
              <success>)\n            print(</success>  
              <attributes>[s, t, r]</attributes>  
            </Name>  
            <success>)\n            print(</success>  
          </namelist>  
          <success>)\n            print(</success>  
        </parlist>  
        <block>  
          <try>\n            print("</try>  
          <stat>  
            <try>\n            print("</try>  
            <label>  
              <try>print("[in lua]:" ..</try>  
              <fail/>  
            </label>  
            <functioncall>  
              <try>print("[in lua]:" ..</try>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <args>  
                <try>("[in lua]:" .. tost</try>  
                <explist>  
                  <try>"[in lua]:" .. tostr</try>  
                  <exp>  
                    <try>"[in lua]:" .. tostr</try>  
                    <simexp>  
                      <try>"[in lua]:" .. tostr</try>  
                      <literal_string>  
                        <try>"[in lua]:" .. tostr</try>  
                        <success> .. tostring(str))\n </success>  
                        <attributes>[[, i, n,  , l, u, a, ], :]</attributes>  
                      </literal_string>  
                      <success> .. tostring(str))\n </success>  
                    </simexp>  
                    <success> .. tostring(str))\n </success>  
                  </exp>  
                  <success> .. tostring(str))\n </success>  
                </explist>  
                <tableconstructor>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </tableconstructor>  
                <literal_string>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </literal_string>  
                <fail/>  
              </args>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <fail/>  
            </functioncall>  
            <varlist>  
              <try>print("[in lua]:" ..</try>  
              <var>  
                <try>print("[in lua]:" ..</try>  
                <Name>  
                  <try>print("[in lua]:" ..</try>  
                  <success>("[in lua]:" .. tost</success>  
                  <attributes>[p, r, i, n, t]</attributes>  
                </Name>  
                <success>("[in lua]:" .. tost</success>  
              </var>  
              <success>("[in lua]:" .. tost</success>  
            </varlist>  
            <fail/>  
          </stat>  
          <retstat>  
            <try>("[in lua]:" .. tost</try>  
            <fail/>  
          </retstat>  
          <stat>  
            <try>\n            print("</try>  
            <label>  
              <try>print("[in lua]:" ..</try>  
              <fail/>  
            </label>  
            <functioncall>  
              <try>print("[in lua]:" ..</try>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <args>  
                <try>("[in lua]:" .. tost</try>  
                <explist>  
                  <try>"[in lua]:" .. tostr</try>  
                  <exp>  
                    <try>"[in lua]:" .. tostr</try>  
                    <simexp>  
                      <try>"[in lua]:" .. tostr</try>  
                      <literal_string>  
                        <try>"[in lua]:" .. tostr</try>  
                        <success> .. tostring(str))\n </success>  
                        <attributes>[[, i, n,  , l, u, a, ], :]</attributes>  
                      </literal_string>  
                      <success> .. tostring(str))\n </success>  
                    </simexp>  
                    <success> .. tostring(str))\n </success>  
                  </exp>  
                  <success> .. tostring(str))\n </success>  
                </explist>  
                <tableconstructor>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </tableconstructor>  
                <literal_string>  
                  <try>("[in lua]:" .. tost</try>  
                  <fail/>  
                </literal_string>  
                <fail/>  
              </args>  
              <prefixexp>  
                <try>print("[in lua]:" ..</try>  
                <var>  
                  <try>print("[in lua]:" ..</try>  
                  <Name>  
                    <try>print("[in lua]:" ..</try>  
                    <success>("[in lua]:" .. tost</success>  
                    <attributes>[p, r, i, n, t]</attributes>  
                  </Name>  
                  <success>("[in lua]:" .. tost</success>  
                </var>  
                <success>("[in lua]:" .. tost</success>  
              </prefixexp>  
              <fail/>  
            </functioncall>  
            <varlist>  
              <try>print("[in lua]:" ..</try>  
              <var>  
                <try>print("[in lua]:" ..</try>  
                <Name>  
                  <try>print("[in lua]:" ..</try>  
                  <success>("[in lua]:" .. tost</success>  
                  <attributes>[p, r, i, n, t]</attributes>  
                </Name>  
                <success>("[in lua]:" .. tost</success>  
              </var>  
              <success>("[in lua]:" .. tost</success>  
            </varlist>  
            <fail/>  
          </stat>  
          <success>("[in lua]:" .. tost</success>  
        </block>  
        <fail/>  
      </funcbody>  
      <fail/>  
    </stat>  
    <success>("[in lua]:" .. tost</success>  
  </block>  
  <success>("[in lua]:" .. tost</success>  
</chunk>  
parse fail!  
Program ended with exit code: 0  
```   
  
  
Here is my test code:  
  
```c++  
#include <iostream>  
#include <iomanip>  
#include <string>  
#include <map>  
#include <vector>  
#include <list>  
#include <numeric>  
#include <fstream>  
  
#define BOOST_SPIRIT_DEBUG 1  
#define BOOST_SPIRIT_X3_DEBUG 1  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/position_tagged.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/spirit/home/x3/support/utility/error_reporting.hpp>  
#include <boost/spirit/home/x3/support/utility/annotate_on_success.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/fusion/include/std_pair.hpp>  
#include <boost/fusion/include/io.hpp>  
namespace x3 = boost::spirit::x3;  
  
namespace ast {  
  
using unop = x3::unused_type;  
using binop = x3::unused_type;  
using fieldsep = x3::unused_type;  
using field = x3::unused_type;  
using fieldlist = x3::unused_type;  
using tableconstructor = x3::unused_type;  
using parlist = x3::unused_type;  
using funcbody = x3::unused_type;  
using functiondef = x3::unused_type;  
using args = x3::unused_type;  
using functioncall = x3::unused_type;  
using prefixexp = x3::unused_type;  
using exp = x3::unused_type;  
using explist = x3::unused_type;  
using namelist = x3::unused_type;  
using var = x3::unused_type;  
using varlist = x3::unused_type;  
using funcname = x3::unused_type;  
using label = x3::unused_type;  
using retstat = x3::unused_type;  
using stat = x3::unused_type;  
using block = x3::unused_type;  
using chunk = x3::unused_type;  
  
using boost::fusion::operator<<;  
}  
  
//BOOST_FUSION_ADAPT_STRUCT(antgroup::ast::person_attr,f_name,l_name);  
  
namespace parse {  
  
struct x3_base_handle {  
    template <typename Iterator, typename Exception, typename Context>  
    x3::error_handler_result on_error(Iterator& first, Iterator const& last , Exception const& x, Context const& context){  
//        std::string message = std::string("Error! here:") + *x.where() + ", Expecting:" + x.which() ;  
//        std::cerr << message << std::endl;  
        return x3::error_handler_result::fail;  
    }  
    template <typename T, typename Iterator, typename Context>  
    inline void on_success(Iterator const& first, Iterator const& last, T& ast, Context const& context){  
        if((last - first) > 0) {  
//            auto value = x3::_val(context);  
//            auto where = x3::_where(context);  
//            auto attr = x3::_attr(context);  
//            auto pass = x3::_pass(context);  
//            std::cout << "success:" << std::string(first,last) << std::endl;  
        }  
    }  
};  
  
struct unop_class : x3_base_handle{};  
struct binop_class : x3_base_handle{};  
struct fieldsep_class : x3_base_handle{};  
struct field_class : x3_base_handle{};  
struct fieldlist_class : x3_base_handle{};  
struct tableconstructor_class : x3_base_handle{};  
struct parlist_class : x3_base_handle{};  
struct funcbody_class : x3_base_handle{};  
struct functiondef_class : x3_base_handle{};  
struct args_class : x3_base_handle{};  
struct functioncall_class : x3_base_handle{};  
struct prefixexp_class : x3_base_handle{};  
struct exp_class : x3_base_handle{};  
struct explist_class : x3_base_handle{};  
struct namelist_class : x3_base_handle{};  
struct var_class : x3_base_handle{};  
struct varlist_class : x3_base_handle{};  
struct funcname_class : x3_base_handle{};  
struct label_class : x3_base_handle{};  
struct retstat_class : x3_base_handle{};  
struct stat_class : x3_base_handle{};  
struct block_class : x3_base_handle{};  
struct chunk_class : x3_base_handle{};  
  
  
auto const unop = x3::rule<unop_class,ast::unop>{"unop"};  
auto const binop = x3::rule<binop_class,ast::binop>{"binop"};  
auto const fieldsep = x3::rule<fieldsep_class,ast::fieldsep>{"fieldsep"};  
auto const field = x3::rule<field_class,ast::field>{"field"};  
auto const fieldlist = x3::rule<fieldlist_class,ast::fieldlist>{"fieldlist"};  
auto const tableconstructor = x3::rule<tableconstructor_class,ast::tableconstructor>{"tableconstructor"};  
auto const parlist = x3::rule<parlist_class,ast::parlist>{"parlist"};  
auto const funcbody = x3::rule<funcbody_class,ast::funcbody>{"funcbody"};  
auto const functiondef = x3::rule<functiondef_class,ast::functiondef>{"functiondef"};  
auto const args = x3::rule<args_class,ast::args>{"args"};  
auto const functioncall = x3::rule<functioncall_class,ast::functioncall>{"functioncall"};  
auto const prefixexp = x3::rule<prefixexp_class,ast::prefixexp>{"prefixexp"};  
auto const exp = x3::rule<exp_class,ast::exp>{"exp"};  
auto const explist = x3::rule<explist_class,ast::explist>{"explist"};  
auto const namelist = x3::rule<namelist_class,ast::namelist>{"namelist"};  
auto const var = x3::rule<var_class,ast::var>{"var"};  
auto const varlist = x3::rule<varlist_class,ast::varlist>{"varlist"};  
auto const funcname = x3::rule<funcname_class,ast::funcname>{"funcname"};  
auto const label = x3::rule<label_class,ast::label>{"label"};  
auto const retstat = x3::rule<retstat_class,ast::retstat>{"retstat"};  
auto const stat = x3::rule<stat_class,ast::stat>{"stat"};  
auto const block = x3::rule<block_class,ast::block>{"block"};  
auto const chunk = x3::rule<chunk_class,ast::chunk>{"chunk"};  
  
  
struct literal_string_class : x3_base_handle{};  
auto const literal_string = x3::rule<literal_string_class, std::string>{"literal_string"};  
auto const literal_string_def = (x3::lexeme['"' >> +(x3::char_ - '"') >> '"']) | (x3::lexeme['\'' >> +(x3::char_ - '\'') >> '\'']);  
BOOST_SPIRIT_DEFINE(literal_string);  
  
template <typename Tag>  
static auto make_sym(std::initializer_list<char const*> elements) {  
    x3::symbols<x3::unused_type> instance;  
    for (auto el : elements)  
        instance += el;  
    return instance;  
}  
  
static auto const reserve_keywords = make_sym<struct reserve_keywords_class>({"and","break","do","else","elseif","end","false","for","function","goto","if","in","local","nil","not","or","repeat","return","then","true","until","while"});  
  
struct Name_class : x3_base_handle{};  
auto const Name = x3::rule<Name_class,std::string>{"Name"};  
auto const Name_def = ((x3::ascii::char_("a-zA-Z") | x3::ascii::char_('_')) >> *(x3::ascii::char_("0-9a-zA-Z") | x3::ascii::char_('_'))) - reserve_keywords;  
BOOST_SPIRIT_DEFINE(Name);  
  
struct simexp_class : x3_base_handle{};  
auto const simexp = x3::rule<simexp_class>{"simexp"};  
auto const simexp_def = x3::lit("nil") | "false" | "true" | x3::uint64 | x3::double_ | literal_string | "..." | functiondef | prefixexp | tableconstructor ;  
BOOST_SPIRIT_DEFINE(simexp);  
  
  
//https://www.lua.org/manual/5.3/manual.html#9  
auto const unop_def = x3::ascii::char_('-') | x3::lit("not") | x3::ascii::char_('#') | x3::ascii::char_('~');  
auto const binop_def = x3::ascii::char_('+') | x3::ascii::char_('-') | x3::ascii::char_('*') | x3::ascii::char_('/') | x3::lit("//") | x3::ascii::char_('^') | x3::ascii::char_('%') | x3::ascii::char_('&') | x3::ascii::char_('~') | x3::ascii::char_('|') | x3::lit(">>") | x3::lit("<<") | x3::lit("..") | x3::ascii::char_('<') | x3::lit("<=") | x3::ascii::char_('>') | x3::lit(">=") | x3::lit("==") | x3::lit("~=") | x3::lit("and") | x3::lit("or");  
auto const fieldsep_def = x3::lit(",") | x3::lit(";");  
auto const field_def = ("[" >> exp >> "]" >> "=" >> exp) | (Name >> "=" >> exp) | exp;  
auto const fieldlist_def = (field >> *(fieldsep >> field)) | (field >> *(fieldsep >> field) >> fieldsep);  
auto const tableconstructor_def = x3::lit("{") >> "}" | x3::lit("{") >> fieldlist >> "}";  
auto const parlist_def = namelist | (namelist >> "," >> "...") | "...";  
auto const funcbody_def = ("(" > parlist > ")" > block > "end") | (x3::lit("(") > ")" > block > "end");  
auto const functiondef_def = "function" >> funcbody;  
auto const args_def = ("(" >> explist >> ")") | (x3::lit("(") >> ")") | tableconstructor | literal_string;  
auto const functioncall_def = (prefixexp >> args) | (prefixexp >> ":" >> Name >> args);  
auto const prefixexp_def = var | functioncall | ("(" > exp > ")");  
auto const exp_def = simexp | (simexp >> binop >> simexp) | (unop >> simexp);  
auto const explist_def = exp >> *("," >> exp); // exp % ","  
auto const namelist_def = Name >> *("," >> Name);  
auto const var_def = Name | (prefixexp >> "[" >> exp >> "]") | (prefixexp >> "." >>  Name);  
auto const varlist_def = var >> *("," >> var);  
auto const funcname_def = (Name >> *("." >> Name) >> ":" >> Name) | (Name >> *("." >> Name));  
auto const label_def = "::" >> Name >> "::";  
auto const retstat_def = ("return" >> explist >> ";") | ("return" >> explist) | "return" | (x3::lit("return") >> ";");  
auto const stat_def = x3::lit(";") | "break" | ("goto" > Name) | ("do" > block > "end") | ("while" > exp > "do" > block > "end") | ("repeat" > block > "until" > exp) | ("if" > exp > "then" > block > *("elseif" > exp > "then" > block) > "else" > block > "end") | ("if" > exp > "then" > block > *("elseif" > exp > "then" > block) > "end") | ("for" > Name > "=" > exp > "," > exp > "do" > block > "end") | ("for" > Name > "=" > exp > "," > exp > "," > exp > "do" > block > "end") | ("for" > namelist > "in" > explist > "do" > block > "end") | ("function" > funcname > funcbody) | (x3::lit("local") > "function" > Name > funcbody) | ("local" > namelist) | ("local" > namelist > "=" > explist) | label | functioncall | (varlist > "=" > explist);  
auto block_def = (*(stat) >> retstat) | *(stat);  
auto chunk_def = block;  
  
  
BOOST_SPIRIT_DEFINE(chunk,block,stat,retstat,label,funcname,varlist,var,namelist,explist,exp,prefixexp,functioncall,args,functiondef,funcbody,parlist,tableconstructor,fieldlist,field,fieldsep,binop,unop);  
  
}  
  
//local,nil,true,false,end,then,if,elseif,not,and,or,function  
  
  
int main(int argc,const char *argv[]) {  
      
    std::string lua_src_content = R"(  
        function LuaPrint(str)  
            print("[in lua]:" .. tostring(str))  
        end  
    )";  
      
    typedef std::string::const_iterator iterator_type;  
    iterator_type iter = lua_src_content.begin();  
    iterator_type end = lua_src_content.end();  
    ast::chunk out;  
    bool r = x3::phrase_parse(iter, end, parse::chunk, x3::ascii::space, out);  
    if(r && (iter == end)){  
        std::cout << "parse success!" << std::endl;  
    }else{  
        std::cerr << "parse fail!" << std::endl;  
    }  
      
    return 0;  
}  
  
```
