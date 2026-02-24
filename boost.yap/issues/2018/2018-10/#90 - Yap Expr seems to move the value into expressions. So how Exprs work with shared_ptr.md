# #90 - Yap Expr seems to move the value into expressions. So how Exprs work with shared_ptr? [Closed]

> Username: davidmlw  
> Created at: 2018-10-30 03:58:36 UTC  
> Updated at: 2018-11-13 03:32:39 UTC  
> Closed at: 2018-11-13 03:32:39 UTC  
> Url: https://github.com/boostorg/yap/issues/90  

I designed a class that uses shared_ptr to contain resources. But when I use Expr::term to hold the objects of that class, the resources will be released since they are "moved" out. Here is a piece of code to demonstrate my purpose. Would you please help me out?  
  
Best regards,  
  
`````  
#include <boost/yap/yap.hpp>  
#include <boost/yap/print.hpp>  
  
#include <iostream>  
#include <memory>  
  
using namespace std;  
  
struct foo_expr :  
  public boost::yap::expression<boost::yap::expr_kind::terminal,  
                                boost::hana::tuple<std::shared_ptr<int>>>  
{  
  foo_expr () { cout << "foo_expr ()" << endl;}  
  explicit foo_expr (std::shared_ptr<int> var) {  
    this->elements = boost::hana::tuple<std::shared_ptr<int>>(var);  
  }  
};  
  
struct get_foo_value  
{  
  boost::yap::terminal<boost::yap::expression, int>  
  operator() (boost::yap::terminal<boost::yap::expression, std::shared_ptr<int>> const & expr)  
  {  
    boost::yap::print(std::cout, expr);  
    int x = (* boost::yap::value(expr).get());  
    return boost::yap::make_terminal<boost::yap::expression, int>(std::move(x));  
  }  
};  
  
struct FooBase  
{  
  virtual int evaluate() {return 0;}  
};  
  
template <typename Expr>  
struct Foo : FooBase  
{  
  Expr expr;  
  Foo(Expr _expr) : expr(_expr) {}  
  int evaluate() override {  
    return boost::yap::evaluate(boost::yap::transform(expr, get_foo_value()));  
    return 0;  
  }  
};  
  
template <typename Expr>  
shared_ptr<FooBase> build_foo (Expr expr)  
{  
  return make_shared<Foo<Expr>>(expr);  
}  
  
void run(shared_ptr<FooBase> foo)  
{  
  cout << foo->evaluate() << endl;;  
}  
  
  
struct FooContainer{  
  shared_ptr<FooBase> foo;  
  shared_ptr<int> ip;  
  void build()  
  {  
    ip = make_shared<int>(41);  
    auto a = foo_expr(ip);  
    foo = build_foo(a + 1);  
  }  
  void run() {  
    cout << foo->evaluate() << endl;;  
  }  
};  
  
int main()  
{  
  shared_ptr<FooBase> foo;  
  {  
    auto ip = make_shared<int>(41);  
    auto a = foo_expr(ip);  
    foo = build_foo(a + 1);  
  }  
  {  
    run(foo); // wrong ip has been released  
  }  
    
  auto ip = make_shared<int>(41);  
  {  
    auto a = foo_expr(ip);  
    foo = build_foo(a + 1);  
  }  
  {  
    run(foo); // 42  
  }  
    
  FooContainer fc;  
  fc.build();  
  fc.run(); // 42 but fc.ip is going to be released.  
  fc.run(); // wrong, fc.ip has been released.  
  fc.run(); // wrong too  
}  
`````

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-11-13 03:32:39 UTC  
> Url: https://github.com/boostorg/yap/issues/90#issuecomment-438120590  

> int main()  
> {  
>   shared_ptr<FooBase> foo;  
>   {  
>     auto ip = make_shared<int>(41);  
  
`ip` is local to this scope.  
  
>     auto a = foo_expr(ip);  
  
`a` is local to this scope, and contains a lvalue reference to `ip`, which is also local.  
  
>     foo = build_foo(a + 1);  
  
`foo` now contains an lvalue reference to `a`, and transitively to `ip`.  
  
>   }  
  
Now all references to `ip` and `a` are dangling.  
  
>   {  
>     run(foo); // wrong ip has been released  
>   }  
> ```  
  
The handling of expressions in YAP, is covered in the "How Expression Operands Are Treated" section of the docs:  
  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_yap/manual.html#boost_yap.manual.how_expression_operands_are_treated
