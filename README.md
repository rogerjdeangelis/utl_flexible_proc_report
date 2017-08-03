# utl_flexible_proc_report
Alternative to tabulate to create a clinical report

    ```  PCT in PROC TABULATE (I propose and alternative to possibly being painted in a corner)  ```
    ```    ```
    ```  see for sixx page report (or run the code below)  ```
    ```  https://www.dropbox.com/s/jb8koydt1pai4nx/carsales.rtf?dl=0  ```
    ```    ```
    ```  see  ```
    ```  https://goo.gl/gNhYw2  ```
    ```  https://communities.sas.com/t5/Base-SAS-Programming/PCT-in-PROC-TABULATE/m-p/384961  ```
    ```    ```
    ```  Clinical template on the end  ```
    ```    ```
    ```  Below is a more complicated solution but is very flexible;  ```
    ```    ```
    ```    1. Added row and column percents  ```
    ```    2. Big N in the header  ```
    ```    3. Sum(Pct) in each cell (more readable)  ```
    ```    4. Column sum and percents  ```
    ```    5. Differerent titles and foornotes on each page  ```
    ```    6. Follows a clinical template (box around report) template on end  ```
    ```    ```
    ```    ```
    ```   WORKING CODE  ```
    ```   ===========  ```
    ```    ```
    ```           1. make intial data long and skinny  ```
    ```    ```
    ```              type = 'Actual   ';  ```
    ```              Sales=Actual ;  ```
    ```              output;  ```
    ```              type = 'Predicted';  ```
    ```              Sales=predict;  ```
    ```              output;  ```
    ```    ```
    ```              *list of years (final columns) in report;  ```
    ```              if index(yearchr,put(year,4.))=0 then yearchr=catx(' ',yearchr,'__'!!put(year,4.));  ```
    ```    ```
    ```          2   * transpose and sum adding row and column totals;  ```
    ```              Proc Corresp Data=havsrt Observed dimens=1  cross=row;  ```
    ```                by country region;  ```
    ```              Tables type , year;  ```
    ```              weight sales;  ```
    ```    ```
    ```          3   * DOW (colsums are  in last row - create sum(pct)  ```
    ```                 do until(last.region);  ```
    ```                  set wantpre;  ```
    ```                      by country region;  ```
    ```                     array nums _numeric_;  ```
    ```                     array sums[&nyears] _temporary_;  ```
    ```                     array pcts[*] $32 &years;  ```
    ```                 do until (last.region);  ```
    ```                     set wantpre end=dne;  ```
    ```                     by country region;  ```
    ```                     do idx=1 to dim(pcts);  ```
    ```                         pcts[idx]=catx(' ',put(nums[idx],dollar12.),cats('(',put(100*nums[idx]/sums[idx],6.1),'%)'));  ```
    ```                     end;  ```
    ```    ```
    ```          4    * create big N column headers into macro vars;  ```
    ```                   select resolve(  ```
    ```                   '%Let '!!cats(compress(country,"."),region,put(year,4.))!!'=%sysfunc(compbl('!!put(year,4.)!!  ```
    ```                   '#(N='!!Put(Count(*),8.)!!');))')  ```
    ```                   from have  Group by country, region, year;  ```
    ```    ```
    ```          5    * create report (run report 6 times)  ```
    ```                   data _null:  ```
    ```                   set want;  ```
    ```                   by country region;  ```
    ```                   call symputx("country",country);  ```
    ```                   call symputx("region",region);  ```
    ```                   call symputx("type",type);  ```
    ```                   ctytyp=cats(compress(country,"."),type);  ```
    ```                   if first.region then do;  ```
    ```    ```
    ```                      pge=pge+1;  ```
    ```                      call symputx('yr1993',symget(cats(ctytyp,'1993')));  * gets bign headers from macro vars;  ```
    ```                      call symputx('yr1994',symget(cats(ctytyp,'1994')));  ```
    ```                      call symputx('total ',symget(cats(ctytyp)));  ```
    ```                      call symputx('pge',put(pge,2.));  ```
    ```    ```
    ```               DOSUBL  ```
    ```                   proc report data=want(where=(page=&pge)) nowd split="#" missing;  ```
    ```                      cols  ```
    ```                        blankline  ```
    ```                        ( "Country: &country Region: Region Observed and Predicted Car Sales for 1993-1994"  ```
    ```                         ("Geography"  ```
    ```                         country  ```
    ```                         region )  ```
    ```                         type  ```
    ```                         ( "Sales Years"  ```
    ```                         __1993  ```
    ```                         __1994  ```
    ```                         __sum  ```
    ```                         )  ```
    ```                       );  ```
    ```                       define blankline/ order    noprint;  ```
    ```                       define country  / order    "Country"      style={cellwidth=13%  just=r } order=data;  ```
    ```                       define region   / order    "Region"       style={cellwidth=10%  just=r } order=data;  ```
    ```                       define type     / display  "Data#Type"    style={cellwidth=12%  just=c } order=data;  ```
    ```                       define __1993   / display  "&yr1993"      style={cellwidth=20%  just=c } order=data;  ```
    ```                       define __1994   / display  "&yr1994"      style={cellwidth=20%  just=c } order=data;  ```
    ```                       define __sum    / display  "&total"       style={cellwidth=20%  just=c } order=data;  ```
    ```                   run;quit;  ```
    ```                   ods rtf text="^S={outputwidth=100% just=r font_size=9pt} Page &pge of &pgemax";  ```
    ```                   ods rtf text="^S={outputwidth=100% just=l font_size=8pt font_style=italic}  {from &country Statistics}";  ```
    ```                   ods rtf text="^S={outputwidth=100% just=l font_size=8pt font_style=italic}  {see Roger  DeAngelis}";  ```
    ```                   ods rtf text="^S={outputwidth=100% just=l font_size=8pt font_style=italic}  {&sysdate &systime}";  ```
    ```                   run;quit;  ```
    ```                 run;quit;  ```
    ```    ```
    ```  HAVE  ```
    ```  ====  ```
    ```    ```
    ```  p to 40 obs WORK.HAVSRT total obs=2,880  ```
    ```    ```
    ```  Obs    COUNTRY    REGION    YEAR    TYPE         SALES  ```
    ```    ```
    ```    1    CANADA      EAST     1993    Actual        925  ```
    ```    2    CANADA      EAST     1993    Predicted     850  ```
    ```    3    CANADA      EAST     1993    Actual        999  ```
    ```    4    CANADA      EAST     1993    Predicted     297  ```
    ```    5    CANADA      EAST     1993    Actual        608  ```
    ```    6    CANADA      EAST     1993    Predicted     846  ```
    ```    7    CANADA      EAST     1993    Actual        642  ```
    ```  ....  ```
    ```    ```
    ```    ```
    ```    ```
    ```  WANT  (looks much better than this in rtf format)  ```
    ```  =================================================  ```
    ```    ```
    ```    ```
    ```                   Country: Canada Region: Region Observed and Predicted Car Sales for 1993-1994  ```
    ```    ```
    ```                                                         Sales Years  ```
    ```          Geography         Data       Canada Actual1993       Canada Actual 1994        Total  ```
    ```    Country     Region      Type       (N= 240)                (N= 240)                (N= 480)  ```
    ```    ```
    ```    Canada      WEST        Actual     $60,826 (50.1%)         $58,294 (49.0%)         $119,120 (49.6%)  ```
    ```                            Predicted  $60,484 (49.9%)         $60,651 (51.0%)         $121,135 (50.4%)  ```
    ```    ```
    ```    Canada      WEST        Sum        $121,310 (100.0%)       $118,945 (100.0%)       $240,255 (100.0%)  ```
    ```    ```
    ```                                                                                           Page 1 og 6  ```
    ```    ....  ```
    ```    ```
    ```    ```
    ```    ```
    ```                  Country: U.S.A. Region: Region Observed and Predicted Car Sales for 1993-1994  ```
    ```    ```
    ```                                                           Sales Years  ```
    ```          Geography         Data       U.S.A.Actual 1993        U.S.A.Actual 1994        Total  ```
    ```    Country     Region      Type       (N= 240)                 (N= 240)                (N= 480)  ```
    ```    ```
    ```    U.S.A.      WEST        Actual     $60,826 (50.1%)          $58,294 (49.0%)         $119,120 (49.6%)  ```
    ```                            Predicted  $60,484 (49.9%)          $60,651 (51.0%)         $121,135 (50.4%)  ```
    ```    ```
    ```    U.S.A.      WEST        Sum        $121,310 (100.0%)        $118,945 (100.0%)       $240,255 (100.0%)  ```
    ```    ```
    ```                                                                                          Page 6 og 6  ```
    ```    ```
