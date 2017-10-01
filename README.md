# utl_flexible_proc_report
Alternative to tabulate to create a clinical report

    ```  A flexible proc report: Moving titles and page numbering inside the body(border box) of 'proc report'  ```
    ```    ```
    ```  see for rtf output  ```
    ```  https://www.dropbox.com/s/jb8koydt1pai4nx/carsales.rtf?dl=0  ```
    ```    ```
    ```  "I need to do this: All titles and page numbering  ```
    ```  include in RTF body file, not just like headers,  ```
    ```  Page numbering should be on the top and left. Please help."  ```
    ```    ```
    ```  I left 'footnotes out but it is easy to do 'and compute after to move footnotes inside  ```
    ```  the box.  ```
    ```    ```
    ```    ```
    ```  see  ```
    ```  https://communities.sas.com/t5/ODS-and-Base-Reporting/Proc-Report/m-p/400102  ```
    ```    ```
    ```    ```
    ```    WORKING CODE (you can use a macro instaead of DOSUBL)  ```
    ```    ============  ```
    ```    ```
    ```      proc report data=want(where=(page=&pge)) nowd split="#" missing;  ```
    ```         cols  ```
    ```           ( "^S={just=r font_size=9pt} Page &pge of &pgemax"  ```
    ```           blankline  ```
    ```            ("Country: &country Region: Region Observed and Predicted Car Sales for 1993-1994"  ```
    ```             ("Geography"  ```
    ```             country  ```
    ```             region )  ```
    ```             type  ```
    ```             ( "Sales Years"  ```
    ```             __1993  ```
    ```             __1994  ```
    ```             __sum  ```
    ```             )  ```
    ```          )  ```
    ```    ```
    ```    ```
    ```  HAVE  (from sashelp.prdsale)  ```
    ```  =============================  ```
    ```    ```
    ```    WORK.HAVSRT total obs=2,880  ```
    ```    ```
    ```      Obs    COUNTRY    REGION    YEAR    TYPE         SALES  ```
    ```    ```
    ```        1    CANADA      EAST     1993    Actual        925  ```
    ```        2    CANADA      EAST     1993    Predicted     850  ```
    ```        3    CANADA      EAST     1993    Actual        999  ```
    ```        4    CANADA      EAST     1993    Predicted     297  ```
    ```        5    CANADA      EAST     1993    Actual        608  ```
    ```        6    CANADA      EAST     1993    Predicted     846  ```
    ```        7    CANADA      EAST     1993    Actual        642  ```
    ```        8    CANADA      EAST     1993    Predicted     533  ```
    ```      ...  ```
    ```    ```
    ```  WANT  ```
    ```  ====  ```
    ```    ```
    ```    ```
    ```   +---------------------------------------------------------------------------------------------------------------------------+  ```
    ```   |                                                                                                               Page 1 of 6 |  ```
    ```   |                              Country: U.S.A. Region: Region Observed and Predicted Car Sales for 1993-1994                |  ```
    ```   |                                                                                                                           |  ```
    ```   |       Geography         Data                                                   Sales Years                                |  ```
    ```   |                                                                                                                           |  ```
    ```   | Country     Region      Type       USAActual1993                     USAActual1994                     USAActual          |  ```
    ```   |                                                                                                                           |  ```
    ```   | U.S.A.      WEST        Actual     $60,826 (50.1%)                   $58,294 (49.0%)                   $119,120 (49.6%)   |  ```
    ```   |                         Predicted  $60,484 (49.9%)                   $60,651 (51.0%)                   $121,135 (50.4%)   |  ```
    ```   | U.S.A.      WEST        Sum        $121,310 (100.0%)                 $118,945 (100.0%)                 $240,255 (100.0%)  |  ```
    ```   |                                                                                                                           |  ```
    ```   +---------------------------------------------------------------------------------------------------------------------------+  ```
    ```    ```
    ```    ....  ```
    ```    ```
    ```    ```
    ```   +---------------------------------------------------------------------------------------------------------------------------+  ```
    ```   |                                                                                                              Page 6 of 6  |  ```
    ```   |                              Country: Germany Region: Region Observed and Predicted Car Sales for 1993-1994               |  ```
    ```   |                                                                                                                           |  ```
    ```   |       Geography         Data                                                   Sales Years                                |  ```
    ```   |                                                                                                                           |  ```
    ```   | Country     Region      Type       GBRActual1993                     GBRActual1994                     GBRActual          |  ```
    ```   |                                                                                                                           |  ```
    ```   | Germany     WEST        Actual     $60,826 (50.1%)                   $58,294 (49.0%)                   $119,120 (49.6%)   |  ```
    ```   |                         Predicted  $60,484 (49.9%)                   $60,651 (51.0%)                   $121,135 (50.4%)   |  ```
    ```   | Germany     WEST        Sum        $121,310 (100.0%)                 $118,945 (100.0%)                 $240,255 (100.0%)  |  ```
    ```   |                                                                                                                           |  ```
    ```   +---------------------------------------------------------------------------------------------------------------------------+  ```
    ```    ```
    ```  *                _               _       _  ```
    ```   _ __ ___   __ _| | _____     __| | __ _| |_ __ _  ```
    ```  | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |  ```
    ```  | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |  ```
    ```  |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|  ```
    ```    ```
    ```  ;  ```
    ```   proc datasets lib=work;  ```
    ```   delete have;  ```
    ```   run;quit;  ```
    ```    ```
    ```  *just in case - you do not have to do thsi;  ```
    ```   %symdel  ```
    ```      CANADAEAST CANADAEAST1993 CANADAEAST1994 CANADAWEST CANADAWEST1993  ```
    ```      CANADAWEST1994 GERMANYEAST GERMANYEAST1993 GERMANYEAST1994  ```
    ```      GERMANYWEST GERMANYWEST1993 GERMANYWEST1994 NYEARS PGE PGEMAX  ```
    ```      REGION TOTAL TYPE USAEAST USAEAST1993 USAEAST1994 USAWEST USAWEST1993  ```
    ```      USAWEST1994 YEARS YR1993 YR1994 / nowarn;  ```
    ```    ```
    ```  %utlkill(d:/rtf/carsales.rtf); * deletes the rtf output if it exists;  ```
    ```    ```
    ```  data have;  ```
    ```     length yearchr $50;  ```
    ```     retain yearchr ' ';  ```
    ```     set sashelp.prdsale(keep=country region year actual predict) end=dne;  ```
    ```     type = 'Actual   ';  ```
    ```     Sales=Actual ;  ```
    ```     output;  ```
    ```     type = 'Predicted';  ```
    ```     Sales=predict;  ```
    ```     output;  ```
    ```     if index(yearchr,put(year,4.))=0 then yearchr=catx(' ',yearchr,'__'!!put(year,4.));  ```
    ```     drop actual predict;  ```
    ```     if dne then do;  ```
    ```         years=countw(yearchr);  ```
    ```         call symputx('years',catx(' ',yearchr,'__sum'));  ```
    ```         call symputx('nyears',put(countw(yearchr,' ')+1,2.));  ```
    ```     end;  ```
    ```     keep country region year type sales;  ```
    ```  run;quit;  ```
    ```  proc sort data=have out=havsrt;  ```
    ```  by country region;  ```
    ```  run;quit;  ```
    ```  *          _       _   _  ```
    ```   ___  ___ | |_   _| |_(_) ___  _ __  ```
    ```  / __|/ _ \| | | | | __| |/ _ \| '_ \  ```
    ```  \__ \ (_) | | |_| | |_| | (_) | | | |  ```
    ```  |___/\___/|_|\__,_|\__|_|\___/|_| |_|  ```
    ```    ```
    ```  ;  ```
    ```    ```
    ```  * you need to compile the template on the end first;  ```
    ```    ```
    ```  Ods Exclude All;  ```
    ```  Ods Output Observed=wantpre(Rename=Label=type);  ```
    ```  Proc Corresp Data=havsrt Observed dimens=1  cross=row;  ```
    ```  by country region;  ```
    ```     Tables type , year;  ```
    ```     weight sales;  ```
    ```  Run;  ```
    ```  Ods Select All;  ```
    ```    ```
    ```  data want;  ```
    ```    do until (last.region);  ```
    ```      set wantpre;  ```
    ```      array nums _numeric_;  ```
    ```      length cntry $12;  ```
    ```      retain page 0 cntry;  ```
    ```      array sums[&nyears] _temporary_;  ```
    ```      array pcts[*] $32 &years;  ```
    ```      by country region;  ```
    ```    end;  ```
    ```    do idx=1 to dim(pcts);  ```
    ```       sums[idx]=nums[idx];  ```
    ```    end;  ```
    ```    cntry=country;  ```
    ```    page=page+1;  ```
    ```    do until (last.region);  ```
    ```      set wantpre end=dne;  ```
    ```      by country region;  ```
    ```      do idx=1 to dim(pcts);  ```
    ```        pcts[idx]=catx(' ',put(nums[idx],dollar12.),cats('(',put(100*nums[idx]/sums[idx],6.1),'%)'));  ```
    ```      end;  ```
    ```      keep blankline page country region type  __:  ;  ```
    ```      if type='Sum' then blankline=1;  ```
    ```      else blankline=0;  ```
    ```      output;  ```
    ```    end;  ```
    ```    do idx=1 to dim(pcts);  ```
    ```       sums[idx]=0;  ```
    ```    end;  ```
    ```    call symputx('pgemax',put(page,2.));  ```
    ```  run;quit;  ```
    ```    ```
    ```  * create header text;  ```
    ```  proc sql;  ```
    ```    select resolve(  ```
    ```  '%Let '!!cats(compress(country,"."),region,put(year,4.))!!'=%sysfunc(compbl('!!put(year,4.)!!  ```
    ```    '#(N='!!Put(Count(*),8.)!!');))')  ```
    ```    from have  Group by country, region, year  ```
    ```   ;  ```
    ```    select resolve(  ```
    ```  '%Let '!!cats(compress(country,"."),region)!!'=%sysfunc(compbl(Total'!!  ```
    ```    '#(N='!!Put(Count(*),8.)!!');))')  ```
    ```    from have  Group by country, region  ```
    ```  ;quit;  ```
    ```    ```
    ```  %put _all_;  ```
    ```    ```
    ```  options nodate nonumber orientation=landscape ls=255 ps=65;  ```
    ```  title;footnote;  ```
    ```  %utl_rtflan100;  ```
    ```  ods escapechar='^';  ```
    ```  ods listing close;  ```
    ```  ods rtf file="d:/rtf/carsales.rtf" style=utl_rtflan100 notoc_data;  ```
    ```  data _null_;  ```
    ```    ```
    ```     length ctytyp yr1993 yr1994 total $96;  ```
    ```     retain pge 0;  ```
    ```     set want;  ```
    ```     by country region;  ```
    ```     call symputx("country",country);  ```
    ```     call symputx("region",region);  ```
    ```     call symputx("type",type);  ```
    ```     ctytyp=cats(compress(country," _."),type);  ```
    ```     put ctytyp;  ```
    ```     if first.region then do;  ```
    ```    ```
    ```        pge=pge+1;  ```
    ```        yr1993=cats(ctytyp,'1993');  ```
    ```        yr1994=cats(ctytyp,'1994');  ```
    ```        total =cats(ctytyp);  ```
    ```    ```
    ```    ```
    ```        call symputx('yr1993',yr1993);  ```
    ```        call symputx('yr1994',yr1994);  ```
    ```        call symputx('total ',total);  ```
    ```        call symputx('pge',put(pge,2.));  ```
    ```    ```
    ```        rc=dosubl('  ```
    ```            proc report data=want(where=(page=&pge)) nowd split="#" missing;  ```
    ```               cols  ```
    ```                 ( "^S={just=r font_size=9pt} Page &pge of &pgemax"  ```
    ```                 blankline  ```
    ```                  ("Country: &country Region: Region Observed and Predicted Car Sales for 1993-1994"  ```
    ```                   ("Geography"  ```
    ```                   country  ```
    ```                   region )  ```
    ```                   type  ```
    ```                   ( "Sales Years"  ```
    ```                   __1993  ```
    ```                   __1994  ```
    ```                   __sum  ```
    ```                   )  ```
    ```                )  ```
    ```                );  ```
    ```                define blankline/ order noprint;  ```
    ```                define country  / order    "Country"      style={cellwidth=13%  just=r } order=data;  ```
    ```                define region   / order    "Region"       style={cellwidth=10%  just=r } order=data;  ```
    ```                define type     / display  "Data#Type"    style={cellwidth=12%  just=c } order=data;  ```
    ```                define __1993   / display  "&yr1993"      style={cellwidth=20%  just=c } order=data;  ```
    ```                define __1994   / display  "&yr1994"      style={cellwidth=20%  just=c } order=data;  ```
    ```                define __sum    / display  "&total"       style={cellwidth=20%  just=c } order=data;  ```
    ```            run;quit;  ```
    ```            ods rtf text="^S={outputwidth=100% just=l font_size=8pt font_style=italic}  {from &country Statistics}";  ```
    ```            ods rtf text="^S={outputwidth=100% just=l font_size=8pt font_style=italic}  {see Roger  DeAngelis}";  ```
    ```            ods rtf text="^S={outputwidth=100% just=l font_size=8pt font_style=italic}  {&sysdate &systime}";  ```
    ```            run;quit;  ```
    ```          run;quit;  ```
    ```        ');  ```
    ```    ```
    ```    ```
    ```       end;  ```
    ```  run;quit;  ```
    ```    ```
    ```  ods rtf close;  ```
    ```  ods listing;  ```
    ```    ```
    ```  *_                       _       _  ```
    ```  | |_ ___ _ __ ___  _ __ | | __ _| |_ ___  ```
    ```  | __/ _ \ '_ ` _ \| '_ \| |/ _` | __/ _ \  ```
    ```  | ||  __/ | | | | | |_) | | (_| | ||  __/  ```
    ```   \__\___|_| |_| |_| .__/|_|\__,_|\__\___|  ```
    ```                    |_|  ```
    ```  ;  ```
    ```    ```
    ```  %Macro utl_rtflan100  ```
    ```      (  ```
    ```        style=utl_rtflan100,  ```
    ```        frame=box,  ```
    ```        rules=groups,  ```
    ```        bottommargin=1.0in,  ```
    ```        topmargin=1.5in,  ```
    ```        rightmargin=1.0in,  ```
    ```        cellheight=10pt,  ```
    ```        cellpadding = 7,  ```
    ```        cellspacing = 3,  ```
    ```        leftmargin=.75in,  ```
    ```        borderwidth = 1  ```
    ```      ) /  Des="SAS Rtf Template for CompuCraft";  ```
    ```    ```
    ```  options orientation=landscape;run;quit;  ```
    ```    ```
    ```  ods path work.templat(update) sasuser.templat(update) sashelp.tmplmst(read);  ```
    ```    ```
    ```  Proc Template;  ```
    ```    ```
    ```     define style &Style;  ```
    ```     parent=styles.rtf;  ```
    ```    ```
    ```    ```
    ```          replace body from Document /  ```
    ```    ```
    ```                 protectspecialchars=off  ```
    ```                 asis=on  ```
    ```                 bottommargin=&bottommargin  ```
    ```                 topmargin   =&topmargin  ```
    ```                 rightmargin =&rightmargin  ```
    ```                 leftmargin  =&leftmargin  ```
    ```                 ;  ```
    ```    ```
    ```          replace color_list /  ```
    ```                'link' = blue  ```
    ```                 'bgH'  = _undef_  ```
    ```                 'fg'  = black  ```
    ```                 'bg'   = _undef_;  ```
    ```    ```
    ```          replace fonts /  ```
    ```                 'TitleFont2'           = ("Arial, Helvetica, Helv",11pt,Bold)  ```
    ```                 'TitleFont'            = ("Arial, Helvetica, Helv",11pt,Bold)  ```
    ```    ```
    ```                 'HeadingFont'          = ("Arial, Helvetica, Helv",10pt)  ```
    ```                 'HeadingEmphasisFont'  = ("Arial, Helvetica, Helv",10pt,Italic)  ```
    ```    ```
    ```                 'StrongFont'           = ("Arial, Helvetica, Helv",10pt,Bold)  ```
    ```                 'EmphasisFont'         = ("Arial, Helvetica, Helv",10pt,Italic)  ```
    ```    ```
    ```                 'FixedFont'            = ("Courier New, Courier",9pt)  ```
    ```                 'FixedEmphasisFont'    = ("Courier New, Courier",9pt,Italic)  ```
    ```                 'FixedStrongFont'      = ("Courier New, Courier",9pt,Bold)  ```
    ```                 'FixedHeadingFont'     = ("Courier New, Courier",9pt,Bold)  ```
    ```                 'BatchFixedFont'       = ("Courier New, Courier",7pt)  ```
    ```    ```
    ```                 'docFont'              = ("Arial, Helvetica, Helv",10pt)  ```
    ```    ```
    ```                 'FootFont'             = ("Arial, Helvetica, Helv", 9pt)  ```
    ```                 'StrongFootFont'       = ("Arial, Helvetica, Helv",8pt,Bold)  ```
    ```                 'EmphasisFootFont'     = ("Arial, Helvetica, Helv",8pt,Italic)  ```
    ```                 'FixedFootFont'        = ("Courier New, Courier",8pt)  ```
    ```                 'FixedEmphasisFootFont'= ("Courier New, Courier",8pt,Italic)  ```
    ```                 'FixedStrongFootFont'  = ("Courier New, Courier",7pt,Bold);  ```
    ```    ```
    ```          replace GraphFonts /  ```
    ```                 'GraphDataFont'        = ("Arial, Helvetica, Helv",8pt)  ```
    ```                 'GraphAnnoFont'        = ("Arial, Helvetica, Helv",8pt)  ```
    ```                 'GraphValueFont'       = ("Arial, Helvetica, Helv",10pt)  ```
    ```                 'GraphUnicodeFont'     = ("Arial, Helvetica, Helv",10pt)  ```
    ```                 'GraphLabelFont'       = ("Arial, Helvetica, Helv",10pt,Bold)  ```
    ```                 'GraphLabel2Font'      = ("Arial, Helvetica, Helv",10pt,Bold)  ```
    ```                 'GraphFootnoteFont'    = ("Arial, Helvetica, Helv",8pt)  ```
    ```                 'GraphTitle1Font'      = ("Arial, Helvetica, Helv",11pt,Bold)  ```
    ```                 'GraphTitleFont'       = ("Arial, Helvetica, Helv",11pt,Bold);  ```
    ```    ```
    ```          style table from table /  ```
    ```                  outputwidth=100%  ```
    ```                  protectspecialchars=off  ```
    ```                  asis=on  ```
    ```                  background = colors('tablebg')  ```
    ```                  frame=&frame  ```
    ```                  rules=&rules  ```
    ```                  cellheight  = &cellheight  ```
    ```                  cellpadding = &cellpadding  ```
    ```                  cellspacing = &cellspacing  ```
    ```                  bordercolor = colors('tableborder')  ```
    ```                  borderwidth = &borderwidth;  ```
    ```    ```
    ```           replace Footer from HeadersAndFooters  ```
    ```    ```
    ```                  / font = fonts('FootFont')  just=left asis=on protectspecialchars=off ;  ```
    ```    ```
    ```                  replace FooterFixed from Footer  ```
    ```                  / font = fonts('FixedFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace FooterEmpty from Footer  ```
    ```                  / font = fonts('FootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace FooterEmphasis from Footer  ```
    ```                  / font = fonts('EmphasisFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace FooterEmphasisFixed from FooterEmphasis  ```
    ```                  / font = fonts('FixedEmphasisFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace FooterStrong from Footer  ```
    ```                  / font = fonts('StrongFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace FooterStrongFixed from FooterStrong  ```
    ```                  / font = fonts('FixedStrongFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace RowFooter from Footer  ```
    ```                  / font = fonts('FootFont')  asis=on protectspecialchars=off just=left;  ```
    ```    ```
    ```                  replace RowFooterFixed from RowFooter  ```
    ```                  / font = fonts('FixedFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace RowFooterEmpty from RowFooter  ```
    ```                  / font = fonts('FootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace RowFooterEmphasis from RowFooter  ```
    ```                  / font = fonts('EmphasisFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace RowFooterEmphasisFixed from RowFooterEmphasis  ```
    ```                  / font = fonts('FixedEmphasisFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace RowFooterStrong from RowFooter  ```
    ```                  / font = fonts('StrongFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace RowFooterStrongFixed from RowFooterStrong  ```
    ```                  / font = fonts('FixedStrongFootFont')  just=left asis=on protectspecialchars=off;  ```
    ```    ```
    ```                  replace SystemFooter from TitlesAndFooters / asis=on  ```
    ```                          protectspecialchars=off just=left;  ```
    ```    ```
    ```      end;  ```
    ```  run;  ```
    ```    ```
    ```  quit;  ```
    ```    ```
    ```  %Mend utl_rtflan100;  ```
    ```    ```
