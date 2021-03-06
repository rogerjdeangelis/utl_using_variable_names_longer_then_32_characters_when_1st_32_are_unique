# utl_using_variable_names_longer_then_32_characters_when_1st_32_are_unique
Using variable names longer then 32 characters when 1st 32 are unique (sort of).  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Using variable names longer then 32 characters when 1st 32 are unique (sort of).

     My database has names longer than 32 characters and I would like to use those names in
     my SAS program and then just make a very small change to a macro to use
     the same code in my foreign database.

      TWO SOLUTIONS AND A COMMNET ON STATEMENT STYLE MACROS

          1. macro with positional parameter for
          2. slight adjustment to macro to create code for the databse
          3. Interesting statement style macro
             WPS WARNING: The IMPLMAC system option is not supported

          WPS gave the same result in 1 and 2.

          SOAPBOX ON
          Statement and command macros are barely supported by SAS?
          Mostly used with classic SAS?
          SOAPBOX OFF

    INPUT
    =====

     Database names  (35 character names)

          lattitude_of_los_angeles_california
          longitude_of_los_angeles_california


    PROCESS (using long names in SAS and database))
    ===============================================

       EXAMPLE CODE WITH 35 CHARACTER VARIABLES

      1. Running development code in SAS with truncated long names

         %macro _(nam) / des="substring long name";
              %substr(%str(&nam),1,32)
         %mend _;

         proc sql;
            create
               table want_sas as
            select
               x as     %_(longitude_of_los_angeles_california)
              ,y as     %_(lattitude_of_los_angeles_california)
              ,y**2  as %_(squared_lattitude_of_los_angeles_california)
            from
              sashelp.zipcode(obs=5 keep = x y statename zip)
         ;quit;


      2. Running code in database with 35 character names
         Note the change in the '_' macro.
         Should also setup for passthru.

         %macro _(nam) / des="substring long name";
              &nam   /* note no substring */
         %mend _;

         proc sql;
            create
               table want_database as
            select
               x as     %_(longitude_of_los_angeles_california)
              ,y as     %_(lattitude_of_los_angeles_california)
              ,y**2  as %_(squared_lattitude_of_los_angeles_california)
            from
              sashelp.zipcode(obs=5 keep = x y statename zip)
         ;quit;


      3. Interesting statement style macro

         Unfortunately statement and command macros can
         only gernerate a complete SAS statement.

         This does work

         options IMPLMAC=1 cmdmac=1;
         %macro asg(nam) / stmt des="substring long name";
             %substr(%str(&nam),1,32)=0;
         %mend asg;

         data want_stmt;
            asg longitude_of_los_angeles_california;
         run;quit;

         options IMPLMAC=0 cmdmac=0;  ** important;


    OUTPUT
    ======

    1. Running development code in SAS with truncated long names

    Middle Observation(2 ) of WANT_SAS - Total Obs 5

     -- NUMERIC --                     TYPE   VALUE

    LONGITUDE_OF_LOS_ANGELES_CALIFOR    N8    -73.049288
    LATTITUDE_OF_LOS_ANGELES_CALIFOR    N8    40.813223
    SQUARED_LATTITUDE_OF_LOS_ANGELES    N8    1665.7191716


    3. Interesting statement style macro

    Middle Observation(1 ) of want_stmt - Total Obs 1

     -- NUMERIC --
    LONGITUDE_OF_LOS_ANGELES_CALIFOR    N8    0


    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

      * just use sashelp.zipcode

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    for SAS see process;

    * WPS;
    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    libname hlp  sas7bdat "C:\Progra~1\SASHome\SASFoundation\9.4\core\sashelp";
    %macro _(nam) / des="substring long name";
         %substr(%str(&nam),1,32)
    %mend _;
    proc sql;
       create
          table want_wps as
       select
          x as     %_(longitude_of_los_angeles_california)
         ,y as     %_(lattitude_of_los_angeles_california)
         ,y**2  as %_(squared_lattitude_of_los_angeles_california)
       from
         hlp.zipcode(obs=5 keep = x y statename zip)
    ;quit;
    run;quit;
    proc print;
    run;quit;
    ');


    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    libname hlp  sas7bdat "C:\Progra~1\SASHome\SASFoundation\9.4\core\sashelp";
    options IMPLMAC=1 cmdmac=1;
    %macro asg(nam) / stmt des="substring long name";
        %substr(%str(&nam),1,32)=0;
    %mend asg;

    data want_stmt;
       asg longitude_of_los_angeles_california;
    run;quit;

    options IMPLMAC=0 cmdmac=0;  ** important;
    run;quit;
    proc print;
    run;quit;
    ');

