# utl_how_to_find_the_union_and_intersection_of_words_in_two_strings_nlp
How to find the union and intersection of words in two strings NLP.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    How to find the union and intersection of words in two strings NLP.

       Maybe Python is the best language for this Natural Language Problem?

       FIVE SOLUTIONS

             1. Datastep and FCMP
             2. HASH by Bartosz Jablonski (need help with adding union)
             2.5 Bartosz Jablonski (recent addition - see end of message)
             3. R intersect and union
             4. Datastep view and proc sql (just intersect)
             5. Python (union and intersect - slightly different input)
             6.5 Rick's recent addition Rick Wicklin rick.wicklin@sas.com
             
        FCMP Subroutine on end


    github
    https://tinyurl.com/yatup8u7
    https://github.com/rogerjdeangelis/utl_how_to_find_the_union_and_intersection_of_words_in_two_strings_nlp

    SAS Forum
    https://tinyurl.com/yatdpaum
    https://communities.sas.com/t5/SAS-Studio/How-to-find-the-union-and-intersection-of-two-string-combos/m-p/492517

    Python Solution
    http://forums.devshed.com/python-programming-11/compare-2-lists-words-strings-765278.html

    This uses FCMP subroutive on the end of message.
    The FCMP pops the first word off a sentence and removes the first word from input string..

    see nice hash solution in end by
    Bartosz Jablonski's profile photo
    yabwon@gmail.com


    INPUT
    =====

     SD1.HAVE total obs=2
      STR1                                          STR2
      to be or not to be                            2 b or not 2 b
      every good deed results in a better person    good better best never let it rest
                                                    until the good is better and better is best

    EXAMPLE OUTPUT  (Intersecting or common words)
    -----------------------------------------------

     WORK.WANT total obs=2

       INTERSECT           UNION                                        CNTINTERSECT    CNTUNION

       or not              to be or not 2 b                                  2             6

       good better best    every deed results in a or person never           3            18
                           let it rest until the good and better is best**

       ** good, better and best only appear once in the union


    PROCESS
    =======

     1. Datastep and FCMP
     --------------------

       data want;
        length pop1st intersect union $400 ;
        call missing(pop1st,intersect);
        set sd1.have;
        both=catx(' ',str1,str2);
        do wrds=1 to countw(str1);
          * pop first word from str1 - not str1 will not have the first word after utl_pop;
          call utl_pop(str1,pop1st,"first");
          if indexw(str2,pop1st)>0 then intersect=catx(' ',intersect,pop1st);
        end;
        * dedup union - note only good, better and best appear once ;
        do wrds=1 to countw(both);
          call utl_pop(both,pop1st,"first");
          select;
              when (indexw(both,pop1st)>0);
              otherwise union=catx(' ',union,pop1st);
          end;
        end;
        cntIntersect=countw(intersect);
        cntUnion    =countw(Union);
        keep intersect union cntIntersect cntUnion;
       run;quit;

          if indexw(str2,pop1st)=0 /*and indexw(union,pop1st)=0*/ then union=catx(' ',union,pop1st);

     2. HASH by Bartosz Jablonski
     -----------------------------

        data want;

        dcl hash h () ;
          h.definekey ("word") ;
          h.definedata ("word", "count") ;
          h.definedone ();

        do until(eof);
        h.clear();

        set sd1.have end = eof;

        count = 0;
        do i=1 to countw(str1);
         word = scan(str1, i);
         if h.find() then h.add();
        end;

        do i=1 to countw(str2);
         word = scan(str2, i);
         if h.find() = 0 then do; count = 1; h.replace(); end;
        end;


        declare hiter ih('h');
        count_of_common=0; length common_words $ 200; common_words = "";

        _rc_ = ih.first();
        do while(_rc_ = 0);
            if count then do;
                            common_words = catx(" ", common_words, word);
                            count_of_common +1;
                          end;
            _rc_ = ih.next();
        end;
        output;
        end;

        stop; keep str1 str2 common_words count_of_common;
        run;

    3.  R
    -----

        %utl_submit_r64('
        a = c("to be or not to be");
        b = c("2 b or not 2 b");
        a=unlist(strsplit(a,"[ ]"));
        b=unlist(strsplit(b,"[ ]"));
        intersect(a,b);
        union(a,b);
       ');

       Intersect "or"  "not"
       Union     "to"  "be"  "or"  "not" "2"   "b"


    4. Datastep view and proc sql
       --------------------------

       data havNrm/view=havNrm;
          retain grp 1;
          set sd1.have(obs=2);
          fro='ONE';
          do wrds=1 to countw(str1);
             wrd=scan(str1,wrds);
             output;
         end;
          fro='TWO';
          do wrds=1 to countw(str2);
             wrd=scan(str2,wrds);
             output;
         end;
         grp=grp+1;
         keep grp fro wrd;
       run;quit;

       proc sql;
         select
           distinct
             l.grp
            ,l.wrd
         from
            havNrm(where=(fro='ONE')) as l, havNrm(where=(fro='TWO')) as r
         where
            l.wrd = r.wrd and
            l.grp = r.grp
       ;quit;

        /*
         GRP  WRD
        -------------
           1  not
           1  or
           2  best
           2  better
           2  good
        */

    5. Python (union and intersect - slightly different input)

       %utl_submit_py64("
       set1 = set(['one' , 'two' , 'three' , 'four' , 'five']);
       set2 = set(['eight' , 'seven' , 'six' , 'five' , 'four']);
       ntersect = set1.intersection(set2);
       common = set1.union(set2);
       print(common);
       print(ntersect);
       ");

      Distinct Union set(['seven', 'six', 'three', 'one', 'four', 'five', 'two', 'eight'])
      Intersection:  set(['four', 'five'])


    OUTPUT
    ======

     WORK.WANT total obs=2

       INTERSECT           UNION                                        CNTINTERSECT    CNTUNION

       or not              to be or not 2 b                                  2             6

       good better best    every deed results in a or person never           3            18
                           let it rest until the good and better is best**

       ** good, better and best only appear once in the union
    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
       length str1 str2 $200;
       str1="to be or not to be";
       str2="2 b or not 2 b";
       output;
       str1="every good deed results in a better or best person";
       str2="good better best never let it rest until the good is better and better is best";
       output;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|
    ;

    see process


    * __
     / _| ___ _ __ ___  _ __    _ __   ___  _ __
    | |_ / __| '_ ` _ \| '_ \  | '_ \ / _ \| '_ \
    |  _| (__| | | | | | |_) | | |_) | (_) | |_) |
    |_|  \___|_| |_| |_| .__/  | .__/ \___/| .__/
                       |_|     |_|         |_|
    ;


    options cmplib=work.funcs;
    proc fcmp outlib=work.funcs.temp;
    Subroutine utl_pop(string $,word $,action $);
        outargs word, string;
        length word $200;
        select (upcase(action));
          when ("LAST") do;
            call scan(string,-1,_action,_length,' ');
            word=substr(string,_action,_length);
            string=substr(string,1,_action-1);
          end;

          when ("FIRST") do;
            call scan(string,1,_action,_length,' ');
            word=substr(string,_action,_length);
            string=substr(string,_action + _length);
          end;

          otherwise put "ERROR: Invalid action";

        end;
    endsub;
    run;quit;
    
     ____             _
    | __ )  __ _ _ __| |_ ___  ___ ____
    |  _ \ / _` | '__| __/ _ \/ __|_  /
    | |_) | (_| | |  | || (_) \__ \/ /
    |____/ \__,_|_|   \__\___/|___/___|

    ;

    data have;
       length str1 str2 $200;
       str1="to be or not to be";
       str2="2 b or not 2 b";
       output;
       str1="every good deed results in a better person best";
       str2="good better best never let it rest until the good is better and better is best";
       output;
       str1="1 2 3";
       str2="3 4 5";
       output;
       str1="A B C";
       str2="a b c";
       output;
       str1="x y z";
       str2="x y z";
       output;
    run;quit;


    data want;

    dcl hash h (ordered: "a") ;
      h.definekey ("word") ;
      h.definedata ("word", "count") ;
      h.definedone ();

    do until(eof);
    h.clear();

    set have end = eof;

    count = 0;
    countw_str1 = countw(str1);
    do i=1 to countw_str1;
     word = scan(str1, i);
     if h.find() then h.add();
    end;

    countw_str2 = countw(str2);
    do i=1 to countw_str2;
     word = scan(str2, i);
     if h.find() = 0 then do; count = count + 1;                     h.replace(); end;
                     else do; count = -sum(countw_str1,countw_str2); h.add();     end;
                     /* it could be done like that: "count = .;"
                        but then you have: "NOTE: Missing values were generated..." in the log
                        and it is a ugly one ;-)
                     */
    end;


    declare hiter ih('h');
    count_of_common=0; length common_words $ 200; common_words = "";
    count_of_union=0;  length union_words $ 200; union_words = "";

    _rc_ = ih.first();
    do while(_rc_ = 0);
        if count>0 then do;
                        common_words = catx(" ", common_words, word);
                        count_of_common +1;
                      end;
                      union_words = catx(" ", union_words, word);
                      count_of_union +1;
        _rc_ = ih.next();
    end;
    output;

    /**/
    end;

    stop;
    keep str1 str2 common_words count_of_common union_words count_of_union;
    run;
    
    
        *____  _      _
    |  _ \(_) ___| | __
    | |_) | |/ __| |/ /
    |  _ <| | (__|   <
    |_| \_\_|\___|_|\_\

    ;

    For a discussion of how to break a sentence into individual words in SAS, see
    https://blogs.sas.com/content/iml/2016/07/11/break-sentence-into-words-sas.html

    The SAS/IML solution is almost identical to the R solution, except it uses the familiar COUNTW and SCAN functions.
    Also the UNION function in IML returns the results in sorted order.

    proc iml;
    a = "to be or not to be";
    b = "2 b or not 2 b";
    delims = ' ,.!';
    a = scan(a, 1:countw(a, delims), delims);
    b = scan(b, 1:countw(b, delims), delims);
    intersect = xsect(a,b);
    union = union(a,b);
    print intersect, union;


    Rick Wicklin
    Statistical programming blog: http://blogs.sas.com/content/iml

    Rick Wicklin rick.wicklin@sas.com






