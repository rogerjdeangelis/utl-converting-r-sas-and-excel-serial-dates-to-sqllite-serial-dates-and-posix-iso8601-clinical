# utl-converting-r-sas-and-excel-serial-dates-to-sqllite-serial-dates-and-posix-iso8601-clinical
Converting r sas and excel days since reference dates to sqllite dates in posix iso8601 clinical formats
    %let pgm=utl-converting-r-sas-and-excel-serial-dates-to-sqllite-serial-dates-and-posix-iso8601-clinical;

    %stop_submission;

    Converting r sas and excel days since reference dates to sqllite dates in posix iso8601 clinical formats;

    github
    https://tinyurl.com/33etvmj2
    https://github.com/rogerjdeangelis/utl-converting-r-sas-and-excel-serial-dates-to-sqllite-serial-dates-and-posix-iso8601-clinical

    Please be critical and use at your own risk.

    If possible it might be easier to convert the numeric SAS, R and EXCEL dates to ISO8601 or POSIX text dates
    and import the text dates into ssqllite.

    Once you have the ISO8601 date SQLLITE can work directly with the text date in sqllite

       SELECT DATE('YYYY-MM-DD', '+1 MONTH);

       SOLUTIONS (SAS EXCEL R NUMERIC DATSE TO SQLLITE TEXT ISO8601 DATES)

          1 sas dates

          2 excel dates (use with caution - not sure if my solution is always right, because of excel error)
            Needs special processing to correct excel bug?

          3 r date (same base date as sqllite)

          4 python not included due to complexity?
            Pyhton adds special classes for dates
            All the 8 sql dialects I have used have a base dates

          5 add a months and days

       Here are the sqllite conversions for r, sas and excel dates to ISO8601 text

          strftime("%Y-%m-%d",(excel_date-25568)*86400,"unixepoch")
          strftime("%Y-%m-%d",(r_date*86400)          ,"unixepoch")
          strftime("%Y-%m-%d",(sas_date-3653)*86400   ,"unixepoch")

          SQLLITE USES ISO DATE DIRECTLY +MONTHS +YEARS +DAYS

             ISO8601 dates are very useful

             SELECT DATE('YYYY-MM-DD', '+1 YEAR');


    Sqllite uses datetimes and does not appear to work directly with serial days.
    So I mutiplied r, sas and excel dates by 86400 to get sqllite text dates
    86400 is the number of seconds in a day.

    SOAPBOX ON
      Dates are inherrently tricky
        1. Leap years
        2. Leap seconds
        3. Calendar changes (I think they most use the gregorian calendar 1582 pope gregory)
        4. sqllite default base date is November 24, 4714 BC is based on  Julian Calendar (carbon dateing?)
        4. sqllite with modifier unixepoc uses a base date of January 1, 1970:00:00 seconds (this is what I used-same in R)
        5. Errors in excel algorithm for dates
    SOAPBOX OFF

    BASE DATES (BIRTHDAYS EXCEPT PYTHON)
    ======================

    SAME BIRTDAYS (somtimes call base date or reference date)
    SQLLITE birthday January  01, 1970:00:00 seconds
    R birthday       January  01, 1970

    SAS birthday     January  01, 1960 days
    EXCEL birthday   December 31, 1899  days (dates prior to March 1, 1900 are off by 1 day)


    PYTHON DOES NOT HAVE A BASE DATE
    ================================

    It requires several special date classes.
    Less would be more?
    Sometimes additional classes require
    knowledge of syntax much different
    than syntax in any other language.
    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                    |                                                          |                        */
    /*            INPUT                   |   PROCESS                                                |       OUTPUT           */
    /*            =====                   |   =======                                                |       ======           */
    /*                                    |                                                          |                        */
    /*   TYP    DTE                       | SQLLITE has two base dates                               | R                      */
    /*                                    | for day 0 depending on the epoc                          |                        */
    /*   R     20066  (2024-12-09 R)      |                                                          |   TYP   DTE    ISO8601 */
    /*   EXCEL 45634  (2024-12-09 EXCEL)  | Defaultis to November 24, 4714 BC.                       |                        */
    /*   SAS   23719  (2024-12-09 SAS_    | When using modifier unixepoc                             | EXCEL 45634 2024-12-09 */
    /*                                    |   January  01, 1970:00:00 = 0 day                        |     R 20066 2024-12-09 */
    /*                                    |                                                          |   SAS 23719 2024-12-09 */
    /*                                    | SQLlite uses date and time                               |                        */
    /*                                    | and does not seem to use                                 |                        */
    /*                                    | days when manipulating dates.                            |                        */
    /*                                    |                                                          |                        */
    /*                                    |  my solutions use unixepoc                               |                        */
    /*                                    |                                                          |                        */
    /*                                    |  R DATE TO SQLLITE DATE                                  |                        */
    /*                                    |  =======================                                 |                        */
    /*                                    |                                                          |                        */
    /*                                    |  R and Sqllite have the same                             |                        */
    /*                                    |  timestamp for day 0                                     |                        */
    /*                                    |     "1970-01-01:00:00"                                   |                        */
    /*                                    |                                                          |                        */
    /*                                    |  So converting R time in days                            |                        */
    /*                                    |  since "1970-01-01' to ISO8601                           |                        */
    /*                                    |  text is simple:                                         |                        */
    /*                                    |                                                          |                        */
    /*                                    |  If R date is 20066                                      |                        */
    /*                                    |  (days since 1970-01-01 or 2024-12-09 )                  |                        */
    /*                                    |                                                          |                        */
    /*                                    |  To convert 20066 to ISO8601 text simply                 |                        */
    /*                                    |                                                          |                        */
    /*                                    |  strftime("%Y-%m-%d",(date*86400),"unixepoch")           |                        */
    /*                                    |                                                          |                        */
    /*                                    |  will return 2024-12-09                                  |                        */
    /*                                    |                                                          |                        */
    /*                                    |                                                          |                        */
    /*                                    |                                                          |                        */
    /*                                    |  SAS DATE TO SQLLITE DATE                                |                        */
    /*                                    |  =======================                                 |                        */
    /*                                    |                                                          |                        */
    /*                                    |  Tirst we compute the sqllite date that                  |                        */
    /*                                    |  corresponds to the SAS birthday (day 0)                 |                        */
    /*                                    |                                                          |                        */
    /*                                    |   we only need to do this once                           |                        */
    /*                                    |                                                          |                        */
    /*                                    |  julianday("1960-01-01") - julianday("1970-01-01")       |                        */
    /*                                    |                                                          |                        */
    /*                                    |  This yeilds -3653 days                                  |                        */
    /*                                    |                                                          |                        */
    /*                                    |  If we used the sas date directly in sqllite we would    |                        */
    /*                                    |  have 10 more years of days = 3653                       |                        */
    /*                                    |                                                          |                        */
    /*                                    |  So we need to subtract 3653 from dfatter sas dates      |                        */
    /*                                    |  to get sqllite dates.                                   |                        */
    /*                                    |                                                          |                        */
    /*                                    |  Because sqllite uses datetime multiply by 8600          |                        */
    /*                                    |                                                          |                        */
    /*                                    |  For sas date 23719(2024-12-09) then ISO8601 text is     |                        */
    /*                                    |                                                          |                        */
    /*                                    |  strftime("%Y-%m-%d",(date-3653)*86400 ,"unixepoch")     |                        */
    /*                                    |                                                          |                        */
    /*                                    |  2024-12-09                                              |                        */
    /*                                    |                                                          |                        */
    /*                                    |                                                          |                        */
    /*                                    |  SAS DATE TO SQLLITE DATE (beware of this)               |                        */
    /*                                    |  =========================================               |                        */
    /*                                    |                                                          |                        */
    /*                                    |  The offset for excel                                    |                        */
    /*                                    |                                                          |                        */
    /*                                    |  julianday("1899-12-31") - julianday("1970-01-01")       |                        */
    /*                                    |                                                          |                        */
    /*                                    |  -25568                                                  |                        */
    /*                                    |                                                          |                        */
    /*                                    |   If excel date is 45634                                 |                        */
    /*                                    |                                                          |                        */
    /*                                    |  strftime("%Y-%m-%d",(dte-25568)*86400,"unixepoch")      |                        */
    /*                                    |                                                          |                        */
    /*                                    |  yeilds 2024-12-09                                       |                        */
    /*                                    |                                                          |                        */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      input typ$ dte ;
      put dte yymmdd10.;
    cards4;
    EXCEL 45634
    R 20066
    SAS 23719
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* When opened in the appropriate application all dates are 2024-12009                                                    */
    /*                                                                                                                        */
    /*  TYP       DTE                                                                                                         */
    /*                                                                                                                        */
    /*  EXCEL    45634                                                                                                        */
    /*  R        20066                                                                                                        */
    /*  SAS      23719                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|                 _   _                        __  __          _
      ___  _ __   ___  | |_(_)_ __ ___   ___   ___  / _|/ _|___  ___| |_ ___
     / _ \| `_ \ / _ \ | __| | `_ ` _ \ / _ \ / _ \| |_| |_/ __|/ _ \ __/ __|
    | (_) | | | |  __/ | |_| | | | | | |  __/| (_) |  _|  _\__ \  __/ |_\__ \
     \___/|_| |_|\___|  \__|_|_| |_| |_|\___| \___/|_| |_| |___/\___|\__|___/

    */

    /*---- One time to detemine offsets for each application                      ----*/
    /*---- R does not require and offset SQLlite and R use the same base datetime ----*/

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want <- sqldf('
       select
         julianday("1960-01-01") - julianday("1970-01-01") as sas_offset
        ,julianday("1899-12-31") - julianday("1970-01-01") as excel_offset
      from
          have
      limit 1

       ')
    want
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* > want                                                                                                                 */
    /*                                                                                                                        */
    /*   sas_offset excel_offset                                                                                              */
    /*                                                                                                                        */
    /* 1      -3653       -25568                                                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*      __ _                     __  __          _
      __ _ / _| |_ ___ _ __    ___  / _|/ _|___  ___| |_ ___
     / _` | |_| __/ _ \ `__|  / _ \| |_| |_/ __|/ _ \ __/ __|
    | (_| |  _| ||  __/ |    | (_) |  _|  _\__ \  __/ |_\__ \
     \__,_|_|  \__\___|_|     \___/|_| |_| |___/\___|\__|___/

    */

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want <- sqldf('
       select
         typ
        ,dte
        ,case typ
          when "EXCEL" then strftime("%Y-%m-%d",(dte-25568)*86400,"unixepoch")
          when "R"     then strftime("%Y-%m-%d",(dte*86400)      ,"unixepoch")
          when "SAS"   then strftime("%Y-%m-%d",(dte-3653)*86400 ,"unixepoch")
        end as iso8601
      from
          have
       ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                            |                                                                                           */
    /*  R                         |    SAS                                                                                    */
    /* ==                         |    ===                                                                                    */
    /*                    SQLITE  |                                   SQLLITE                                                 */
    /*      TYP   DTE    iso8601  |    ROWNAMES    TYP       DTE      ISO8601                                                 */
    /*                            |                                                                                           */
    /*  1 EXCEL 45634 2024-12-09  |        1       EXCEL    45634    2024-12-09                                               */
    /*  2     R 20066 2024-12-09  |        2       R        20066    2024-12-09                                               */
    /*  3   SAS 23719 2024-12-09  |        3       SAS      23719    2024-12-09                                               */
    /*                            |                                                                                           */
    /**************************************************************************************************************************/

    /*         _     _                                 _   _
      __ _  __| | __| |   __ _   _ __ ___   ___  _ __ | |_| |__
     / _` |/ _` |/ _` |  / _` | | `_ ` _ \ / _ \| `_ \| __| `_ \
    | (_| | (_| | (_| | | (_| | | | | | | | (_) | | | | |_| | | |
     \__,_|\__,_|\__,_|  \__,_| |_| |_| |_|\___/|_| |_|\__|_| |_|

    */

    %utl_rbeginx;
    parmcards4;
    library(sqldf)
    want <- sqldf("
       SELECT
         '2024-12-09'                   as original_date
        ,DATE('2024-12-09', '+1 MONTH') as add_1_month
        ,DATE('2024-12-09', '+93 DAYS') as add_93_days
       ")
    want
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  > want                                                                                                                */
    /*                                                                                                                        */
    /*  original_date add_1_month add_93_days                                                                                 */
    /*                                                                                                                        */
    /*     2024-12-09  2025-01-09  2025-03-12                                                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
