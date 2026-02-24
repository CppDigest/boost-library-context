# #179 - size-based rotation keeps growing early logs on restart [Closed]

> Username: sehe  
> Created at: 2022-02-24 01:24:28 UTC  
> Updated at: 2022-02-26 21:13:35 UTC  
> Closed at: 2022-02-26 21:13:10 UTC  
> Url: https://github.com/boostorg/log/issues/179  

By request, reproducer adapted from https://stackoverflow.com/a/71220978/85371  
  
A minimal size-based rotation textfile sink:  
  
```c++  
#include <boost/log/core.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/file.hpp>  
  
namespace logging = boost::log;  
namespace keywords = boost::log::keywords;  
  
void init_logging()  
{  
    logging::register_simple_formatter_factory<logging::trivial::severity_level, char>("Severity");  
  
    auto file = logging::add_file_log(  
        keywords::file_name        = "sample_%N.log",    //  
        keywords::target           = "./",               //  
        keywords::target_file_name = "sample_%N.log",    //  
        keywords::open_mode        = std::ios_base::app, //  
        keywords::auto_flush       = true,               //  
        keywords::rotation_size    = 1024,               //  
        keywords::max_size         = 300 * 1024,         //  
        keywords::format =  
            "%LineID%. [%TimeStamp%][%ThreadID%] [%Severity%] - %Message%");  
  
    file->locked_backend()->scan_for_files();  
  
    logging::core::get()->set_filter(logging::trivial::severity >= logging::trivial::info);  
  
    logging::add_common_attributes();  
}  
  
int main()  
{  
    init_logging();  
  
    BOOST_LOG_TRIVIAL(trace)   << "This is a  trace         severity message";  
    BOOST_LOG_TRIVIAL(debug)   << "This is a  debug         severity message";  
    BOOST_LOG_TRIVIAL(info)    << "This is an informational severity message";  
    BOOST_LOG_TRIVIAL(warning) << "This is a  warning       severity message";  
    BOOST_LOG_TRIVIAL(error)   << "This is an error         severity message";  
    BOOST_LOG_TRIVIAL(fatal)   << "This is a  fatal         severity message";  
}  
```  
  
When run a few times will create `sample_0.log`...`sample_3.log` but never more. That's because the first message **always** goes to `sample_0.log` even though that had already been rotated, the second to `sample_1.log` even after it, too, has been rotated etc. There are only 4 messages logged per invocation, so nothing ever reaches `sample_4.log`. The upshot is that after 100 invocations, the sizes of all the logs are multiples of the rotation size, especially the first file.  
  
Also, log messages are scattered out of chronological order across several files.  
  
In a comment @Lastique said  
  
> @sehe File rotation only happens when a log record reaches the sink backend, this is expected. Rotation happens as soon as the rotation criteria is met, which is checked before the record is written. I remember there was a bug in this regard, and it was fixed a while ago  
  
Is there still a bug, or is there some other setup issue/edge condition at play?  
  
------  
  
Sample output:  
  
```bash  
rm -fv sample_*.log  
for a in {1..100}; do ./sotest; done ; nl sample_0.log ; du --apparent-size -kc sample*.log | xargs; tail -n 1 sample*.log  
```  
  
Prints **[Live On Coliru](http://coliru.stacked-crooked.com/a/fca07e55db072053)**  
  
```  
     1	1. [2022-02-24 02:08:26.654872][0x00007fc689fd6740] [info] - This is an informational severity message  
     2	2. [2022-02-24 02:08:26.655027][0x00007fc689fd6740] [warning] - This is a  warning       severity message  
     3	3. [2022-02-24 02:08:26.655044][0x00007fc689fd6740] [error] - This is an error         severity message  
     4	4. [2022-02-24 02:08:26.655057][0x00007fc689fd6740] [fatal] - This is a  fatal         severity message  
     5	1. [2022-02-24 02:08:26.662348][0x00007f3bcaeb8740] [info] - This is an informational severity message  
     6	2. [2022-02-24 02:08:26.662489][0x00007f3bcaeb8740] [warning] - This is a  warning       severity message  
     7	3. [2022-02-24 02:08:26.662506][0x00007f3bcaeb8740] [error] - This is an error         severity message  
     8	4. [2022-02-24 02:08:26.662518][0x00007f3bcaeb8740] [fatal] - This is a  fatal         severity message  
     9	1. [2022-02-24 02:08:26.669411][0x00007f372007e740] [info] - This is an informational severity message  
    10	1. [2022-02-24 02:08:26.677186][0x00007f525429e740] [info] - This is an informational severity message  
    11	1. [2022-02-24 02:08:26.684830][0x00007f25ec5a0740] [info] - This is an informational severity message  
    12	1. [2022-02-24 02:08:26.691614][0x00007f753ea3e740] [info] - This is an informational severity message  
    13	1. [2022-02-24 02:08:26.698558][0x00007ffa53bde740] [info] - This is an informational severity message  
    14	1. [2022-02-24 02:08:26.705352][0x00007fdd2891e740] [info] - This is an informational severity message  
    15	1. [2022-02-24 02:08:26.712528][0x00007f0e8368e740] [info] - This is an informational severity message  
    16	1. [2022-02-24 02:08:26.719807][0x00007f8004384740] [info] - This is an informational severity message  
    17	1. [2022-02-24 02:08:26.727490][0x00007f5f76b82740] [info] - This is an informational severity message  
    18	1. [2022-02-24 02:08:26.734783][0x00007fd8a02dd740] [info] - This is an informational severity message  
    19	1. [2022-02-24 02:08:26.745071][0x00007f14be1a6740] [info] - This is an informational severity message  
    20	1. [2022-02-24 02:08:26.752131][0x00007fce884b0740] [info] - This is an informational severity message  
    21	1. [2022-02-24 02:08:26.761897][0x00007fbbee12f740] [info] - This is an informational severity message  
    22	1. [2022-02-24 02:08:26.769207][0x00007fe60e686740] [info] - This is an informational severity message  
    23	1. [2022-02-24 02:08:26.779877][0x00007f8b383e7740] [info] - This is an informational severity message  
    24	1. [2022-02-24 02:08:26.787245][0x00007f6e1c8de740] [info] - This is an informational severity message  
    25	1. [2022-02-24 02:08:26.794336][0x00007f3b4411f740] [info] - This is an informational severity message  
    26	1. [2022-02-24 02:08:26.801553][0x00007eff2d813740] [info] - This is an informational severity message  
    27	1. [2022-02-24 02:08:26.808696][0x00007f216036b740] [info] - This is an informational severity message  
    28	1. [2022-02-24 02:08:26.815883][0x00007f5ba7091740] [info] - This is an informational severity message  
    29	1. [2022-02-24 02:08:26.823046][0x00007fdd8dd27740] [info] - This is an informational severity message  
    30	1. [2022-02-24 02:08:26.830548][0x00007f9e7bdbf740] [info] - This is an informational severity message  
    31	1. [2022-02-24 02:08:26.838080][0x00007ff26237c740] [info] - This is an informational severity message  
    32	1. [2022-02-24 02:08:26.845231][0x00007f64becb2740] [info] - This is an informational severity message  
    33	1. [2022-02-24 02:08:26.852194][0x00007f7c9b54b740] [info] - This is an informational severity message  
    34	1. [2022-02-24 02:08:26.859324][0x00007f1eb9d9f740] [info] - This is an informational severity message  
    35	1. [2022-02-24 02:08:26.866360][0x00007ff1a4625740] [info] - This is an informational severity message  
    36	1. [2022-02-24 02:08:26.873849][0x00007f14316e6740] [info] - This is an informational severity message  
    37	1. [2022-02-24 02:08:26.881244][0x00007f7731cc1740] [info] - This is an informational severity message  
    38	1. [2022-02-24 02:08:26.888647][0x00007f12fca27740] [info] - This is an informational severity message  
    39	1. [2022-02-24 02:08:26.895771][0x00007fbe5bf69740] [info] - This is an informational severity message  
    40	1. [2022-02-24 02:08:26.902864][0x00007f78bd125740] [info] - This is an informational severity message  
    41	1. [2022-02-24 02:08:26.909877][0x00007fdbf025a740] [info] - This is an informational severity message  
    42	1. [2022-02-24 02:08:26.916727][0x00007fd49f4c0740] [info] - This is an informational severity message  
    43	1. [2022-02-24 02:08:26.923639][0x00007f7cd3d03740] [info] - This is an informational severity message  
    44	1. [2022-02-24 02:08:26.930385][0x00007f1b30e97740] [info] - This is an informational severity message  
    45	1. [2022-02-24 02:08:26.937145][0x00007f61f9b4d740] [info] - This is an informational severity message  
    46	1. [2022-02-24 02:08:26.944075][0x00007fa456ddb740] [info] - This is an informational severity message  
    47	1. [2022-02-24 02:08:26.950968][0x00007facb953d740] [info] - This is an informational severity message  
    48	1. [2022-02-24 02:08:26.957908][0x00007fa09b823740] [info] - This is an informational severity message  
    49	1. [2022-02-24 02:08:26.964912][0x00007f4b597f5740] [info] - This is an informational severity message  
    50	1. [2022-02-24 02:08:26.971836][0x00007fdf466f4740] [info] - This is an informational severity message  
    51	1. [2022-02-24 02:08:26.978758][0x00007f25b5ef0740] [info] - This is an informational severity message  
    52	1. [2022-02-24 02:08:26.985609][0x00007f9e9956e740] [info] - This is an informational severity message  
    53	1. [2022-02-24 02:08:26.992617][0x00007fcedaa7a740] [info] - This is an informational severity message  
    54	1. [2022-02-24 02:08:27.000138][0x00007fa49db9d740] [info] - This is an informational severity message  
    55	1. [2022-02-24 02:08:27.008168][0x00007f97640ef740] [info] - This is an informational severity message  
    56	1. [2022-02-24 02:08:27.020046][0x00007ff5b662e740] [info] - This is an informational severity message  
    57	1. [2022-02-24 02:08:27.031269][0x00007fb6a0775740] [info] - This is an informational severity message  
    58	1. [2022-02-24 02:08:27.043476][0x00007f245bbf0740] [info] - This is an informational severity message  
    59	1. [2022-02-24 02:08:27.054183][0x00007f528a4bb740] [info] - This is an informational severity message  
    60	1. [2022-02-24 02:08:27.066100][0x00007f2163fe2740] [info] - This is an informational severity message  
    61	1. [2022-02-24 02:08:27.078182][0x00007ff874d34740] [info] - This is an informational severity message  
    62	1. [2022-02-24 02:08:27.085925][0x00007fbc7f0b6740] [info] - This is an informational severity message  
    63	1. [2022-02-24 02:08:27.093090][0x00007fde6cb9f740] [info] - This is an informational severity message  
    64	1. [2022-02-24 02:08:27.100273][0x00007fdf50162740] [info] - This is an informational severity message  
    65	1. [2022-02-24 02:08:27.112051][0x00007f92d416a740] [info] - This is an informational severity message  
    66	1. [2022-02-24 02:08:27.120971][0x00007fae19e51740] [info] - This is an informational severity message  
    67	1. [2022-02-24 02:08:27.131680][0x00007f6d37979740] [info] - This is an informational severity message  
    68	1. [2022-02-24 02:08:27.143306][0x00007f04bd7da740] [info] - This is an informational severity message  
    69	1. [2022-02-24 02:08:27.154915][0x00007fb3639bb740] [info] - This is an informational severity message  
    70	1. [2022-02-24 02:08:27.167549][0x00007f5d4ee76740] [info] - This is an informational severity message  
    71	1. [2022-02-24 02:08:27.178924][0x00007f60beac1740] [info] - This is an informational severity message  
    72	1. [2022-02-24 02:08:27.187232][0x00007f2990b04740] [info] - This is an informational severity message  
    73	1. [2022-02-24 02:08:27.198785][0x00007fbf69414740] [info] - This is an informational severity message  
    74	1. [2022-02-24 02:08:27.206062][0x00007f78b0f61740] [info] - This is an informational severity message  
    75	1. [2022-02-24 02:08:27.212891][0x00007fcbb9124740] [info] - This is an informational severity message  
    76	1. [2022-02-24 02:08:27.220320][0x00007f16fe3b9740] [info] - This is an informational severity message  
    77	1. [2022-02-24 02:08:27.228236][0x00007f716daba740] [info] - This is an informational severity message  
    78	1. [2022-02-24 02:08:27.236104][0x00007f8ee82d7740] [info] - This is an informational severity message  
    79	1. [2022-02-24 02:08:27.243832][0x00007f7d496df740] [info] - This is an informational severity message  
    80	1. [2022-02-24 02:08:27.250825][0x00007ff0c20f3740] [info] - This is an informational severity message  
    81	1. [2022-02-24 02:08:27.257761][0x00007ff8f96aa740] [info] - This is an informational severity message  
    82	1. [2022-02-24 02:08:27.266617][0x00007feebb80a740] [info] - This is an informational severity message  
    83	1. [2022-02-24 02:08:27.277814][0x00007f0bd30fb740] [info] - This is an informational severity message  
    84	1. [2022-02-24 02:08:27.286637][0x00007facb0b74740] [info] - This is an informational severity message  
    85	1. [2022-02-24 02:08:27.294902][0x00007f9b70287740] [info] - This is an informational severity message  
    86	1. [2022-02-24 02:08:27.302915][0x00007f5a20f2b740] [info] - This is an informational severity message  
    87	1. [2022-02-24 02:08:27.310397][0x00007ff8e640c740] [info] - This is an informational severity message  
    88	1. [2022-02-24 02:08:27.319014][0x00007f68cb476740] [info] - This is an informational severity message  
    89	1. [2022-02-24 02:08:27.326697][0x00007f6ed0e12740] [info] - This is an informational severity message  
    90	1. [2022-02-24 02:08:27.334601][0x00007f6582c71740] [info] - This is an informational severity message  
    91	1. [2022-02-24 02:08:27.342711][0x00007f4c92c01740] [info] - This is an informational severity message  
    92	1. [2022-02-24 02:08:27.353311][0x00007f1fed2f8740] [info] - This is an informational severity message  
    93	1. [2022-02-24 02:08:27.363221][0x00007ffaf591c740] [info] - This is an informational severity message  
    94	1. [2022-02-24 02:08:27.370908][0x00007fd5d68ec740] [info] - This is an informational severity message  
    95	1. [2022-02-24 02:08:27.379839][0x00007f96a5af9740] [info] - This is an informational severity message  
    96	1. [2022-02-24 02:08:27.388258][0x00007f5096431740] [info] - This is an informational severity message  
    97	1. [2022-02-24 02:08:27.395870][0x00007fc269297740] [info] - This is an informational severity message  
    98	1. [2022-02-24 02:08:27.403028][0x00007f8f30445740] [info] - This is an informational severity message  
    99	1. [2022-02-24 02:08:27.410006][0x00007f41da45d740] [info] - This is an informational severity message  
   100	1. [2022-02-24 02:08:27.417091][0x00007f75f58ba740] [info] - This is an informational severity message  
   101	1. [2022-02-24 02:08:27.424156][0x00007fdfa5baa740] [info] - This is an informational severity message  
   102	1. [2022-02-24 02:08:27.430900][0x00007ff5dc7d1740] [info] - This is an informational severity message  
   103	1. [2022-02-24 02:08:27.437497][0x00007f022a7dc740] [info] - This is an informational severity message  
   104	1. [2022-02-24 02:08:27.444347][0x00007fe81fc84740] [info] - This is an informational severity message  
   105	1. [2022-02-24 02:08:27.451169][0x00007fd9eec1b740] [info] - This is an informational severity message  
   106	1. [2022-02-24 02:08:27.457798][0x00007f5dac3a4740] [info] - This is an informational severity message  
11 sample_0.log 11 sample_1.log 11 sample_2.log 10 sample_3.log 41 total  
==> sample_0.log <==  
1. [2022-02-24 02:08:27.457798][0x00007f5dac3a4740] [info] - This is an informational severity message  
  
==> sample_1.log <==  
2. [2022-02-24 02:08:27.457878][0x00007f5dac3a4740] [warning] - This is a  warning       severity message  
  
==> sample_2.log <==  
3. [2022-02-24 02:08:27.457933][0x00007f5dac3a4740] [error] - This is an error         severity message  
  
==> sample_3.log <==  
4. [2022-02-24 02:08:27.457986][0x00007f5dac3a4740] [fatal] - This is a  fatal         severity message  
```  
  
Note that the line  
  
```  
11 sample_0.log 11 sample_1.log 11 sample_2.log 10 sample_3.log 41 total  
```  
  
Indicates that the files have grown to 41 kiB in total, A more live play by play:  
![test](https://user-images.githubusercontent.com/324097/155438462-57f6f09a-3e4a-46a6-95c1-a0db4a40deb2.gif)

---

## Comment 1

> Username: sehe  
> Created at: 2022-02-24 01:28:46 UTC  
> Url: https://github.com/boostorg/log/issues/179#issuecomment-1049403920  

PS Adding  
  
```c++  
            keywords::enable_final_rotation = false  
```  
  
Doesn't appear to help.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-02-26 12:21:05 UTC  
> Url: https://github.com/boostorg/log/issues/179#issuecomment-1052105689  

The problem is with the `scan_for_files` call. `add_file_log` already calls `scan_for_files` internally, if `target` is specified, which means the second explicit call doesn't find any new files. It still updates the internal file counter to 0, which makes the sink open `sample_0.log` on the first log record, and writes the record in that file. On the next record, the file is rotated and the counter is incremented, and then the next record is written in `sample_1.log`. And so on.  
  
The situation when the newly opened file after rotation exists, as well as any other situation when the target directory is modified by a third party, is not expected. The library makes no attempt to preserve that file or maintain its invariants, like adhering to the file size limit.  
  
The file counter can be fixed by not calling `scan_for_files` explicitly. Then each run of the application writes log records in a new file. The SO question poster wanted each run to append to the last written file, so that wouldn't work and is not implemented currently. I could probably add support for that, based on the `open_mode & ios_base::app` flag.  
  
I should also add a protection against multiple `scan_for_files` calls, as admittedly the zero counter value is not an expected outcome.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-02-26 21:13:35 UTC  
> Url: https://github.com/boostorg/log/issues/179#issuecomment-1052623458  

Should be implemented now, thanks for the report.
