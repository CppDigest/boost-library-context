# #215 - Strange behavior of `skipped_token()` v.s. `found_directive()`. [Closed]

> Username: jwnhy  
> Created at: 2024-07-30 04:34:45 UTC  
> Updated at: 2024-08-01 23:43:27 UTC  
> Closed at: 2024-08-01 23:43:27 UTC  
> Url: https://github.com/boostorg/wave/issues/215  

Hello,  
  
```C  
#ifdef __LINE__  
  
#if 0  
int main() {  
  
}  
#endif  
  
#endif  
```  
  
I aim to capture each `#if/ifdef/ifndef` and `#endif` and pair them up.  
  
When I tried `skipped_token()`, I noticed this weird behavior.  
  
The first `#endif` is correctly captured by `skipped_token()` and **not** captured by `found_directive()`.  
However, the second `#endif` is captured **twice** by both `skipped_token()` and `found_directive()`.  
  
Technically speaking, I don't think the second `#endif` is skipped; it just does not generate any code, and most importantly, it is still "found" by `found_directive()`.  
  
I am not sure if this qualifies as a bug, but it is quite challenging for me to pair `#if/...` and `#endif` up...  
I wonder if it is possible to adjust the behavior of  `skipped_token()` to ignore the second `#endif`  
  
Thank you very much.

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-07-30 06:19:30 UTC  
> Url: https://github.com/boostorg/wave/issues/215#issuecomment-2257554974  

Would you mind posting your code? I think it would help me understand what you expect to happen.  
  
It looks like I had to solve this problem myself at one point and my approach was to implement a state machine that responded to "events" (Wave hooks) as they happened. Have a look at [my code here](https://github.com/jefftrull/clang_refactoring/blob/master/refactor_stage1.cpp); maybe that will be enough to get started.

---

## Comment 2

> Username: jwnhy  
> Created at: 2024-07-30 08:14:10 UTC  
> Url: https://github.com/boostorg/wave/issues/215#issuecomment-2257746650  

It's basically like this...  
Anyway, I hacked a little to ignore the repetitive `#endif` due to this issue...  
  
It seems to be working fine.  
  
```C++  
template <typename TokenT>  
class conditional_policy  
    : public wave::context_policies::eat_whitespace<TokenT> {  
private:  
  void stack_ctl(TokenT const &directive) {  
    if (token_id(directive) == boost::wave::T_PP_IF ||  
        token_id(directive) == boost::wave::T_PP_IFDEF ||  
        token_id(directive) == boost::wave::T_PP_IFNDEF) {  
      std::cerr << directive.get_value() << " @ " << directive.get_position()  
                << std::endl;  
      conditional_stack.push(directive);  
    } else if (token_id(directive) == boost::wave::T_PP_ENDIF) {  
      std::cerr << directive.get_value() << " @ " << directive.get_position()  
                << std::endl;  
      if (conditional_stack.empty()) {  
        std::cerr << "unmatched #endif" << std::endl;  
        return;  
      }  
      conditional_stack.pop();  
    }  
  }  
  
public:  
  template <typename ContextT, typename ContainerT>  
  bool evaluated_conditional_expression(ContextT const &ctx,  
                                        TokenT const &directive,  
                                        ContainerT const &expression,  
                                        bool expression_value) {  
    return false;  
  }  
  
  template <typename ContextT>  
  bool found_directive(ContextT const &ctx, TokenT const &directive) {  
    auto pos = directive.get_position();  
    auto pos_pair = std::make_pair(pos.get_line(), pos.get_column());  
    if (last_pos == pos_pair)  
      return false;  
    last_pos = pos_pair;  
    stack_ctl(directive);  
    return false;  
  }  
  
  template <typename ContextT>  
  void skipped_token(ContextT const &ctx, TokenT const &directive) {  
    auto pos = directive.get_position();  
    auto pos_pair = std::make_pair(pos.get_line(), pos.get_column());  
    if (last_pos == pos_pair)  
      return;  
    stack_ctl(directive);  
  }  
  std::stack<TokenT> conditional_stack;  
  std::pair<unsigned long, unsigned long> last_pos;  
};  
```

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-07-30 21:05:02 UTC  
> Url: https://github.com/boostorg/wave/issues/215#issuecomment-2259208012  

Actually it seems that `#endif` is always considered "skipped". Try using `#if 1` in your code to see. Perhaps that is because it never produces output.

---

## Comment 4

> Username: jefftrull  
> Created at: 2024-08-01 17:22:26 UTC  
> Url: https://github.com/boostorg/wave/issues/215#issuecomment-2263579351  

Anyway this seems to be working as intended (that is, `#endif` is always a skipped token) and:  
  
1. even if it's a little weird, users may be counting on this behavior  
2. you have a solution that works  
  
So I'm thinking closing this bug is appropriate.
