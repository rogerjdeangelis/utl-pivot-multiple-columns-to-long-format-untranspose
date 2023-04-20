# utl-pivot-multiple-columns-to-long-format-untranspose
Pivot multiple columns to long format untranspose 
    %let pgm=utl-pivot-multiple-columns-to-long-format-untranspose;

    Pivot multiple columns to long format untranspose

    simple mmatrix problem

    1. SAS arrays
    2. WPS arrays

    github
    https://tinyurl.com/2a8baxb8
    https://github.com/rogerjdeangelis/utl-pivot-multiple-columns-to-long-format-untranspose

    StackOverflow
    https://tinyurl.com/tjf5brbh
    https://stackoverflow.com/questions/75397242/sas-pivot-multiple-columns-to-long-format

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have (drop=z rename=(
           CHILDID1       =  CI1
           CHILD1METRICX  =  C12
           CHILD1METRICY  =  C13
           CHILDID2       =  C21
           CHILD2METRICX  =  C22
           CHILD2METRICY  =  C23
           CHILDID3       =  C31
           CHILD3METRICX  =  C32
           CHILD3METRICY  =  C33
           ));
      do id = 1 to 10;
        z+1; childid1 = z;
        z+1; child1metricX = z;
        z+1; child1metricY = z;

        z+1; childid2 = z;
        z+1; child2metricX = z;
        z+1; child2metricY = z;

        z+1; childid3 = z;
        z+1; child3metricX = z;
        z+1; child3metricY = z;

        output;
      end;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Up to 40 obs from last table WORK.HAVE total obs=10 19APR2023:19:33:47    |   RULES                                    */
    /*                                                                           |                                            */
    /* Obs    ID    CI1    C12    C13    C21    C22    C23    C31    C32    C33  |   ID  ROW    MAPPING ROW COLUMN            */
    /*                                                                           |    1  1      c11=1  c12=2  c13=3           */
    /*   1     1      1      2      3      4      5      6      7      8      9  |    1  2      c21=4  c22=5  c23=6           */
    /*   2     2     10     11     12     13     14     15     16     17     18  |    1  3      c31=7  c32=8  c33=9           */
    /*   3     3     19     20     21     22     23     24     25     26     27  |                                            */
    /*   4     4     28     29     30     31     32     33     34     35     36  |                                            */
    /*   5     5     37     38     39     40     41     42     43     44     45  |                                            */
    /*   6     6     46     47     48     49     50     51     52     53     54  |                                            */
    /*   7     7     55     56     57     58     59     60     61     62     63  |                                            */
    /*   8     8     64     65     66     67     68     69     70     71     72  |                                            */
    /*   9     9     73     74     75     76     77     78     79     80     81  |                                            */
    /*  10    10     82     83     84     85     86     87     88     89     90  |                                            */
    /*                                                                           |                                            */
    /**************************************************************************************************************************/

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

     /**************************************************************************************************************************/
     /*                                                                                                                        */
     /* Up to 40 obs from WANT total obs=30 19APR2023:19:58:39                                                                 */
     /*                                                                                                                        */
     /* Obs    ID    ROW    CHILD_ID    CHILD_X    CHILD_Y                                                                     */
     /*                                                                                                                        */
     /*   1     1     1         1           2          3                                                                       */
     /*   2     1     2         4           5          6                                                                       */
     /*   3     1     3         7           8          9                                                                       */
     /*   4     2     1        10          11         12                                                                       */
     /*   5     2     2        13          14         15                                                                       */
     /*   6     2     3        16          17         18                                                                       */
     /*   7     3     1        19          20         21                                                                       */
     /*   8     3     2        22          23         24                                                                       */
     /*   9     3     3        25          26         27                                                                       */
     /*                                                                                                                        */
     /**************************************************************************************************************************/

     /*
     ___  __ _ ___   _ __  _ __ ___   ___ ___  ___ ___
    / __|/ _` / __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    \__ \ (_| \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
    |___/\__,_|___/ | .__/|_|  \___/ \___\___||___/___/
                    |_|
    */

    data want (rename=(o1=child_id o2=child_X o3=child_y));

      retain id row o1 o2 o2;
      set have;

      array cs[3,3] CI1 C12 C13 C21 C22 C23 C31 C32 C33;
      array os[3] o1 o2 o3 ;

      do row =1 to 3;
        do col =1 to 3;
           os[col] =cs [row,col];
        end;
        output;
      end;

      keep  id row o:;
    run;quit;

    /*
    __      ___ __  ___   _ __  _ __ ___   ___ ___  ___ ___
    \ \ /\ / / `_ \/ __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
     \ V  V /| |_) \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
      \_/\_/ | .__/|___/ | .__/|_|  \___/ \___\___||___/___/
             |_|         |_|
    */

    %let _pth=%sysfunc(pathname(work));

    %utl_submit_wps64('

    libname wrk "&_pth";

    data want (rename=(o1=child_id o2=child_X o3=child_y));

      retain id row o1 o2 o2;
      set wrk.have;

      array cs[3,3] CI1 C12 C13 C21 C22 C23 C31 C32 C33;
      array os[3] o1 o2 o3 ;

      do row =1 to 3;
        do col =1 to 3;
           os[col] =cs [row,col];
        end;
        output;
      end;

      keep  id row o:;

    run;quit;

    proc print;
    run;quit;

    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs    id    row    child_id    child_X    child_y                                                                     */
    /*                                                                                                                        */
    /*   1     1     1         1           2          3                                                                       */
    /*   2     1     2         4           5          6                                                                       */
    /*   3     1     3         7           8          9                                                                       */
    /*   4     2     1        10          11         12                                                                       */
    /*   5     2     2        13          14         15                                                                       */
    /*   6     2     3        16          17         18                                                                       */
    /*   7     3     1        19          20         21                                                                       */
    /*   8     3     2        22          23         24                                                                       */
    /*   9     3     3        25          26         27                                                                       */
    /*   ...                                                                                                                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
