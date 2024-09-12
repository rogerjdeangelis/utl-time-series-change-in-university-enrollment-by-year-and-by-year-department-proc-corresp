# utl-time-series-change-in-university-enrollment-by-year-and-by-year-department-proc-corresp
Calculating the enrollment change throughout the years for each University by Year and by year and department 
    %let pgm=utl-time-series-change-in-university-enrollment-by-year-and-by-year-department-proc-corresp;

    Calculating the enrollment change throughout the years for each University by Year and by year and department

    github
    https://tinyurl.com/khuchr7w
    https://github.com/rogerjdeangelis/utl-time-series-change-in-university-enrollment-by-year-and-by-year-department-proc-corresp

    PROCESS

     1.  Calculate the enrollment change for the sum of all
         departments by university and year

     2   Calculate the enrollment change by university,
         department and year

    stackoverflow r:
    https://stackoverflow.com/questions/78949934/yearly-rate-change-based-on-one-or-two-columns-in-r

           CONTENTS (I use views)

              1 change by university and year
              2 change by university, year and departments
              3 combine 1 & 2

    Other  solutions?

        1 proc summary (needs sorts, split and datastep?)
        2 DOW loops (reqires sorts?)
        3 change by university, year and departments cal be done with a sort and datastep
        4 can be dome sith sql arrays
        5 can be done with my numary macro and datsstep loops


    SOAPBOX ON

    Creating this output crosstab TABLE is more difficult in other languages (sort, transpose and sum at once)

    ods output observed=uni_year;
    proc corresp data=sd1.have observed dim=1;
      table uni, year;
      weight enrol;

     Table uni_yer

     LABEL    _2017    _2018    _2019     SUM

     uni1       730     1591     1072     3393
     uni2       990      462      926     2378
     uni3      2768     2618     2105     7491
     Sum       4488     4671     4103    13262

    proc report can do it, but you need to rename col1-col3 on output dataset;
    ie rename col1-col3=_2017 - _2019

    SOAPBOX OFF;

    Relates repos

    REPO
    ------------------------------------------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl-create-a-state-diagram-table-hash-corresp-and-transpose
    https://github.com/rogerjdeangelis/utl-creating-big-N-headers-in-your-reports-corresp-clinical-ods
    https://github.com/rogerjdeangelis/utl-crosstab-output-tables-from-corresp-report-not-static-tabulate
    https://github.com/rogerjdeangelis/utl-highlight-existing-cells-in-excel-sheet2-that-correspond-to-cells-in-sheet1-with-specified-value
    https://github.com/rogerjdeangelis/utl-minimum-code-for-a-crosstab-output_dataset-with-sums-using-proc-corresp
    https://github.com/rogerjdeangelis/utl-proc-corresp-sort-transpose-summary-combined-with-output-table
    https://github.com/rogerjdeangelis/utl-three-dimensional-crosstab-proc-freq-tabulate-corresp-and-report
    https://github.com/rogerjdeangelis/utl-transposing-sorting-and-summarizing-with-a-single-proc-corresp-freq-tabulate-and-report
    https://github.com/rogerjdeangelis/utl-use-freq-and-corresp-table-output-to-avoid-tabulate-static-printouts
    https://github.com/rogerjdeangelis/utl_compare_corresp_vs_report_output_datasets
    https://github.com/rogerjdeangelis/utl_proc_corresp_crosstab

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                                 |                                                                      */
    /*            INPUT                                |                                                                      */
    /*            ======                               |              PROCESS AND OUTPUT                                      */
    /*                                                 |              ===================                                     */
    /*                                                 |                                                                      */
    /*    SD1.HAVE total obs=30                        |                                                                      */
    /*                                                 |  Calculate the enrollment change for the total of all                */
    /*    Obs    YEAR    UNI     DEPT     ENROL        |  departments enrollment by university and year                       */
    /*                                                 |                                                                      */
    /*      1    2017    uni1    dept1      51         |     GRP              ENROL     CHANGE                                */                                                                       */
    /*      2    2017    uni1    dept2     322         |                                                                      */                                                                       */
    /*      3    2017    uni1    dept3     251         |   uni1 * 2017          730      .                                    */
    /*      4    2017    uni1    dept4     106         |   uni1 * 2018         1591     1.17945  1591/730-1                   */
    /*      5    2018    uni1    dept1     468         |   uni1 * 2019         1072    -0.32621  1072/1591-1                  */
    /*      6    2018    uni1    dept2     205         |                                                                      */
    /*      7    2018    uni1    dept3     718         |   uni2 * 2017          990      .                                    */
    /*      8    2018    uni1    dept4     200         |   uni2 * 2018          462    -0.53333  462/990-1                    */
    /*      9    2019    uni1    dept1     344         |   uni2 * 2019          926     1.00433                               */
    /*     10    2019    uni1    dept2     256         |                                                                      */
    /*     11    2019    uni1    dept3     434         |   uni3 * 2017         2768      .                                    */
    /*     12    2019    uni1    dept4      38         |   uni3 * 2018         2618    -0.05419                               */
    /*     13    2017    uni2    dept1     487         |   uni3 * 2019         2105    -0.19595                               */
    /*     14    2017    uni2    dept2     503         |   ...                                                                */
    /*     15    2018    uni2    dept1      14         |                                                                      */
    /*     16    2018    uni2    dept2     448         |  Calculate the enrollment chang by university                        */
    /*     17    2019    uni2    dept1     489         |  department and year                                                 */
    /*     18    2019    uni2    dept2     437         |                                                                      */
    /*     19    2017    uni3    dept1     695         |   uni1 * dept1 * 2017   51       .                                   */
    /*     20    2017    uni3    dept2     833         |   uni1 * dept1 * 2018  468      8.1765  468/51 -1                    */
    /*     21    2017    uni3    dept3     941         |   uni1 * dept1 * 2019  344     -0.2650  344/468 -1                   */
    /*     22    2017    uni3    dept4     299         |                                                                      */
    /*     23    2018    uni3    dept1     864         |   uni1 * dept2 * 2017  322       .                                   */
    /*     24    2018    uni3    dept2     888         |   uni1 * dept2 * 2018  205     -0.3634                               */
    /*     25    2018    uni3    dept3     531         |   uni1 * dept2 * 2019  256      0.2488                               */
    /*     26    2018    uni3    dept4     335         |                                                                      */
    /*     27    2019    uni3    dept1      47         |   uni1 * dept3 * 2017  251       .                                   */
    /*     28    2019    uni3    dept2     753         |   uni1 * dept3 * 2018  718      1.8606                               */
    /*     29    2019    uni3    dept3     319         |   uni1 * dept3 * 2019  434     -0.3955                               */
    /*     30    2019    uni3    dept4     986         |                                                                      */
    /*                                                 |   uni1 * dept4 * 2017  106       .                                   */
    /*                                                 |   uni1 * dept4 * 2018  200      0.8868                               */
    /*                                                 |   uni1 * dept4 * 2019   38     -0.8100                               */
    /*                                                 |                                                                      */
    /*                                                 |                                                                      */
    /*                                                 |                                                                      */
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
    input year Uni $ Dept $ Enrol ;
    cards4;
    2017 uni1 dept1   51
    2017 uni1 dept2  322
    2017 uni1 dept3  251
    2017 uni1 dept4  106
    2018 uni1 dept1  468
    2018 uni1 dept2  205
    2018 uni1 dept3  718
    2018 uni1 dept4  200
    2019 uni1 dept1  344
    2019 uni1 dept2  256
    2019 uni1 dept3  434
    2019 uni1 dept4   38
    2017 uni2 dept1  487
    2017 uni2 dept2  503
    2018 uni2 dept1   14
    2018 uni2 dept2  448
    2019 uni2 dept1  489
    2019 uni2 dept2  437
    2017 uni3 dept1  695
    2017 uni3 dept2  833
    2017 uni3 dept3  941
    2017 uni3 dept4  299
    2018 uni3 dept1  864
    2018 uni3 dept2  888
    2018 uni3 dept3  531
    2018 uni3 dept4  335
    2019 uni3 dept1   47
    2019 uni3 dept2  753
    2019 uni3 dept3  319
    2019 uni3 dept4  986
    ;;;;
    run;quit;
    /*   _                         _                   _
    / | | |__  _   _   _   _ _ __ (_)   __ _ _ __   __| |  _   _  ___  __ _ _ __
    | | | `_ \| | | | | | | | `_ \| |  / _` | `_ \ / _` | | | | |/ _ \/ _` | `__|
    | | | |_) | |_| | | |_| | | | | | | (_| | | | | (_| | | |_| |  __/ (_| | |
    |_| |_.__/ \__, |  \__,_|_| |_|_|  \__,_|_| |_|\__,_|  \__, |\___|\__,_|_|
               |___/                                       |___/
    */
    * Creating this output crosstab table is no so simple in other languages (sort, transpose and sum at once)
    ods output observed=uni_year(drop=sum);
    proc corresp data=sd1.have observed dim=1;
      table uni, year;
      weight enrol;
    run;quit;

    * MOR GENERAL AND TRANSFERABLE TO OTHER LANGUAGES?;
    data uniyer/view=uniyer;
     set uni_year;
     array yrs  _:;
     grp=catx(' * ',label,substr(vname(yrs[1]),2));
     enrol=yrs[1];
     delta=.;
     output;
     do j = 2 to dim(yrs)-1;
       grp=catx(' * ',label,substr(vname(yrs[j]),2));
       enrol=yrs[j];
       delta=yrs[j]/yrs[j-1] -1;
       keep grp enrol delta;
       output;
     end;
    run;quit;

    proc print data=uniyer;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   UNI_YEAR total obs=4 11SEP2024:14:23:12                                                                              */
    /*                                                                                                                        */
    /*    LABEL    _2017    _2018    _2019     SUM                                                                            */
    /*                                                                                                                        */
    /*    uni1       730     1591     1072     3393                                                                           */
    /*    uni2       990      462      926     2378                                                                           */
    /*    uni3      2768     2618     2105     7491                                                                           */
    /*    Sum       4488     4671     4103    13262                                                                           */
    /*                                                                                                                        */
    /*   TRANSPOSE                                                                                                            */
    /*                                                                                                                        */
    /*     GRP           ENROL     CHANGE                                                                                     */
    /*                                                                                                                        */
    /*   uni1 * 2017        730      .                                                                                        */
    /*   uni1 * 2018       1591     1.17945  1591/730-1                                                                       */
    /*   uni1 * 2019       1072    -0.32621  1072/1591-1                                                                      */
    /*                                                                                                                        */
    /*   uni2 * 2017        990      .                                                                                        */
    /*   uni2 * 2018        462    -0.53333  462/990-1                                                                        */
    /*   uni2 * 2019        926     1.00433                                                                                   */
    /*                                                                                                                        */
    /*   uni3 * 2017       2768      .                                                                                        */
    /*   uni3 * 2018       2618    -0.05419                                                                                   */
    /*   uni3 * 2019       2105    -0.19595                                                                                   */
    /*                                                                                                                        */
    /*   Sum *  2017       4488      .                                                                                        */
    /*   Sum *  2018       4671     0.04078   Sum of all universities change in enrollment over the years                     */
    /*   Sum *  2019       4103    -0.12160                                                                                   */
    /*                                                                                                                        */
    /********* ****************************************************************************************************************/

    /*___    _                         _       _            _      ___
    |___ \  | |__  _   _   _   _ _ __ (_)   __| | ___ _ __ | |_   ( _ )    _   _  ___  __ _ _ __
      __) | | `_ \| | | | | | | | `_ \| |  / _` |/ _ \ `_ \| __|  / _ \/\ | | | |/ _ \/ _` | `__|
     / __/  | |_) | |_| | | |_| | | | | | | (_| |  __/ |_) | |_  | (_>  < | |_| |  __/ (_| | |
    |_____| |_.__/ \__, |  \__,_|_| |_|_|  \__,_|\___| .__/ \__|  \___/\/  \__, |\___|\__,_|_|
                   |___/                             |_|                   |___/
    */

    ods output observed=uni_dept;
    proc corresp data=sd1.have observed dim=1 cross=row;
      table uni dept, year;
      weight enrol;
    run;quit;

    data unidpt/ view=unidpt;
     set uni_dept;
     array yrs  _:;
     grp=catx(' * ',label,substr(vname(yrs[1]),2));
     enrol=yrs[1];
     delta=.;
     output;
     do j = 2 to dim(yrs)-1;
       grp=catx(' * ',label,substr(vname(yrs[j]),2));
       enrol=yrs[j];
       delta=yrs[j]/yrs[j-1] -1;
       keep grp enrol delta;
       output;
     end;
    run;quit;

    proc print data=unidpt;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  LABEL           _2017    _2018    _2019     SUM                                                                       */
    /*                                                                                                                        */
    /*  uni1 * dept1       51      468      344      863                                                                      */
    /*  uni1 * dept2      322      205      256      783                                                                      */
    /*  uni1 * dept3      251      718      434     1403                                                                      */
    /*  uni1 * dept4      106      200       38      344                                                                      */
    /*  uni2 * dept1      487       14      489      990                                                                      */
    /*  uni2 * dept2      503      448      437     1388                                                                      */
    /*  uni3 * dept1      695      864       47     1606                                                                      */
    /*  uni3 * dept2      833      888      753     2474                                                                      */
    /*  uni3 * dept3      941      531      319     1791                                                                      */
    /*  uni3 * dept4      299      335      986     1620                                                                      */
    /*  Sum              4488     4671     4103    13262                                                                      */
    /*                                                                                                                        */
    /*  GRP                    ENROL       DELTA                                                                              */
    /*                                                                                                                        */
    /*  uni1 * dept1 * 2017       51       .                                                                                  */
    /*  uni1 * dept1 * 2018      468      8.1765  468/51 -1                                                                   */
    /*  uni1 * dept1 * 2019      344     -0.2650  344/468-1                                                                   */
    /*                                                                                                                        */
    /*  uni1 * dept2 * 2017      322       .                                                                                  */
    /*  uni1 * dept2 * 2018      205     -0.3634                                                                              */
    /*  uni1 * dept2 * 2019      256      0.2488                                                                              */
    /*  uni1 * dept3 * 2017      251       .                                                                                  */
    /*  uni1 * dept3 * 2018      718      1.8606                                                                              */
    /*  uni1 * dept3 * 2019      434     -0.3955                                                                              */
    /*  uni1 * dept4 * 2017      106       .                                                                                  */
    /*  uni1 * dept4 * 2018      200      0.8868                                                                              */
    /*  uni1 * dept4 * 2019       38     -0.8100                                                                              */
    /*  uni2 * dept1 * 2017      487       .                                                                                  */
    /*  uni2 * dept1 * 2018       14     -0.9713                                                                              */
    /*  uni2 * dept1 * 2019      489     33.9286                                                                              */
    /*  uni2 * dept2 * 2017      503       .                                                                                  */
    /*  uni2 * dept2 * 2018      448     -0.1093                                                                              */
    /*  uni2 * dept2 * 2019      437     -0.0246                                                                              */
    /*  uni3 * dept1 * 2017      695       .                                                                                  */
    /*  uni3 * dept1 * 2018      864      0.2432                                                                              */
    /*  uni3 * dept1 * 2019       47     -0.9456                                                                              */
    /*  uni3 * dept2 * 2017      833       .                                                                                  */
    /*  uni3 * dept2 * 2018      888      0.0660                                                                              */
    /*  uni3 * dept2 * 2019      753     -0.1520                                                                              */
    /*  uni3 * dept3 * 2017      941       .                                                                                  */
    /*  uni3 * dept3 * 2018      531     -0.4357                                                                              */
    /*  uni3 * dept3 * 2019      319     -0.3992                                                                              */
    /*  uni3 * dept4 * 2017      299       .                                                                                  */
    /*  uni3 * dept4 * 2018      335      0.1204                                                                              */
    /*  uni3 * dept4 * 2019      986      1.9433                                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    data both(where=(grp ne :'Sum'));
      set uniyer unidpt;
    run;quit;

    proc print data=both;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* RESULT total obs=9                                                                                                     */
    /*                                                                                                                        */
    /*  Obs    GRP                    ENROL       DELTA                                                                       */
    /*                                                                                                                        */
    /*    1    uni1 * 2017              730       .                                                                           */
    /*    2    uni1 * 2018             1591      1.1795                                                                       */
    /*    3    uni1 * 2019             1072     -0.3262                                                                       */
    /*    4    uni2 * 2017              990       .                                                                           */
    /*    5    uni2 * 2018              462     -0.5333                                                                       */
    /*    6    uni2 * 2019              926      1.0043                                                                       */
    /*    7    uni3 * 2017             2768       .                                                                           */
    /*    8    uni3 * 2018             2618     -0.0542                                                                       */
    /*    9    uni3 * 2019             2105     -0.1960                                                                       */
    /*                                                                                                                        */
    /*   10    uni1 * dept1 * 2017       51       .                                                                           */
    /*   11    uni1 * dept1 * 2018      468      8.1765                                                                       */
    /*   12    uni1 * dept1 * 2019      344     -0.2650                                                                       */
    /*   13    uni1 * dept2 * 2017      322       .                                                                           */
    /*   14    uni1 * dept2 * 2018      205     -0.3634                                                                       */
    /*   15    uni1 * dept2 * 2019      256      0.2488                                                                       */
    /*   16    uni1 * dept3 * 2017      251       .                                                                           */
    /*   17    uni1 * dept3 * 2018      718      1.8606                                                                       */
    /*   18    uni1 * dept3 * 2019      434     -0.3955                                                                       */
    /*   19    uni1 * dept4 * 2017      106       .                                                                           */
    /*   20    uni1 * dept4 * 2018      200      0.8868                                                                       */
    /*   21    uni1 * dept4 * 2019       38     -0.8100                                                                       */
    /*   22    uni2 * dept1 * 2017      487       .                                                                           */
    /*   23    uni2 * dept1 * 2018       14     -0.9713                                                                       */
    /*   24    uni2 * dept1 * 2019      489     33.9286                                                                       */
    /*   25    uni2 * dept2 * 2017      503       .                                                                           */
    /*   26    uni2 * dept2 * 2018      448     -0.1093                                                                       */
    /*   27    uni2 * dept2 * 2019      437     -0.0246                                                                       */
    /*   28    uni3 * dept1 * 2017      695       .                                                                           */
    /*   29    uni3 * dept1 * 2018      864      0.2432                                                                       */
    /*   30    uni3 * dept1 * 2019       47     -0.9456                                                                       */
    /*   31    uni3 * dept2 * 2017      833       .                                                                           */
    /*   32    uni3 * dept2 * 2018      888      0.0660                                                                       */
    /*   33    uni3 * dept2 * 2019      753     -0.1520                                                                       */
    /*   34    uni3 * dept3 * 2017      941       .                                                                           */
    /*   35    uni3 * dept3 * 2018      531     -0.4357                                                                       */
    /*   36    uni3 * dept3 * 2019      319     -0.3992                                                                       */
    /*   37    uni3 * dept4 * 2017      299       .                                                                           */
    /*   38    uni3 * dept4 * 2018      335      0.1204                                                                       */
    /*   39    uni3 * dept4 * 2019      986      1.9433                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

