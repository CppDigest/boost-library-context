# #501 - Document problematic initializer_list corner cases [Open]

> Username: jhcarl0814  
> Created at: 2021-02-16 12:38:16 UTC  
> Updated at: 2021-02-17 05:30:31 UTC  
> Url: https://github.com/boostorg/json/issues/501  

(Edit(2021.02.17): add `{}` and `nullptr` parts: `{}` does not have problem, `nullptr` have the same problem with `int`.)    
```c++  
#include<boost/json/src.hpp>  
#include<iostream>  
int main(int argc, char* argv[])  
{  
    boost::json::value v;  
    std::cout << v <<"     (default initialization)"<< std::endl;  
    std::cout << boost::json::value{} << "     {}        !!! expected output:\"{}\"; but `{}` must be same with `()`, so forget about it" << std::endl;  
    std::cout << boost::json::value{ {} } << "     { {} }" << std::endl;  
    std::cout << boost::json::value{ {{}} } << "   { {{}} }" << std::endl;  
    std::cout << boost::json::value{ {{{}}} } << " { {{{}}} }" << std::endl;  
    std::cout << boost::json::value() << "     ()" << std::endl;  
    std::cout << boost::json::value({}) << "       ({})" << std::endl;  
    std::cout << boost::json::value({ {} }) << "     ({ {} })" << std::endl;  
    std::cout << boost::json::value({ {{}} }) << "   ({ {{}} })" << std::endl;  
    std::cout << boost::json::value({ {{{}}} }) << " ({ {{{}}} })" << std::endl;  
  
    std::cout << std::endl;  
  
    std::cout << "---          nullptr" << std::endl;  
    std::cout << boost::json::value{ nullptr } << "       { nullptr }" << std::endl;  
    std::cout << boost::json::value{ {nullptr} } << "     { {nullptr} }" << std::endl;  
    std::cout << boost::json::value{ {{nullptr}} } << "   { {{nullptr}} }" << std::endl;  
    std::cout << boost::json::value{ {{{nullptr}}} } << " { {{{nullptr}}} }" << std::endl;  
    std::cout << boost::json::value(nullptr) << "         (nullptr)" << std::endl;  
    std::cout << boost::json::value({ nullptr }) << "         ({ nullptr })    !!! expected output: \"[null]\"" << std::endl;  
    std::cout << boost::json::value({ {nullptr} }) << "     ({ {nullptr} })" << std::endl;  
    std::cout << boost::json::value({ {{nullptr}} }) << "   ({ {{nullptr}} })" << std::endl;  
    std::cout << boost::json::value({ {{{nullptr}}} }) << " ({ {{{nullptr}}} })" << std::endl;  
  
    std::cout << std::endl;  
  
    std::cout << "---       1" << std::endl;  
    std::cout << boost::json::value{ 1 } << "       { 1 }" << std::endl;  
    std::cout << boost::json::value{ {1} } << "     { {1} }" << std::endl;  
    std::cout << boost::json::value{ {{1}} } << "   { {{1}} }" << std::endl;  
    std::cout << boost::json::value{ {{{1}}} } << " { {{{1}}} }" << std::endl;  
    std::cout << boost::json::value(1) << "         (1)" << std::endl;  
    std::cout << boost::json::value({ 1 }) << "         ({ 1 })       !!! expected output:\"[1]\"" << std::endl;  
    std::cout << boost::json::value({ {1} }) << "     ({ {1} })" << std::endl;  
    std::cout << boost::json::value({ {{1}} }) << "   ({ {{1}} })" << std::endl;  
    std::cout << boost::json::value({ {{{1}}} }) << " ({ {{{1}}} })" << std::endl;  
}  
```  
  
output:    
```  
null     (default initialization)  
null     {}        !!! expected output:"{}"; but `{}` must be same with `()`, so forget about it  
[{}]     { {} }  
[[{}]]   { {{}} }  
[[[{}]]] { {{{}}} }  
null     ()  
{}       ({})  
[{}]     ({ {} })  
[[{}]]   ({ {{}} })  
[[[{}]]] ({ {{{}}} })  
  
---          nullptr  
[null]       { nullptr }  
[[null]]     { {nullptr} }  
[[[null]]]   { {{nullptr}} }  
[[[[null]]]] { {{{nullptr}}} }  
null         (nullptr)  
null         ({ nullptr })    !!! expected output: "[null]"  
[[null]]     ({ {nullptr} })  
[[[null]]]   ({ {{nullptr}} })  
[[[[null]]]] ({ {{{nullptr}}} })  
  
---       1  
[1]       { 1 }  
[[1]]     { {1} }  
[[[1]]]   { {{1}} }  
[[[[1]]]] { {{{1}}} }  
1         (1)  
1         ({ 1 })       !!! expected output:"[1]"  
[[1]]     ({ {1} })  
[[[1]]]   ({ {{1}} })  
[[[[1]]]] ({ {{{1}}} })  
```  
  
Possible solution: put `value::value(int)`'s logic into `value_ref` (remove `value::value(int)`)    
because:  
 1. `value({1})` prefers `value::value(int)` over `value::value(std::initializer_list<value_ref>)`;    
 2. `value_ref` already handles `int`;    
  
(same goes for other fundamental types)    
  
In other words, avoid mixing initializer_list overload with non-initializer_list overloads.    
  
**(Side effect: does not support implicit conversion from `int` to `value` if really do this.)**  
  
-------------  
  
demonstration:  
 - `tree_node_old` uses initializer_list directly so the arguments are copied;    
 - `tree_node_new` uses initializer_list through `tree_node_new_ref` so the arguments are only moved;    
  
(Edit(2021.02.17): add the `{}` part; since `tree_node_old` `tree_node_new_ref` `tree_node_new` do not support default constructor, `{}` can have the desired behavior (a node with zero children) without being troubled by `()` occupying default constructor.)    
```c++  
#include<vector>  
#include<variant>  
#include<functional>  
#include<iostream>  
  
template <class T, template <class...> class Template>  
struct is_specialization : std::false_type {};  
  
template <template <class...> class Template, class... Args>  
struct is_specialization<Template<Args...>, Template> : std::true_type {};  
  
template<class T, template <class...> class Template>  
inline constexpr bool is_specialization_v = is_specialization<T, Template>::value;  
  
  
template<typename What, typename ... Args>  
struct is_present :std::bool_constant< (std::is_same_v<What, Args> || ...)> {};  
  
template<typename What, typename ... Args>  
inline constexpr bool is_present_v = is_present<What, Args...>::value;  
  
  
template <typename... Ts>  
struct Overloader : Ts... {  
    using Ts::operator()...; // exposes operator() from every base  
};  
  
template <typename... T>  
Overloader(T...)->Overloader<T...>;  
  
  
template<typename ...Ts>  
class tree_node_old  
{  
public:  
    std::variant<std::variant<Ts..., std::pair<void*, const std::type_info&>>, std::vector<tree_node_old>>data_or_children;  
  
    tree_node_old(tree_node_old const& n) :data_or_children(n.data_or_children) {}  
    tree_node_old(tree_node_old&& n):data_or_children(std::move(n.data_or_children)){}  
  
    template<  
        typename T,  
        std::enable_if_t<!is_specialization_v<std::remove_cvref_t<T>, tree_node_old>, bool> = true,  
        std::enable_if_t<is_present_v<std::remove_cvref_t<T>, Ts...>, bool> = true  
    >tree_node_old(T&& datum) : data_or_children(std::in_place_index<0>, std::variant<Ts..., std::pair<void*, const std::type_info&>>(std::forward<T>(datum))) {}  
  
    template<  
        typename T,  
        std::enable_if_t<!is_specialization_v<std::remove_cvref_t<T>, tree_node_old>, bool> = true,  
        std::enable_if_t<!is_present_v<std::remove_cvref_t<T>, Ts...>, bool> = true  
    >tree_node_old(T&& datum) : data_or_children(std::in_place_index<0>, std::variant<Ts..., std::pair<void*, const std::type_info&>>(std::in_place_index<sizeof...(Ts)>, &datum, typeid(T))) {}  
  
    tree_node_old(std::initializer_list<tree_node_old>ns) :data_or_children(std::in_place_index<1>, ns) {}  
    //tree_node_old(std::initializer_list<tree_node_old>ns) :data_or_children(std::in_place_index<1>,std::make_move_iterator(const_cast<tree_node_old*>(std::begin(ns))), std::make_move_iterator(const_cast<tree_node_old*>(std::end(ns)))) {}  
  
    template<typename ostream_t>  
    friend ostream_t&& operator<<(ostream_t&& os, tree_node_old const& n)  
    {  
        int indent = 0;  
        std::function<void(tree_node_old const& n)> print_tree_node_old = [&](tree_node_old const& n)->void  
        {  
            std::visit(  
                Overloader(  
                    [&](std::variant<Ts..., std::pair<void*, const std::type_info&>>const& pdata)->void  
                    {  
                        std::visit(  
                            Overloader(  
                                [&](Ts const& datum)->void  
                                {  
                                    std::forward<ostream_t>(os) << std::string(indent, ' ') << typeid(Ts).name() << '(' << datum << ')' << std::endl;  
                                }...,  
                                [&](std::pair<void*, const std::type_info&>const& pdatum)->void  
                                {  
                                    std::forward<ostream_t>(os) << std::string(indent, ' ') << std::get<1>(pdatum).name() << " address: " << std::get<0>(pdatum) << std::endl;  
                                }  
                            ),  
                            pdata  
                        );  
                    },  
                    [&](std::vector<tree_node_old>const& children)->void  
                    {  
                        std::forward<ostream_t>(os) << std::string(indent, ' ') << '[' << std::size(children) << ']' << std::endl;  
                        indent += 4;  
                        for (auto&& child : children)  
                        {  
                            print_tree_node_old(child);  
                        }  
                        indent -= 4;  
                    }  
                ),  
                n.data_or_children  
            );  
        };  
        print_tree_node_old(n);  
        return std::forward<ostream_t>(os);  
    }  
};  
  
template<typename ...Ts>  
class tree_node_new_ref  
{  
public:  
    std::variant<std::variant<Ts*..., std::pair<void*, const std::type_info&>>, std::vector<tree_node_new_ref>>pdata_or_children;  
  
    tree_node_new_ref(tree_node_new_ref const& r) :pdata_or_children(r.pdata_or_children) {}  
    tree_node_new_ref(tree_node_new_ref&& r)noexcept :pdata_or_children(std::move(r.pdata_or_children)) {}  
  
    template<  
        typename T,  
        std::enable_if_t<!is_specialization_v<std::remove_cvref_t<T>, tree_node_new_ref>, bool> = true,  
        std::enable_if_t<is_present_v<std::remove_cvref_t<T>, Ts...>, bool> = true  
    >tree_node_new_ref(T&& datum) : pdata_or_children(std::in_place_index<0>, std::variant<Ts*..., std::pair<void*, const std::type_info&>>(&datum)) {}  
  
    template<  
        typename T,  
        std::enable_if_t<!is_specialization_v<std::remove_cvref_t<T>, tree_node_new_ref>, bool> = true,  
        std::enable_if_t<!is_present_v<std::remove_cvref_t<T>, Ts...>, bool> = true  
    >tree_node_new_ref(T&& datum) : pdata_or_children(std::in_place_index<0>, std::variant<Ts*..., std::pair<void*, const std::type_info&>>(std::in_place_index<sizeof...(Ts)>, &datum,typeid(T) )) {}  
  
    tree_node_new_ref(std::initializer_list<tree_node_new_ref>rs) :pdata_or_children(std::in_place_index<1>, rs) {}  
  
    template<typename ostream_t>  
    friend ostream_t&& operator<<(ostream_t&& os, tree_node_new_ref const& r)  
    {  
        int indent = 0;  
        std::function<void(tree_node_new_ref const& r)> print_tree_node_new_ref = [&](tree_node_new_ref const& r)->void  
        {  
            std::visit(  
                Overloader(  
                    [&](std::variant<Ts*..., std::pair<void*, const std::type_info&>>const& pdata)->void  
                    {  
                        std::visit(  
                            Overloader(  
                                [&](Ts* const& pdatum)->void  
                                {  
                                    std::forward<ostream_t>(os) << std::string(indent, ' ') << typeid(Ts).name() << '(' << *pdatum << ')' << std::endl;  
                                }...,  
                                [&](std::pair<void*, const std::type_info&>const& pdatum)->void  
                                {  
                                    std::forward<ostream_t>(os) << std::string(indent, ' ') << std::get<1>(pdatum).name() << " address: " << std::get<0>(pdatum) << std::endl;  
                                }  
                            ),  
                            pdata  
                        );  
                    },  
                    [&](std::vector<tree_node_new_ref>const& children)->void  
                    {  
                        std::forward<ostream_t>(os) << std::string(indent, ' ') << '[' << std::size(children) << ']' << std::endl;  
                        indent += 4;  
                        for (auto&& child : children)  
                        {  
                            print_tree_node_new_ref(child);  
                        }  
                        indent -= 4;  
                    }  
                ),  
                r.pdata_or_children  
            );  
        };  
        print_tree_node_new_ref(r);  
        return std::forward<ostream_t>(os);  
    }  
};  
  
template<typename ...Ts>  
class tree_node_new;  
  
struct tree_node_convertion_policy_default  
{  
    template<typename ...Ts>  
    static void convert(std::variant<std::variant<Ts..., std::pair<void*, const std::type_info&>>, std::vector<tree_node_new<Ts...>>>&data_or_children, std::pair<void*, const std::type_info&>& pdatum)  
    {  
        data_or_children.emplace<0>(std::in_place_index<sizeof...(Ts)>, pdatum);  
    }  
};  
  
template<typename ...Ts>  
class tree_node_new  
{  
public:  
    std::variant<std::variant<Ts..., std::pair<void*, const std::type_info&>>, std::vector<tree_node_new>>data_or_children;  
  
    tree_node_new(tree_node_new const& n) :data_or_children(n.data_or_children) {}  
    tree_node_new(tree_node_new&& n)noexcept :data_or_children(std::move(n.data_or_children)) {}  
  
    template<typename tree_node_convertion_policy, size_t ...index>  
    void emplace_ith_datum(std::index_sequence<index...>, std::variant<Ts*..., std::pair<void*, const std::type_info&>>& pdata)  
    {  
        std::visit(  
            Overloader(  
                [&](Ts*& pdatum)->void  
                {  
                    this->data_or_children.emplace<0>(std::in_place_index<index>, std::move(*pdatum));  
                }...,  
                [&](std::pair<void*, const std::type_info&>& pdatum)->void  
                {  
                    tree_node_convertion_policy::convert(this->data_or_children, pdatum); //this->data_or_children.emplace<0>(std::in_place_index<sizeof...(Ts)>, pdatum);  
                }  
            ),  
            pdata  
        );  
    }  
    template<typename tree_node_convertion_policy = tree_node_convertion_policy_default>  
    tree_node_new(tree_node_new_ref<Ts...>r, tree_node_convertion_policy p = {})  
    {  
        std::visit(Overloader(  
            [&](std::variant<Ts*..., std::pair<void*, const std::type_info&>>& pdata)->void  
            {  
                this->emplace_ith_datum<tree_node_convertion_policy>(std::make_index_sequence<sizeof...(Ts)>{}, pdata);  
            },  
            [&](std::vector<tree_node_new_ref<Ts...>>& children)->void  
            {  
                this->data_or_children.emplace<1>();  
                std::get<1>(this->data_or_children).reserve(std::size(children));  
                for (auto&& child : children)  
                {  
                    std::get<1>(this->data_or_children).emplace_back(child, p);  
                }  
            }),  
            r.pdata_or_children  
        );  
    }  
    template<typename tree_node_convertion_policy = tree_node_convertion_policy_default>  
    tree_node_new(std::initializer_list<tree_node_new_ref<Ts...>>rs, tree_node_convertion_policy p = {})  
    {  
        this->data_or_children.emplace<1>();  
        std::get<1>(this->data_or_children).reserve(std::size(rs));  
        for (auto&& r : rs)  
        {  
            std::get<1>(this->data_or_children).emplace_back(r, p);  
        }  
    }  
  
    template<typename ostream_t>  
    friend ostream_t&& operator<<(ostream_t&& os, tree_node_new const& n)  
    {  
        int indent = 0;  
        std::function<void(tree_node_new const& n)> print_tree_node_new = [&](tree_node_new const& n)->void  
        {  
            std::visit(  
                Overloader(  
                    [&](std::variant<Ts..., std::pair<void*, const std::type_info&>>const& data)->void  
                    {  
                        std::visit(  
                            Overloader(  
                                [&](Ts const& datum)->void  
                                {  
                                    std::forward<ostream_t>(os) << std::string(indent, ' ') << typeid(Ts).name() << '(' << datum << ')' << std::endl;  
                                }...,  
                                [&](std::pair<void*, const std::type_info&>const& pdatum)->void  
                                {  
                                    std::forward<ostream_t>(os) << std::string(indent, ' ') << std::get<1>(pdatum).name() << " address: " << std::get<0>(pdatum) << std::endl;  
                                }  
                            ),  
                            data  
                        );  
                    },  
                    [&](std::vector<tree_node_new>const& children)->void  
                    {  
                        std::forward<ostream_t>(os) << std::string(indent, ' ') << '[' << std::size(children) << ']' << std::endl;  
                        indent += 4;  
                        for (auto&& child : children)  
                        {  
                            print_tree_node_new(child);  
                        }  
                        indent -= 4;  
                    }  
                ),  
                n.data_or_children  
            );  
        };  
        print_tree_node_new(n);  
        return std::forward<ostream_t>(os);  
    }  
};  
  
class my_move_only  
{  
public:  
    std::string data;  
  
    my_move_only() {}  
    my_move_only(std::string data) :data(std::move(data)) {}  
    //my_move_only(my_move_only const&) = delete;  
    my_move_only(my_move_only const& m) :data(m.data) { std::cout << "copy constructor1" << std::endl; }  
    my_move_only(my_move_only&& m)noexcept :data(std::move(m.data)) { std::cout << "move constructor1" << std::endl; }  
    template<typename ostream_t>  
    friend ostream_t&& operator<<(ostream_t&& os, my_move_only const& m)  
    {  
        return std::forward<ostream_t>(std::forward<ostream_t>(os) << m.data);  
    }  
};  
  
class my_move_only2  
{  
public:  
    std::string data;  
  
    my_move_only2() {}  
    my_move_only2(std::string data) :data(std::move(data)) {}  
    //my_move_only2(my_move_only2 const&) = delete;  
    my_move_only2(my_move_only2 const& m) :data(m.data) { std::cout << "copy constructor2" << std::endl; }  
    my_move_only2(my_move_only2&& m)noexcept :data(std::move(m.data)) { std::cout << "move constructor2" << std::endl; }  
    template<typename ostream_t>  
    friend ostream_t&& operator<<(ostream_t&& os, my_move_only2 const& m)  
    {  
        return std::forward<ostream_t>(std::forward<ostream_t>(os) << m.data);  
    }  
};  
  
template<typename tree_node_convertion_policy_fallback = tree_node_convertion_policy_default>  
struct tree_node_convertion_policy_my_move_only2  
{  
    template<typename ...Ts>  
    static void convert(std::variant<std::variant<Ts..., std::pair<void*, const std::type_info&>>, std::vector<tree_node_new<Ts...>>>& data_or_children, std::pair<void*, const std::type_info&>& pdatum)  
    {  
        if (std::get<1>(pdatum) == typeid(my_move_only2))  
        {  
            data_or_children.emplace<0>(my_move_only(std::move(static_cast<my_move_only2*>(std::get<0>(pdatum))->data)));  
        }  
        else  
        {  
            tree_node_convertion_policy_fallback::convert(data_or_children, pdatum); //data_or_children.emplace<0>(std::in_place_index<sizeof...(Ts)>, pdatum);  
        }  
    }  
};  
  
#include<cstdint>  
int main()  
{  
    std::cout << "--------------------tree_node_old--------------------" << std::endl;  
    typedef tree_node_old<int32_t, uint32_t, int64_t, uint64_t, float, double, long double, my_move_only> tno;  
  
    std::cout << "{}" << std::endl; std::cout << tno{} << std::endl;  
    std::cout << "{ {} }" << std::endl; std::cout << tno{ {} } << std::endl;  
    std::cout << "{ {{}} }" << std::endl; std::cout << tno{ {{}} } << std::endl;  
    std::cout << "{ {{{}}} }" << std::endl; std::cout << tno{ {{{}}} } << std::endl;  
    std::cout << "({})" << std::endl; std::cout << tno({}) << std::endl;  
    std::cout << "({ {} })" << std::endl; std::cout << tno({ {} }) << std::endl;  
    std::cout << "({ {{}} })" << std::endl; std::cout << tno({ {{}} }) << std::endl;  
    std::cout << "({ {{{}}} })" << std::endl; std::cout << tno({ {{{}}} }) << std::endl;  
  
    std::cout << "my_move_only" << std::endl; std::cout << "---" << std::endl << std::endl;  
    std::cout << "{ my_move_only }" << std::endl; std::cout << tno{ my_move_only("abc") } << std::endl;  
    std::cout << "{ {my_move_only} }" << std::endl; std::cout << tno{ { my_move_only("abc") } } << std::endl;  
    std::cout << "{ {{my_move_only}} }" << std::endl; std::cout << tno{ { { my_move_only("abc") } } } << std::endl;  
    std::cout << "{ {{{my_move_only}}} }" << std::endl; std::cout << tno{ { { { my_move_only("abc") } } } } << std::endl;  
    std::cout << "(my_move_only)" << std::endl; std::cout << tno(my_move_only("abc")) << std::endl;  
    std::cout << "( {my_move_only} )" << std::endl; std::cout << tno({ my_move_only("abc") }) << std::endl;  
    std::cout << "( {{my_move_only}} )" << std::endl; std::cout << tno({ { my_move_only("abc") } }) << std::endl;  
    std::cout << "( {{{my_move_only}}} )" << std::endl; std::cout << tno({ { { my_move_only("abc") } } }) << std::endl;  
    std::cout << "( {{{{my_move_only}}}} )" << std::endl; std::cout << tno({ { { { my_move_only("abc") } } } }) << std::endl;  
  
    std::cout << "({...})" << std::endl; std::cout <<  
        tno(  
            {  
                std::numeric_limits<int32_t>::min(),std::numeric_limits<int32_t>::max(),std::numeric_limits<uint32_t>::max(),  
                std::numeric_limits<int64_t>::min(),std::numeric_limits<int64_t>::max(),std::numeric_limits<uint64_t>::max(),  
                3.0,3.0f,3.0l,my_move_only("abc"),my_move_only2("abc"),  
                {1,{{{{}},1}}}  
            }  
        ) << std::endl;  
  
  
    std::cout << "--------------------tree_node_new_ref--------------------" << std::endl;  
    typedef tree_node_new_ref<int32_t, uint32_t, int64_t, uint64_t, float, double, long double, my_move_only> tnnr;  
  
    std::cout << "{}" << std::endl; std::cout << tnnr{} << std::endl;  
    std::cout << "{ {} }" << std::endl; std::cout << tnnr{ {} } << std::endl;  
    std::cout << "{ {{}} }" << std::endl; std::cout << tnnr{ {{}} } << std::endl;  
    std::cout << "{ {{{}}} }" << std::endl; std::cout << tnnr{ {{{}}} } << std::endl;  
    std::cout << "({})" << std::endl; std::cout << tnnr({}) << std::endl;  
    std::cout << "({ {} })" << std::endl; std::cout << tnnr({ {} }) << std::endl;  
    std::cout << "({ {{}} })" << std::endl; std::cout << tnnr({ {{}} }) << std::endl;  
    std::cout << "({ {{{}}} })" << std::endl; std::cout << tnnr({ {{{}}} }) << std::endl;  
  
    std::cout << "my_move_only" << std::endl; std::cout << "---" << std::endl << std::endl;  
    std::cout << "{ my_move_only }" << std::endl; std::cout << tnnr{ my_move_only("abc") } << std::endl;  
    std::cout << "{ {my_move_only} }" << std::endl; std::cout << tnnr{ { my_move_only("abc") } } << std::endl;  
    std::cout << "{ {{my_move_only}} }" << std::endl; std::cout << tnnr{ { { my_move_only("abc") } } } << std::endl;  
    std::cout << "{ {{{my_move_only}}} }" << std::endl; std::cout << tnnr{ { { { my_move_only("abc") } } } } << std::endl;  
    std::cout << "(my_move_only)" << std::endl; std::cout << tnnr(my_move_only("abc")) << std::endl;  
    std::cout << "( {my_move_only} )" << std::endl; std::cout << tnnr({ my_move_only("abc") }) << std::endl;  
    std::cout << "( {{my_move_only}} )" << std::endl; std::cout << tnnr({ { my_move_only("abc") } }) << std::endl;  
    std::cout << "( {{{my_move_only}}} )" << std::endl; std::cout << tnnr({ { { my_move_only("abc") } } }) << std::endl;  
    std::cout << "( {{{{my_move_only}}}} )" << std::endl; std::cout << tnnr({ { { { my_move_only("abc") } } } }) << std::endl;  
  
    std::cout << "({...})" << std::endl; std::cout <<  
        tnnr({  
            std::numeric_limits<int32_t>::min(),std::numeric_limits<int32_t>::max(),std::numeric_limits<uint32_t>::max(),  
            std::numeric_limits<int64_t>::min(),std::numeric_limits<int64_t>::max(),std::numeric_limits<uint64_t>::max(),  
            3.0,3.0f,3.0l,my_move_only("abc"),my_move_only2("abc"),  
            {1,{{{{}},1}}}  
        }) << std::endl;  
  
  
    std::cout << "--------------------tree_node_new--------------------" << std::endl;  
    typedef tree_node_new<int32_t, uint32_t, int64_t, uint64_t, float, double, long double, my_move_only> tnn;  
  
    std::cout << "{}" << std::endl; std::cout << tnn{} << std::endl;  
    std::cout << "{ {} }" << std::endl; std::cout << tnn{ {} } << std::endl;  
    std::cout << "{ {{}} }" << std::endl; std::cout << tnn{ {{}} } << std::endl;  
    std::cout << "{ {{{}}} }" << std::endl; std::cout << tnn{ {{{}}} } << std::endl;  
    std::cout << "({})" << std::endl; std::cout << tnn({}) << std::endl;  
    std::cout << "({ {} })" << std::endl; std::cout << tnn({ {} }) << std::endl;  
    std::cout << "({ {{}} })" << std::endl; std::cout << tnn({ {{}} }) << std::endl;  
    std::cout << "({ {{{}}} })" << std::endl; std::cout << tnn({ {{{}}} }) << std::endl;  
  
    std::cout << "my_move_only" << std::endl; std::cout << "---" << std::endl << std::endl;  
    std::cout << "{ my_move_only }" << std::endl; std::cout << tnn{ my_move_only("abc") } << std::endl;  
    std::cout << "{ {my_move_only} }" << std::endl; std::cout << tnn{ { my_move_only("abc") } } << std::endl;  
    std::cout << "{ {{my_move_only}} }" << std::endl; std::cout << tnn{ { { my_move_only("abc") } } } << std::endl;  
    std::cout << "{ {{{my_move_only}}} }" << std::endl; std::cout << tnn{ { { { my_move_only("abc") } } } } << std::endl;  
    std::cout << "(my_move_only)" << std::endl; std::cout << tnn(my_move_only("abc")) << std::endl;  
    std::cout << "( {my_move_only} )" << std::endl; std::cout << tnn({ my_move_only("abc") }) << std::endl;  
    std::cout << "( {{my_move_only}} )" << std::endl; std::cout << tnn({ { my_move_only("abc") } }) << std::endl;  
    std::cout << "( {{{my_move_only}}} )" << std::endl; std::cout << tnn({ { { my_move_only("abc") } } }) << std::endl;  
    std::cout << "( {{{{my_move_only}}}} )" << std::endl; std::cout << tnn({ { { { my_move_only("abc") } } } }) << std::endl;  
  
    std::cout << "({...})" << std::endl; std::cout <<  
        tnn(  
            //tnnr(  
                {  
                    std::numeric_limits<int32_t>::min(),std::numeric_limits<int32_t>::max(),std::numeric_limits<uint32_t>::max(),  
                    std::numeric_limits<int64_t>::min(),std::numeric_limits<int64_t>::max(),std::numeric_limits<uint64_t>::max(),  
                    3.0,3.0f,3.0l,my_move_only("abc"),my_move_only2("abc"),  
                    {1,{{{{}},1}}}  
                }  
            //)  
        ) << std::endl;  
    std::cout << "({...})" << std::endl; std::cout <<  
        tnn(  
            //tnnr(  
                {  
                    std::numeric_limits<int32_t>::min(),std::numeric_limits<int32_t>::max(),std::numeric_limits<uint32_t>::max(),  
                    std::numeric_limits<int64_t>::min(),std::numeric_limits<int64_t>::max(),std::numeric_limits<uint64_t>::max(),  
                    3.0,3.0f,3.0l,my_move_only("abc"),my_move_only2("abc"),  
                    {1,{{{{}},1}}}  
                }  
            //)  
            , tree_node_convertion_policy_my_move_only2{}  
        ) << std::endl;  
}  
```  
  
output:    
```  
--------------------tree_node_old--------------------  
{}  
[0]  
  
{ {} }  
[1]  
    [0]  
  
{ {{}} }  
[1]  
    [1]  
        [0]  
  
{ {{{}}} }  
[1]  
    [1]  
        [1]  
            [0]  
  
({})  
[0]  
  
({ {} })  
[1]  
    [0]  
  
({ {{}} })  
[1]  
    [1]  
        [0]  
  
({ {{{}}} })  
[1]  
    [1]  
        [1]  
            [0]  
  
my_move_only  
---  
  
{ my_move_only }  
move constructor1  
move constructor1  
copy constructor1  
[1]  
    class my_move_only(abc)  
  
{ {my_move_only} }  
move constructor1  
move constructor1  
copy constructor1  
copy constructor1  
[1]  
    [1]  
        class my_move_only(abc)  
  
{ {{my_move_only}} }  
move constructor1  
move constructor1  
copy constructor1  
copy constructor1  
copy constructor1  
[1]  
    [1]  
        [1]  
            class my_move_only(abc)  
  
{ {{{my_move_only}}} }  
move constructor1  
move constructor1  
copy constructor1  
copy constructor1  
copy constructor1  
copy constructor1  
[1]  
    [1]  
        [1]  
            [1]  
                class my_move_only(abc)  
  
(my_move_only)  
move constructor1  
move constructor1  
class my_move_only(abc)  
  
( {my_move_only} )  
move constructor1  
move constructor1  
copy constructor1  
[1]  
    class my_move_only(abc)  
  
( {{my_move_only}} )  
move constructor1  
move constructor1  
copy constructor1  
copy constructor1  
[1]  
    [1]  
        class my_move_only(abc)  
  
( {{{my_move_only}}} )  
move constructor1  
move constructor1  
copy constructor1  
copy constructor1  
copy constructor1  
[1]  
    [1]  
        [1]  
            class my_move_only(abc)  
  
( {{{{my_move_only}}}} )  
move constructor1  
move constructor1  
copy constructor1  
copy constructor1  
copy constructor1  
copy constructor1  
[1]  
    [1]  
        [1]  
            [1]  
                class my_move_only(abc)  
  
({...})  
move constructor1  
move constructor1  
copy constructor1  
[12]  
    int(-2147483648)  
    int(2147483647)  
    unsigned int(4294967295)  
    __int64(-9223372036854775808)  
    __int64(9223372036854775807)  
    unsigned __int64(18446744073709551615)  
    double(3)  
    float(3)  
    long double(3)  
    class my_move_only(abc)  
    class my_move_only2 address: 001CEC2C  
    [2]  
        int(1)  
        [1]  
            [2]  
                [1]  
                    [0]  
                int(1)  
  
--------------------tree_node_new_ref--------------------  
{}  
[0]  
  
{ {} }  
[1]  
    [0]  
  
{ {{}} }  
[1]  
    [1]  
        [0]  
  
{ {{{}}} }  
[1]  
    [1]  
        [1]  
            [0]  
  
({})  
[0]  
  
({ {} })  
[1]  
    [0]  
  
({ {{}} })  
[1]  
    [1]  
        [0]  
  
({ {{{}}} })  
[1]  
    [1]  
        [1]  
            [0]  
  
my_move_only  
---  
  
{ my_move_only }  
[1]  
    class my_move_only(abc)  
  
{ {my_move_only} }  
[1]  
    [1]  
        class my_move_only(abc)  
  
{ {{my_move_only}} }  
[1]  
    [1]  
        [1]  
            class my_move_only(abc)  
  
{ {{{my_move_only}}} }  
[1]  
    [1]  
        [1]  
            [1]  
                class my_move_only(abc)  
  
(my_move_only)  
class my_move_only(abc)  
  
( {my_move_only} )  
[1]  
    class my_move_only(abc)  
  
( {{my_move_only}} )  
[1]  
    [1]  
        class my_move_only(abc)  
  
( {{{my_move_only}}} )  
[1]  
    [1]  
        [1]  
            class my_move_only(abc)  
  
( {{{{my_move_only}}}} )  
[1]  
    [1]  
        [1]  
            [1]  
                class my_move_only(abc)  
  
({...})  
[12]  
    int(-2147483648)  
    int(2147483647)  
    unsigned int(4294967295)  
    __int64(-9223372036854775808)  
    __int64(9223372036854775807)  
    unsigned __int64(18446744073709551615)  
    double(3)  
    float(3)  
    long double(3)  
    class my_move_only(abc)  
    class my_move_only2 address: 001CE4A4  
    [2]  
        int(1)  
        [1]  
            [2]  
                [1]  
                    [0]  
                int(1)  
  
--------------------tree_node_new--------------------  
{}  
[0]  
  
{ {} }  
[1]  
    [0]  
  
{ {{}} }  
[1]  
    [1]  
        [0]  
  
{ {{{}}} }  
[1]  
    [1]  
        [1]  
            [0]  
  
({})  
[0]  
  
({ {} })  
[1]  
    [0]  
  
({ {{}} })  
[1]  
    [1]  
        [0]  
  
({ {{{}}} })  
[1]  
    [1]  
        [1]  
            [0]  
  
my_move_only  
---  
  
{ my_move_only }  
move constructor1  
[1]  
    class my_move_only(abc)  
  
{ {my_move_only} }  
move constructor1  
[1]  
    [1]  
        class my_move_only(abc)  
  
{ {{my_move_only}} }  
move constructor1  
[1]  
    [1]  
        [1]  
            class my_move_only(abc)  
  
{ {{{my_move_only}}} }  
move constructor1  
[1]  
    [1]  
        [1]  
            [1]  
                class my_move_only(abc)  
  
(my_move_only)  
move constructor1  
class my_move_only(abc)  
  
( {my_move_only} )  
move constructor1  
[1]  
    class my_move_only(abc)  
  
( {{my_move_only}} )  
move constructor1  
[1]  
    [1]  
        class my_move_only(abc)  
  
( {{{my_move_only}}} )  
move constructor1  
[1]  
    [1]  
        [1]  
            class my_move_only(abc)  
  
( {{{{my_move_only}}}} )  
move constructor1  
[1]  
    [1]  
        [1]  
            [1]  
                class my_move_only(abc)  
  
({...})  
move constructor1  
[12]  
    int(-2147483648)  
    int(2147483647)  
    unsigned int(4294967295)  
    __int64(-9223372036854775808)  
    __int64(9223372036854775807)  
    unsigned __int64(18446744073709551615)  
    double(3)  
    float(3)  
    long double(3)  
    class my_move_only(abc)  
    class my_move_only2 address: 001CD7C4  
    [2]  
        int(1)  
        [1]  
            [2]  
                [1]  
                    [0]  
                int(1)  
  
({...})  
move constructor1  
move constructor1  
[12]  
    int(-2147483648)  
    int(2147483647)  
    unsigned int(4294967295)  
    __int64(-9223372036854775808)  
    __int64(9223372036854775807)  
    unsigned __int64(18446744073709551615)  
    double(3)  
    float(3)  
    long double(3)  
    class my_move_only(abc)  
    class my_move_only(abc)  
    [2]  
        int(1)  
        [1]  
            [2]  
                [1]  
                    [0]  
                int(1)  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-02-16 14:10:29 UTC  
> Url: https://github.com/boostorg/json/issues/501#issuecomment-779860788  

Oh wow, that's quite a program you wrote there! Yes `initializer_list` construction is not perfect but that's a limitation of C++. Boost.JSON does the best it can to support the init-list syntax as a convenience. I will research your proposal and "circle back" shortly.

---

## Comment 2

> Username: jhcarl0814  
> Created at: 2021-02-17 04:49:06 UTC  
> Updated at: 2021-02-17 04:50:40 UTC  
> Url: https://github.com/boostorg/json/issues/501#issuecomment-780296178  

@vinniefalco Thanks for your attention. By the way I added some tests to my original comment above. It seems there's some problem with plain `{}`, but I don't think I should ask for deleting `value`'s default constructor (semantically and documentally) to make plain `{}` work correctly (yields object with zero children) this time.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-02-17 05:30:05 UTC  
> Url: https://github.com/boostorg/json/issues/501#issuecomment-780310316  

> the basic problem is that it's hard to disambiguate between `value v{1}`, `value v = {1}`  and `value v{{1}}`, and similarly with `()`  
  
Making initializer-list construction work "correctly" in all cases is not really possible due to limitations of C++. If you have a look at "JSON For Modern C++" (nlohmann's JSON) you can see that the same problems exist. It is inherent to the language.  
  
We could in theory add various workarounds to make some syntax work better than others. However, I think that the current implementation of Boost.JSON strikes the right balance between having most common initializer-list cases work correctly, and making the `json::value` class usable (for example, supporting implicit construction from integers).  
  
The documentation could probably be improved to note these corner cases, so that people don't fall into the trap and waste time.
