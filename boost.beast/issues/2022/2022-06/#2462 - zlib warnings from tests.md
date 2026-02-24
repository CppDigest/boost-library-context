# #2462 - zlib warnings from tests [Closed]

> Username: vinniefalco  
> Created at: 2022-06-18 15:18:31 UTC  
> Updated at: 2024-01-03 15:42:05 UTC  
> Closed at: 2024-01-03 15:42:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2462  

The new zlib generates a bunch of warnings which didn't seem to be there before:  
  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\compress.c(23,11): warning C4131: 'compress2': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\compress.c(69,11): warning C4131: 'compress': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\compress.c(82,11): warning C4131: 'compressBound': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\uncompr.c(28,11): warning C4131: 'uncompress2': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\uncompr.c(87,11): warning C4131: 'uncompress': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(118,14): warning C4131: 'byte_swap': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\adler32.c(64,11): warning C4131: 'adler32_z': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\adler32.c(135,11): warning C4131: 'adler32': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\adler32.c(144,11): warning C4131: 'adler32_combine_': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\adler32.c(173,11): warning C4131: 'adler32_combine': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\adler32.c(181,11): warning C4131: 'adler32_combine64': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inffast.c(51,11): warning C4131: 'inflate_fast': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(106,11): warning C4131: 'inflateStateCheck': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(120,11): warning C4131: 'inflateResetKeep': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(146,11): warning C4131: 'inflateReset': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(159,11): warning C4131: 'inflateReset2': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(205,19): warning C4131: 'slide_hash': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\infback.c(29,11): warning C4131: 'inflateBackInit_': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(543,13): warning C4131: 'multmodp': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(197,11): warning C4131: 'inflateInit2_': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(232,15): warning C4131: 'deflateInit_': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(245,15): warning C4131: 'deflateInit2_': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\infback.c(83,26): warning C4131: 'fixedtables': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(241,11): warning C4131: 'inflateInit_': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(249,11): warning C4131: 'inflatePrime': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(280,26): warning C4131: 'fixedtables': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(567,15): warning C4131: 'x2nmodp': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(394,15): warning C4131: 'deflateStateCheck': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(718,14): warning C4131: 'crc_word': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(417,15): warning C4131: 'deflateSetDictionary': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(727,14): warning C4131: 'crc_word_big': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(740,19): warning C4131: 'crc32_z': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(486,15): warning C4131: 'deflateGetDictionary': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(508,15): warning C4131: 'deflateResetKeep': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(546,15): warning C4131: 'deflateReset': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(1064,19): warning C4131: 'crc32': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(558,15): warning C4131: 'deflateSetHeader': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(1073,11): warning C4131: 'crc32_combine64': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\infback.c(251,11): warning C4131: 'inflateBack': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(569,14): warning C4131: 'deflatePending': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(1085,11): warning C4131: 'crc32_combine': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(398,11): warning C4131: 'updatewindow': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(583,15): warning C4131: 'deflatePrime': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(1094,15): warning C4131: 'crc32_combine_gen64': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(1104,13): warning C4131: 'crc32_combine_gen': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(610,15): warning C4131: 'deflateParams': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(624,11): warning C4131: 'inflate': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\crc32.c(1111,11): warning C4131: 'crc32_combine_op': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(659,15): warning C4131: 'deflateTune': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(694,15): warning C4131: 'deflateBound': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(758,19): warning C4131: 'putShortMSB': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(772,15): warning C4131: 'flush_pending': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(805,15): warning C4131: 'deflate': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inftrees.c(33,10): warning C4131: 'inflate_table': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\infback.c(633,11): warning C4131: 'inflateBackEnd': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1120,15): warning C4131: 'deflateEnd': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(380,19): warning C4131: '_tr_init': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1302,11): warning C4131: 'inflateEnd': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1146,15): warning C4131: 'deflateCopy': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(408,19): warning C4131: 'init_block': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1316,11): warning C4131: 'inflateGetDictionary': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1339,11): warning C4131: 'inflateSetDictionary': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(452,19): warning C4131: 'pqdownheap': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1205,15): warning C4131: 'read_buf': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1374,11): warning C4131: 'inflateGetHeader': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(487,19): warning C4131: 'gen_bitlen': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1402,14): warning C4131: 'syncsearch': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1235,19): warning C4131: 'lm_init': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1425,11): warning C4131: 'inflateSync': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1277,19): warning C4131: 'longest_match': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(573,13): warning C4131: 'gen_codes': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1483,11): warning C4131: 'inflateSyncPoint': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1493,11): warning C4131: 'inflateCopy': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(616,19): warning C4131: 'build_tree': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1523,19): warning C4131: 'fill_window': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1540,11): warning C4131: 'inflateUndermine': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(704,19): warning C4131: 'scan_tree': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1535,29): warning C4127: conditional expression is constant  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1558,11): warning C4131: 'inflateValidate': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(724,45): warning C4244: '+=': conversion from 'int' to 'ush', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1573,11): warning C4131: 'inflateMark': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\inflate.c(1586,11): warning C4131: 'inflateCodesUsed': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(749,19): warning C4131: 'send_tree': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1686,19): warning C4131: 'deflate_stored': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1735,45): warning C4244: '=': conversion from 'unsigned int' to 'Bytef', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1736,50): warning C4244: '=': conversion from 'unsigned int' to 'Bytef', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1737,46): warning C4244: '=': conversion from 'unsigned int' to 'Bytef', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1738,51): warning C4244: '=': conversion from 'unsigned int' to 'Bytef', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(800,19): warning C4131: 'build_bl_tree': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(835,19): warning C4131: 'send_all_trees': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1873,19): warning C4131: 'deflate_fast': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(864,19): warning C4131: '_tr_stored_block': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1916,1): warning C4244: '=': conversion from 'ush' to 'uchf', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(888,19): warning C4131: '_tr_flush_bits': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(898,19): warning C4131: '_tr_align': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(1975,19): warning C4131: 'deflate_slow': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(913,19): warning C4131: '_tr_flush_block': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(2041,1): warning C4244: '=': conversion from 'ush' to 'uchf', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1016,19): warning C4131: '_tr_tally': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1020,37): warning C4244: '=': conversion from 'unsigned int' to 'uchf', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(2106,19): warning C4131: 'deflate_rle': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1021,42): warning C4244: '=': conversion from 'unsigned int' to 'uchf', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1022,35): warning C4244: '=': conversion from 'unsigned int' to 'uchf', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(2150,13): warning C4244: '=': conversion from 'ush' to 'uchf', possible loss of data  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1044,19): warning C4131: 'compress_block': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\deflate.c(2179,19): warning C4131: 'deflate_huff': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1104,19): warning C4131: 'detect_data_type': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1138,14): warning C4131: 'bi_reverse': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1153,19): warning C4131: 'bi_flush': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\trees.c(1170,19): warning C4131: 'bi_windup': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\zutil.c(134,9): warning C4131: 'zError': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\zutil.c(306,12): warning C4131: 'zcalloc': uses old-style declarator  
5>C:\Users\vinnie\src\boost\libs\beast\test\extern\zlib-1.2.12\zutil.c(316,12): warning C4131: 'zcfree': uses old-style declarator  
  
and many more.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-03 15:42:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2462#issuecomment-1875571036  

Addressed in https://github.com/boostorg/beast/commit/4f3276fd704009c0417fa1bca282f7f82acd3d1e.
