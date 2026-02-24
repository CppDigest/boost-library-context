# #100 - parse_config_file: alternate comment interpretation [Open]

> Username: xloem  
> Created at: 2020-10-23 21:38:00 UTC  
> Updated at: 2020-10-23 21:48:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/100  

For some time `parse_config_file` treats all `#` signs as initiating comments, preventing the use of `#` signs in values.  
  
It would be very helpful if a simple flag could be set, to only consider # signs in column 0, or preceded only by whitespace, as comments.  This would make for clearer, cleaner code and configuration files, when configuration values contain `#`.  
  
Here's an example of somebody looking for this, and being advised to write an iostreams filter to make it work:  
https://stackoverflow.com/questions/31921241/boostprogram-options-how-to-support-hash-character-in-a-value  
  
Here's an example of brute-force key generation implemented in a production release, to make sure the key never contains the `#` symbol and can be stored in the configuration file:  
https://github.com/libbitcoin/libbitcoin-protocol/blob/version3/src/zmq/certificate.cpp#L78

---

## Comment 1

> Username: vprus  
> Created at: 2020-10-23 21:48:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/100#issuecomment-715605425  

Thanks for the suggestion. I would be open to a PR implementing such option.  
  
On Sat, 24 Oct 2020, 00:38 xloem, <notifications@github.com> wrote:  
  
> For some time parse_config_file treats all # signs as initiating  
> comments, preventing the use of # signs in values.  
>  
> It would be very helpful if a simple flag could be set, to only consider #  
> signs in column 0, or preceded only by whitespace, as comments. This would  
> make for clearer, cleaner code and configuration files, when configuration  
> values contain #.  
>  
> Here's an example of somebody looking for this, and being advised to write  
> an iostreams filter to make it work:  
>  
> https://stackoverflow.com/questions/31921241/boostprogram-options-how-to-support-hash-character-in-a-value  
>  
> Here's an example of brute-force key generation implemented in a  
> production release, to make sure the key never contains the # symbol and  
> can be stored in the configuration file:  
>  
> https://github.com/libbitcoin/libbitcoin-protocol/blob/version3/src/zmq/certificate.cpp#L78  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/issues/100>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AABXE6526XFTAMRGFGPNF7TSMHZUJANCNFSM4S5CBDFA>  
> .  
>
