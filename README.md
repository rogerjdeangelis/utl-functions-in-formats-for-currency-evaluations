# utl-functions-in-formats-for-currency-evaluations
Functions in formats for currency evaluations

    SAS Forum: Functions in formats for currency evaluations                                        
                                                                                                    
    Note: Mexico can use the comma instead of a decimal point.                                      
                                                                                                    
    github                                                                                          
    https://tinyurl.com/y2p65aav                                                                    
    https://github.com/rogerjdeangelis/utl-functions-in-formats-for-currency-evaluations            
                                                                                                    
    SAS Forum                                                                                       
    https://communities.sas.com/t5/SAS-Programming/Currency-reading/m-p/580743                      
                                                                                                    
    *_                   _                                                                          
    (_)_ __  _ __  _   _| |_                                                                        
    | | '_ \| '_ \| | | | __|                                                                       
    | | | | | |_) | |_| | |_                                                                        
    |_|_| |_| .__/ \__,_|\__|                                                                       
            |_|                                                                                     
    ;                                                                                               
                                                                                                    
    data have ;                                                                                     
    informat empcode salary country $10.;                                                           
    input empcode salary country;                                                                   
    cards4;;                                                                                        
    1001 $1,473.33 us                                                                               
    1002 £1,560.00 uk                                                                               
    1003 M1560,55 Mexico                                                                            
    ;;;;                                                                                            
    run;quit;                                                                                       
                                                                                                    
    WORK.HAVE total obs=3                                                                           
                                                                                                    
      EMPCODE     SALARY      COUNTRY                                                               
                                                                                                    
       1001      $1,473.33    us                                                                    
       1002      £1,560.00    uk                                                                    
       1003      M1560,55     Mexico                                                                
                                                                                                    
    *            _               _                                                                  
      ___  _   _| |_ _ __  _   _| |_                                                                
     / _ \| | | | __| '_ \| | | | __|                                                               
    | (_) | |_| | |_| |_) | |_| | |_                                                                
     \___/ \__,_|\__| .__/ \__,_|\__|                                                               
                    |_|                                                                             
    ;                                                                                               
                                                                                                    
    WORK.WANT total obs=3                                                                           
                                                                                                    
      EMPCODE     SALARY      COUNTRY    CURRENCY                                                   
                                                                                                    
       1001      $1,473.33    us          1473.33                                                   
       1002      £1,560.00    uk          1560.00                                                   
       1003      M1560,55     Mexico      1560.55  Note: comma instead of decimal point             
                                                                                                    
                                                                                                    
    *          _       _   _                                                                        
     ___  ___ | |_   _| |_(_) ___  _ __                                                             
    / __|/ _ \| | | | | __| |/ _ \| '_ \                                                            
    \__ \ (_) | | |_| | |_| | (_) | | | |                                                           
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|                                                           
                                                                                                    
    ;                                                                                               
                                                                                                    
    proc fcmp outlib=work.functions.currency;                                                       
    function currency(txt $) $16;                                                                   
      select (substr(txt,1,1));                                                                     
        when ('$') amt=input(substr(txt,2),comma12.2);                                              
        when ('£') amt=input(substr(txt,2),comma12.2);                                              
        when ('M') amt=input(substr(txt,2),numx12.2);                                               
        otherwise amt=.;                                                                            
      end;                                                                                          
      return(amt);                                                                                  
    endsub;                                                                                         
    ;run;quit;                                                                                      
                                                                                                    
    %let cmplib = %sysfunc(getoption(cmplib));                                                      
    options cmplib = (work.functions &cmplib);                                                      
                                                                                                    
    proc format;                                                                                    
    invalue currency (default=10) other=[currency()];                                               
    run;                                                                                            
                                                                                                    
    data want ;                                                                                     
      set have ;                                                                                    
      currency=input(salary,currency.) ;                                                            
    run;quit;                                                                                       
                                                                                                    
                                                                                                    
