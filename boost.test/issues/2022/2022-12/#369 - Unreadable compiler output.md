# #369 - Unreadable compiler output [Open]

> Username: ugly-dean  
> Created at: 2022-12-30 15:20:51 UTC  
> Updated at: 2022-12-30 15:30:55 UTC  
> Url: https://github.com/boostorg/test/issues/369  

Hey!  
There is quite unreadable output of test errors. I mean that line where talked about **"last checkpoint"** sticks together with output of next test suite:  
```  
Running 5 test cases...  
unknown location(0): fatal error: in "DbAccess_AccountMappingTests/DatabaseMapping_Account": Wt::Dbo::backend::PostgresException: ERROR:  new row for relation "accounts" violates check constraint "is_retail_for_only_client_account"  
DETAIL:  Failing row contains (19, 0, 2022-12-30 15:25:50.432222, nccName321, 0, 1000000.00000000, 1, 1111, ALFA, 0, 1, null, null, 1, t, 0, t, 0.1, null, f, , , null, f, f, 0, 1000000.00000000, f, f, , , , 1000000.00000000, 0, f, 1, null, f, , f, f, 2, 0, 0, 1, f, f, f, , t, 6636, 0, t, PrincipalFirmId, PrincipalTradeClearingAccountId, PrincipalFirmId, ClientTrustedCode, null, 1000000.00000000, 0, f, t, t, , 0.00000000, 0, f, f).  
  
/home/dmitry-trebushnikov/main/Sources/Trading/src/TradingTestUtils.cpp(847): last checkpoint  
Dbo.Session: Session exiting with 1 dirty objects  
unknown location(0): fatal error: in "DbAccess_AccountMappingTests/DatabaseMapping_AccountWithCurrencies_FromDbCache": Wt::Dbo::backend::PostgresException: ERROR:  duplicate key value violates unique constraint "accounts_name_and_owner_firm_id_key"  
DETAIL:  Key (name, owner_firm_id)=(name222, 1) already exists.  
  
/home/dmitry-trebushnikov/main/Sources/Trading/src/DbAccess/DatabaseTestFixture.cpp(187): last checkpoint  
```  
I think it is much more consistent to stick it with its test suite output and separate from next:  
```  
Running 5 test cases...  
unknown location(0): fatal error: in "DbAccess_AccountMappingTests/DatabaseMapping_Account": Wt::Dbo::backend::PostgresException: ERROR:  new row for relation "accounts" violates check constraint "is_retail_for_only_client_account"  
DETAIL:  Failing row contains (19, 0, 2022-12-30 15:28:29.370916, nccName321, 0, 1000000.00000000, 1, 1111, ALFA, 0, 1, null, null, 1, t, 0, t, 0.1, null, f, , , null, f, f, 0, 1000000.00000000, f, f, , , , 1000000.00000000, 0, f, 1, null, f, , f, f, 2, 0, 0, 1, f, f, f, , t, 6636, 0, t, PrincipalFirmId, PrincipalTradeClearingAccountId, PrincipalFirmId, ClientTrustedCode, null, 1000000.00000000, 0, f, t, t, , 0.00000000, 0, f, f).  
/home/dmitry-trebushnikov/main/Sources/Trading/src/TradingTestUtils.cpp(847): last checkpoint  
  
Dbo.Session: Session exiting with 1 dirty objects  
unknown location(0): fatal error: in "DbAccess_AccountMappingTests/DatabaseMapping_AccountWithCurrencies_FromDbCache": Wt::Dbo::backend::PostgresException: ERROR:  duplicate key value violates unique constraint "accounts_name_and_owner_firm_id_key"  
DETAIL:  Key (name, owner_firm_id)=(name222, 1) already exists.  
/home/dmitry-trebushnikov/main/Sources/Trading/src/DbAccess/DatabaseTestFixture.cpp(187): last checkpoint  
```  
All you have to do is change ```impl/compiler_log_formatter.ipp``` (line 155):  
```  
    if( !checkpoint_data.m_file_name.is_empty() ) {  
        // output << '\n';                                                             // it was so  
        print_prefix( output, checkpoint_data.m_file_name, checkpoint_data.m_line_num );  
  
        BOOST_TEST_SCOPE_SETCOLOR( m_color_output, output, term_attr::BRIGHT, term_color::CYAN );  
  
        output << "last checkpoint";  
        if( !checkpoint_data.m_message.empty() )  
            output << ": " << checkpoint_data.m_message;  
        output << '\n';                                                                 // now it is  
    }  
```  
Have a good day:)
