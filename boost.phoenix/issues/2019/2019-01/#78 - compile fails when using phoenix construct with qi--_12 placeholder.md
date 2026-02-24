# #78 - compile fails when using phoenix construct with qi::_12 placeholder. [Closed]

> Username: bigfatwhale  
> Created at: 2019-01-10 18:50:41 UTC  
> Updated at: 2019-01-15 08:02:37 UTC  
> Closed at: 2019-01-15 08:02:36 UTC  
> Url: https://github.com/boostorg/phoenix/issues/78  

The following code fails the moment we have more than qi::_12,   
  
```  
#define BOOST_PHOENIX_LIMIT 30  
#define SPIRIT_ARGUMENTS_LIMIT 30  
  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/include/phoenix.hpp>  
#include <ctime>  
#include <chrono>  
#include <string>  
#include <iomanip>  
  
namespace qi = boost::spirit::qi;  
namespace phi = boost::phoenix;  
using namespace std::chrono_literals;  
using namespace qi::labels;  
  
using It = std::string::const_iterator;  
  
#define PRICE_MULT 10000  
  
class ImbalanceMsg  
{  
  
public:  
    ImbalanceMsg(){}  
  
    ImbalanceMsg(timespec ts,  
                 uint8_t msgtype,  
                 uint64_t seq_num,  
                 std::string symbol,  
                 uint64_t symbol_seqnum,  
                 uint64_t ref_price,  
                 uint32_t paired_qty,  
                 uint32_t total_imb_qty,  
                 uint32_t mkt_imb_qty,  
                 uint32_t auction_time,  
                 char     auction_type,  
                 char     imb_side,  
                 uint64_t cont_clear_price,  
                 uint64_t auction_int_clear_price,  
                 uint64_t ssr_filling_price,  
                 uint64_t ind_match_price,  
                 uint64_t upper_collar,  
                 uint64_t lower_collar,  
                 uint32_t auction_status,  
                 uint32_t freeze_status,  
                 uint32_t num_ext  
    ) :  
  
            m_ref_price{ref_price},  
            m_paired_qty{paired_qty},  
            m_total_imb_qty{total_imb_qty},  
            m_mkt_imb_qty{mkt_imb_qty},  
            m_auction_time{auction_time},  
            m_auction_type{auction_type},  
            m_imb_side{imb_side},  
            m_cont_clear_price{cont_clear_price},  
            m_auction_int_clear_price{auction_int_clear_price},  
            m_ssr_filling_price{ssr_filling_price},  
            m_ind_match_price{ind_match_price},  
            m_upper_collar{upper_collar},  
            m_lower_collar{lower_collar},  
            m_auction_status{auction_status},  
            m_freeze_status{freeze_status},  
            m_num_ext{num_ext}  
    {}  
  
    // auto msg = parse( "105,42982201,15:00:05.553620224,AAPL,1192101,157.56,0,200,0,1600,C,S,0,0,0,157.57,159140000,155980000,0,0,0" );  
  
  
    std::string m_symbol;  
    uint64_t m_symbol_seqnum;  
    uint64_t m_ref_price;  
    uint32_t m_paired_qty;  
    uint32_t m_total_imb_qty;  
    uint32_t m_mkt_imb_qty;  
    uint32_t m_auction_time;  
    char     m_auction_type;  
    char     m_imb_side;  
    uint64_t m_cont_clear_price;  
    uint64_t m_auction_int_clear_price;  
    uint64_t m_ssr_filling_price;  
    uint64_t m_ind_match_price;  
    uint64_t m_upper_collar;  
    uint64_t m_lower_collar;  
    uint32_t  m_auction_status;  
    uint32_t  m_freeze_status;  
    uint32_t m_num_ext;  
  
};  
  
  
  
  
int main() {  
  
    std::string s = "AAPL,1192101,157.56,0,200,0,1600,C,S,0,0,0,157.57,159140000,155980000,0,0,0";  
  
    timespec ts;  
    uint8_t msgtype = 105;  
    uint64_t seq_num = 42982201;  
  
    qi::uint_parser<uint32_t, 10, 1, 6> int_part;  
    qi::uint_parser<uint8_t , 10, 1, 1> m_digit;  
  
    qi::rule<std::string::iterator, uint64_t()>  
        m_fixed_point = int_part[qi::_val =  qi::_1 * PRICE_MULT] >>  
              -("." >> -(m_digit[qi::_val += qi::_1 * 1000])  
                    >> -(m_digit[qi::_val += qi::_1 * 100])  
                    >> -(m_digit[qi::_val += qi::_1 * 10])  
                    >> -(m_digit[qi::_val += qi::_1 ])  
              );  
  
    qi::rule<std::string::iterator, ImbalanceMsg()>  
        m_wire_msg = ( qi::as_string[*qi::alpha]   >> "," // symbol  
                                                   >> qi::ulong_    >> "," // symbol seq num  
                                                   >> m_fixed_point >> "," // ref price  
                                                   >> qi::uint_     >> "," // paired_qty  
                                                   >> qi::uint_     >> "," // total_imb_qty  
                                                   >> qi::uint_     >> "," // mkt_imb_qty  
                                                   >> qi::uint_     >> "," // auction_time  
                                                   >> qi::char_     >> "," // auction type  
                                                   >> qi::char_     >> "," // imb side  
                                                   >> m_fixed_point >> "," // cont_clear_price  
                                                   >> m_fixed_point >> "," // auction_int_clear_price  
                                                   >> m_fixed_point >> "," // ssr_filling_price  
                                                   >> m_fixed_point >> "," // ind_match_price  
                                                   >> m_fixed_point >> "," // upper_collar  
                                                   >> m_fixed_point >> "," // lower_collar  
                                                   >> qi::ushort_   >> "," // auction status  
                                                   >> qi::ushort_   >> "," // freeze status  
                                                   >> qi::ushort_  
         )[qi::_val = phi::construct<ImbalanceMsg>(ts, msgtype, seq_num,  
                                                      qi::_1, //symbol  
                                                      qi::_2, //market_id  
                                                      qi::_3, //system_id  
                                                      qi::_4, //exchange_code  
                                                      qi::_5, //security_type  
                                                      qi::_6, //lot_size  
                                                      qi::_7, // prev_close_price  
                                                      qi::_8, // prev_close_volume  
                                                      qi::_9, // price_resolution  
                                                      qi::_10, // round_lot  
                                                      qi::_11, // mpv  
                                                      qi::_12,  
                                                      qi::_13,  
                                                      qi::_14,  
                                                      qi::_15,  
                                                      qi::_16,  
                                                      qi::_17,  
                                                      qi::_18  
    )];  
  
    ImbalanceMsg m;  
    bool ok = parse( s.begin(), s.end(), m_wire_msg, m );  
    std::cout << "ok=" << ok << std::endl;  
  
  
}  
```

---

## Comment 1

> Username: Flast  
> Created at: 2019-01-15 08:02:36 UTC  
> Url: https://github.com/boostorg/phoenix/issues/78#issuecomment-454300011  

This is limitation by `boost::result_of`. Define `#define BOOST_RESULT_OF_NUM_ARGS 30` and it should work.  
  
boostorg/utility#23
