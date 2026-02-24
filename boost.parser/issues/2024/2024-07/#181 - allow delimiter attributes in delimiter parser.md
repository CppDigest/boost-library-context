# #181 - allow delimiter attributes in delimiter parser [Closed]

> Username: cppljevans  
> Created at: 2024-07-13 17:09:15 UTC  
> Updated at: 2024-09-30 04:56:54 UTC  
> Closed at: 2024-09-29 21:06:51 UTC  
> Url: https://github.com/boostorg/parser/issues/181  

Changing parser.hpp to contain:  
  
```  #define DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
  #ifdef DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
    template<typename... Parsers>  
    using seq_parser_default=  
      seq_parser  
      < tuple  
        < Parsers...  
        >  
      , tuple  
        < std::true_type  
        , std::true_type  
        >  
      , tuple  
        < llong<0>  
        , llong<0>  
        >  
      >  
      ;  
  #endif//DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
    template<typename Parser, typename DelimiterParser>  
    struct delimited_seq_parser  
  #ifdef DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
    : seq_parser_default  
      < Parser  
      , zero_plus_parser  
        < seq_parser_default  
          < DelimiterParser  
          , Parser  
          >  
        >  
      >  
  #else  
    : repeat_parser<Parser, DelimiterParser>  
  #endif//DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
    {  
      #ifdef DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
        using  repeater_alias=  
          zero_plus_parser  
          < seq_parser_default  
            < DelimiterParser  
            , Parser  
            >  
          >  
          ;  
        using  super=  
          seq_parser_default  
          < Parser  
          , repeater_alias  
          >  
          ;  
        constexpr delimited_seq_parser  
          ( Parser parser  
          , DelimiterParser delimiter_parser  
          )  
          : super  
            ( std::make_tuple  
              ( parser  
              , repeater_alias  
                ( seq_parser_default  
                  ( std::make_tuple  
                    ( delimiter_parser  
                    , parser  
                    )//make_tuple  
                  )//seq_parser_default  
                )//repeater_alias  
              )//make_tuple  
            )//super  
          {}  
      #else  
        constexpr delimited_seq_parser(  
            Parser parser, DelimiterParser delimiter_parser) :  
            repeat_parser<Parser, DelimiterParser>(  
                parser, 1, Inf, delimiter_parser)  
        {}  
      #endif//DELIMITED_SEQ_PARSER_USE_DELMITER_ATTR  
    };//delimited_seq_parser struct  
  
```  
would allow the delimited_seq_parser attributes to contain the attribute of the DelimiterParser.  If that attribute is nope, then attribute remains the same.  For example, if DelimiterParser attribute is some binary operator, such as std::multiplies<int,int>, then the attribute would contain an easy means for interpretation of the attribute to return an int result.   
  
The std::multiplies<int,int> could easily be put into the attributes by:  
```  
    template  
    < char C  
    , typename AttributeType=detail::nope  
    >  
    struct char_const_parser  
    : char_parser  
      < char  
      , void  
      >  
    {...};        
  
     template  
      < char C  
      , typename AttributeType=detail::nope  
      >  
      const   
      parser_interface  
      < char_const_parser  
        < C  
        , AttributeType  
        >  
      >   
    char_const;  
  
```  
where AttributeType == std::multiplies<int,int>.  
  
This could then be put into a calculator grammar containing, for example:  
```  
      auto const  
    op_mult  
      = bp::char_const  
        < '*'  
        , std::multiplies<atom_attr>  
        >  
      ;  
      auto const   
    term_gram  
      = factor % op_mult  
      ;  
```  
  
In addition, the documentation of the operator% [here](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/cheat_sheet.html) actually claims the equivalence of:  
```  
p1 % p2  
```  
to:  
```  
p1 >> *(p2 >> p1)  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-29 21:06:51 UTC  
> Url: https://github.com/boostorg/parser/issues/181#issuecomment-2381603230  

The fact that the delimiter is just markup, and has no attribute, is by design.  If you want another sequence that contains attribute-bearing parser, where some just happen to be between others, you can make that.  
  
The idea that we should have callables as attributes is not unreasonable -- any regular type will do.  But the parser should not invoke such callables automatically.  That's going to far in the direction of mixing the parsing with the semantic actions/business logic of your program.  
  
In the example above, I might do something like `p % '*'` for my parser, then have a semantic action that folded the values in the resulting vector using `operator*`.

---

## Comment 2

> Username: cppljevans  
> Created at: 2024-09-30 01:10:11 UTC  
> Updated at: 2024-09-30 01:22:38 UTC  
> Url: https://github.com/boostorg/parser/issues/181#issuecomment-2381818118  

No, it is not "just" markup.  As I tried to explain, the delimiter could be operators, such as + or *, and those are not markup.  Why design the % to ignore such operators when they obviously have semantic significance?  If the delimiter as an empty attribute, then no "callable" will be invoked, but if it does, then it will.  Where's the harm in that?  Your remark "that's going too far ... in mixing parsing with semantic actions" makes no sense because parsing is mixing semantic actions with the semantic action p[a].

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-09-30 04:50:11 UTC  
> Url: https://github.com/boostorg/parser/issues/181#issuecomment-2382040132  

You're missing my point, I think.  My point is that, yes, your thing works that way.  There's nothing wrong with that semantic.  It's just that the existing thing, `operator%`, doesn't work that way.  There's room for both, but that does not suggest that we should change `operator%`.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-09-30 04:51:04 UTC  
> Url: https://github.com/boostorg/parser/issues/181#issuecomment-2382044992  

I should add -- in particular, I think the current `operator%` is the more common case.  I say this having used it in Spirit 2 and Spirit X3 for most of the last two decades.

---

## Comment 5

> Username: cppljevans  
> Created at: 2024-09-30 04:56:53 UTC  
> Url: https://github.com/boostorg/parser/issues/181#issuecomment-2382071268  

> You're missing my point, I think. My point is that, yes, your thing works that way. There's nothing wrong with that semantic. It's just that the existing thing, `operator%`, doesn't work that way. There's room for both, but that does not suggest that we should change `operator%`.  
  
Why not, it simplifies the grammar.  Instead of operand_ >> *(operator_ >> operand_), the parser expression can be simplified to   
operand_ % operator_.  What is the downside? After all the existing operand_ % ',' does the "implicit" semantic action of concatenating the operand_'s into a vector.
