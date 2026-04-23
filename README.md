# altair-slc-modeling-mean-time-to-failure-using-mil-hdbk-217f-like-methods-in-r
Altair slc modeling mean time to failure using mil hdbk 217f like methods in r
    %let pgm=altair-slc-modeling-mean-time-to-failure-using-mil-hdbk-217f-like-methods-in-r;

    %stop_submission;

    Altair slc modeling mean time to failure using mil hdbk 217f like methods in r

    Too long to pste in a list, see github
    https://github.com/rogerjdeangelis/altair-slc-modeling-mean-time-to-failure-using-mil-hdbk-217f-like-methods-in-r

    Siemens post
    [MTBF](https://support.industry.siemens.com/cs/document/109479200)

    GIVEN the MTBF data estimate MTBF from 25 to 55 degrees C.

                                        MTBF
                                        HDBK
      Obs      component      temp_c    217F (years)

        1    6GK6015-0AM23      25       23.0
        2    6GK6015-0AM23      25       29.0
        3    6GK6015-0AL11      55       95.0
        4    6GK6015-0AL11      55       95.1
      ...

     PREDICT (Arrhenius equation: lambda=lambda0*exp(A*(1/298-1/T))) 25 + 273 =298 kelvin

        HDBK_217F from 25 to 55 degrees (probably not a good idea to predict out side 25-55 because only two temps in data)

            component temp_c lambda_pred     MTBF_years

        6GK6015-0AM23     25    0.038981      25.7 = 1/0.038981  (minimize extremes?)
        6GK6015-0AM23     26    0.038023      26.3
        6GK6015-0AM23     27    0.037094      27.0
        6GK6015-0AM23     28    0.036194      27.6
        ,,,,

    ADJUSTMENTS needed to Siemens data (may not be the best data, but allows demonstrates a methodology)

      1 Because there are too many very different components, the input data
        was limited to just components with order number 6GK6015.
        Even then I not sure how different the order was.

      2 Since  6GK6015 has only 2 temperatures the model fit will be perfect. (R-sqared=1)

      3 It is ok to use the model for estimates from 25 to 55, but not ouside that range?

    CONTENTS

       1 Download from this repo mtbf.pdf or from Siemens
         [mtbf](https://support.industry.siemens.com/cs/document/109479200)
         https://support.industry.siemens.com/cs/document/109479200/mean-time-between-failures-(mtbf)-%E2%80%93-list-for-ruggedcom-products?dti=0&lc=en-US
       2 Convert pdf to text
       3 Text to slc dataset
       4 Model HDBK_217F

    /*       _                     _                 _             _  __            _   _      __      _       _
    / |   __| | _____      ___ __ | | ___   __ _  __| |  _ __   __| |/ _| _ __ ___ | |_| |__  / _|  __| | __ _| |_ __ _
    | |  / _` |/ _ \ \ /\ / / `_ \| |/ _ \ / _` |/ _` | | `_ \ / _` | |_ | `_ ` _ \| __| `_ \| |_  / _` |/ _` | __/ _` |
    | | | (_| | (_) \ V  V /| | | | | (_) | (_| | (_| | | |_) | (_| |  _|| | | | | | |_| |_) |  _|| (_| | (_| | || (_| |
    |_|  \__,_|\___/ \_/\_/ |_| |_|_|\___/ \__,_|\__,_| | .__/ \__,_|_|  |_| |_| |_|\__|_.__/|_|   \__,_|\__,_|\__\__,_|
                                                        |_|
    */

    [mtbf](https://support.industry.siemens.com/cs/document/109479200)
    MTBF_RUGGEDCOM_March_2026.pdf

    Place MTBF_RUGGEDCOM_March_2026.pdf in d:/pdf/MTBF_RUGGEDCOM_March_2026.pdf

    or download from this pdf

    /*___                                  _               _  __   _         _            _
    |___ \    ___ ___  _ ____   _____ _ __| |_   _ __   __| |/ _| | |_ ___  | |_ _____  _| |_
      __) |  / __/ _ \| `_ \ \ / / _ \ `__| __| | `_ \ / _` | |_  | __/ _ \ | __/ _ \ \/ / __|
     / __/  | (_| (_) | | | \ V /  __/ |  | |_  | |_) | (_| |  _| | || (_) || ||  __/>  <| |_
    |_____|  \___\___/|_| |_|\_/ \___|_|   \__| | .__/ \__,_|_|    \__\___/  \__\___/_/\_\\__|
                                                |_|
    */

    %utlfkil(d:/txt/all_lines.txt);

    options validvarname=v7;
    options set=RHOME "C:\Progra~1\R\R-4.5.2\bin\r";
    proc r;
    submit;
    library("tm")
    file <- "d:/pdf/MTBF_RUGGEDCOM_March_2026.pdf"
    Rpdf <- readPDF(control = list(text = "-layout"))
    corpus <- VCorpus(URISource(file),
          readerControl = list(reader = Rpdf))
    want <- content(content(corpus)[[1]])
    str(want)
    want
    all_lines <- trimws(gsub("\\s+", " ", strsplit(paste(want, collapse = ""), "\\n")[[1]]))
    writeLines(all_lines, "d:/txt/all_lines.txt")
    all_lines
    endsubmit;
    run;quit;

    /**************************************************************************************************************************/
    /* d:/txt/all_lines.txt                                                                                                   */
    /*                                                                                                                        */
    /*  Calculated Mean Time Between Failure Analysis                                                                         */
    /*                                                                                                                        */
    /* RUGGEDCOM routers and switch products are analyzed according to the MIL ...                                            */
    /* departments, federal agencies, and industry.                                                                           */
    /*                                                                         ...                                            */
    /* The MIL-HDBK-217F method for component selection, to verify reliability ...                                            */
    /* its detailed consideration of component and environment variables. As o ...                                            */
    /* favorable reliability predictions.                                                                                     */
    /*                                                                                                                        */
    /* Reliability predictions can only be used as a guide to improvement by c ...                                            */
    /* using different models/variables.                                                                                      */
    /*                                                                                                                        */
    /* Relex Reliability software is used to determine MTBF. The values calcul ...                                            */
    /* used in buildings without controlled temperatures.                                                                     */
    /*                                                                                                                        */
    /* Temperature MTBF (years)                                                                                               */
    /* Order Code / MLFB RUGGEDCOM System (Celcius) MIL-HDBK-217F                                                             */
    /*                                                                                                                        */
    /* 6GK6083-8AC23 RSG920P 55 14.2                                                                                          */
    /* 6GK6083-8AC23 RSG920P 25 25.2                                                                                          */
    /* 6GK6090-0AS11 RS900NC-24-D-MC-MC-MC-XX 55 17.5                                                                         */
    /* 6GK6090-0AS11 RS900NC-24-D-MC-MC-MC-XX 25 47.2                                                                         */
    /* 6GK6090-0AS13 RS900-HI 25 25                                                                                           */
    /* 6GK6090-0AS13 RS900-HI 55 18                                                                                           */
    /* 6GK6090-0GS2 RS900G-N-2SFP 25 19                                                                                       */
    /* 6GK6090-0PS20 RS900GP 55 15.5                                                                                          */
    /* 6GK6091-0AD21-0 RS910L-24-D-S1-TX01-XX 25 47                                                                           */
    /* RS910-24-D-S2-FX06-TX-XX RS910-24-D-S2-FX06-TX-XX 55 39                                                                */
    /* RS910-24-D-S2-FX06-TX-XX RS910-24-D-S2-FX06-TX-XX 25 78                                                                */
    /* 6GK6094-0GS23 RS940G-HI 25 23                                                                                          */
    /**************************************************************************************************************************/

    /*                             _     _
      ___ ___  _ ____   _____ _ __| |_  | | ___   __ _
     / __/ _ \| `_ \ \ / / _ \ `__| __| | |/ _ \ / _` |
    | (_| (_) | | | \ V /  __/ |  | |_  | | (_) | (_| |
     \___\___/|_| |_|\_/ \___|_|   \__| |_|\___/ \__, |
                                                 |___/
    */

    1                                          Altair SLC        13:06 Thursday, April 23, 2026

    NOTE: Copyright 2002-2025 World Programming, an Altair Company
    NOTE: Altair SLC 2026 (05.26.01.00.000758)
          Licensed to Roger DeAngelis
    NOTE: This session is executing on the X64_WIN11PRO platform and is running in 64 bit mode

    NOTE: AUTOEXEC processing beginning; file is C:\wpsoto\autoexec.sas
    NOTE: AUTOEXEC source line
    1       +  ï»¿ods _all_ close;
               ^
    ERROR: Expected a statement keyword : found "?"

    NOTE: AUTOEXEC processing completed

    1          options validvarname=v7;
    2         options set=RHOME "C:\Progra~1\R\R-4.5.2\bin\r";
    3         proc r;
    4         submit;
    5         library("tm")
    6         file <- "d:/pdf/MTBF_RUGGEDCOM_March_2026.pdf"
    7         Rpdf <- readPDF(control = list(text = "-layout"))
    8         corpus <- VCorpus(URISource(file),
    9               readerControl = list(reader = Rpdf))
    10        want <- content(content(corpus)[[1]])
    11        str(want)
    12        want
    13        all_lines <- trimws(gsub("\\s+", " ", strsplit(paste(want, collapse = ""), "\\n")[[1]]))
    14        writeLines(all_lines, "d:/txt/all_lines.txt")
    15        all_lines
    16        endsubmit;
    NOTE: Using R version 4.5.2 (2025-10-31 ucrt) from C:\Program Files\R\R-4.5.2

    NOTE: Submitting statements to R:

    > library("tm")
    Loading required package: NLP
    Warning message:
    package 'tm' was built under R version 4.5.3
    > file <- "d:/pdf/MTBF_RUGGEDCOM_March_2026.pdf"
    > Rpdf <- readPDF(control = list(text = "-layout"))
    > corpus <- VCorpus(URISource(file),
    +       readerControl = list(reader = Rpdf))
    > want <- content(content(corpus)[[1]])
    > str(want)
    > want
    > all_lines <- trimws(gsub("\\s+", " ", strsplit(paste(want, collapse = ""), "\\n")[[1]]))
    > writeLines(all_lines, "d:/txt/all_lines.txt")
    > all_lines

    NOTE: Processing of R statements complete

    17        run;quit;
    NOTE: Procedure r step took :
          real time : 2.111
          cpu time  : 0.031


    ERROR: Error printed on page 1

    NOTE: Submitted statements took :
          real time : 2.190
          cpu time  : 0.109

    /*____   _            _     _              _           _       _                 _
    |___ /  | |_ _____  _| |_  | |_ ___    ___| | ___   __| | __ _| |_ __ _ ___  ___| |_
      |_ \  | __/ _ \ \/ / __| | __/ _ \  / __| |/ __| / _` |/ _` | __/ _` / __|/ _ \ __|
     ___) | | ||  __/>  <| |_  | || (_) | \__ \ | (__ | (_| | (_| | || (_| \__ \  __/ |_
    |____/   \__\___/_/\_\\__|  \__\___/  |___/_|\___| \__,_|\__,_|\__\__,_|___/\___|\__|
    */

    libname works sas7bdat "d:/wpswrkx"; /*--- put this in your autoexec ---*/

    proc datasets lib=workx kill;
    run;

    options validvarname=v7;
    data workx.cleanup(where=(not missing(temp_c)));

      infile "d:/txt/all_lines.txt";
      input;

      if _infile_=: '6GK6015' ;

      component=substr(_infile_,1,13);
      temp_c=input(scan(_infile_,-2,' '),?? best.);
      hdbk_217f =input(scan(_infile_,-1,' '),?? best.);

      keep component temp_c hdbk_217f;
    run;quit;

    /**************************************************************************************************************************/
    /*  workx.cleanup total obs=61                                                                                            */
    /*                                   hdbk_                                                                                */
    /* Obs      component      temp_c     217f                                                                                */
    /*                                                                                                                        */
    /*   1    6GK6015-0AL20      55       81.0                                                                                */
    /*   2    6GK6015-0AL20      55        8.5                                                                                */
    /*   3    6GK6015-0AL13      55      115.0                                                                                */
    /*  ,,,                                                                                                                   */
    /*  59    6GK6015-1BM23      55        6.6                                                                                */
    /*  60    6GK6015-0CM26      55       24.6                                                                                */
    /*  61    6GK6015-0CM23      55       20.0                                                                                */
    /**************************************************************************************************************************/

    /*               _            _     _        _        _     _
    | | ___   __ _  | |_ _____  _| |_  | |_ ___ | |_ __ _| |__ | | ___
    | |/ _ \ / _` | | __/ _ \ \/ / __| | __/ _ \| __/ _` | `_ \| |/ _ \
    | | (_) | (_| | | ||  __/>  <| |_  | || (_) | || (_| | |_) | |  __/
    |_|\___/ \__, |  \__\___/_/\_\\__|  \__\___/ \__\__,_|_.__/|_|\___|
             |___/
    */

    1                                          Altair SLC        13:17 Thursday, April 23, 2026

    NOTE: Copyright 2002-2025 World Programming, an Altair Company
    NOTE: Altair SLC 2026 (05.26.01.00.000758)
          Licensed to Roger DeAngelis
    NOTE: This session is executing on the X64_WIN11PRO platform and is running in 64 bit mode

    NOTE: AUTOEXEC processing beginning; file is C:\wpsoto\autoexec.sas
    NOTE: AUTOEXEC source line
    1       +  ï»¿ods _all_ close;
               ^
    ERROR: Expected a statement keyword : found "?"

    NOTE: AUTOEXEC processing completed


    Altair SLC

    The DATASETS Procedure

             Directory

    Libref           WORKX
    Engine           SAS7BDAT
    Physical Name    d:\wpswrkx
    1         proc datasets lib=workx kill;
    NOTE: No matching members in directory
    2         run;
    3
    4         options validvarname=v7;
    NOTE: Procedure datasets step took :
          real time : 0.031
          cpu time  : 0.015


    5         data workx.cleanup(where=(not missing(temp_c)));
    6
    7           infile "d:/txt/all_lines.txt";
    8           input;
    9
    10          if _infile_=: '6GK6015' ;
    11
    12          component=substr(_infile_,1,13);
    13          temp_c=input(scan(_infile_,-2,' '),?? best.);
    14          hdbk_217f =input(scan(_infile_,-1,' '),?? best.);
    15
    16          keep component temp_c hdbk_217f;
    17        run;

    NOTE: The infile 'd:\txt\all_lines.txt' is:
          Filename='d:\txt\all_lines.txt',
          Owner Name=SLC\suzie,
          File size (bytes)=13758,
          Create Time=08:43:31 Apr 23 2026,
          Last Accessed=13:07:07 Apr 23 2026,
          Last Modified=13:06:22 Apr 23 2026,
          Lrecl=32767, Recfm=V

    NOTE: 319 records were read from file 'd:\txt\all_lines.txt'
          The minimum record length was 0
          The maximum record length was 242
    NOTE: Data set "WORKX.cleanup" has 61 observation(s) and 3 variable(s)
    NOTE: The data step took :
          real time : 0.015
          cpu time  : 0.015


    17      !     quit;
    ERROR: Error printed on page 1

    NOTE: Submitted statements took :
          real time : 0.189
          cpu time  : 0.140

    /*  _                         _      _  _         _ _     _      ____  _ _____ __
    | || |    _ __ ___   ___   __| | ___| || |__   __| | |__ | | __ |___ \/ |___  / _|
    | || |_  | `_ ` _ \ / _ \ / _` |/ _ \ || `_ \ / _` | `_ \| |/ /   __) | |  / / |_
    |__   _| | | | | | | (_) | (_| |  __/ || | | | (_| | |_) |   <   / __/| | / /|  _|
       |_|   |_| |_| |_|\___/ \__,_|\___|_||_| |_|\__,_|_.__/|_|\_\_|_____|_|/_/ |_|
                                                                 |___|
    */

    proc delete data=workx.mtbf_predictions_25_55C workx.mtbf_fit_parameters;
    run;quit;

    options validvarname=v7;
    options set=RHOME "C:\\Progra~1\\R\\R-4.5.2\\bin\\r";
    proc r;
      export data=workx.cleanup r=data;
      submit;
      library(ggplot2)

      # Calculate failure rates (failures per day) from MTBF in days
      data$lambda <- 1 / data$hdbk_217f  # Failures per day
      data$T_K <- data$temp_c + 273
      data$invT <- 1/data$T_K
      data$log_lambda <- log(data$lambda)

      # Store fits and create prediction dataframe
      fits <- list()
      results <- data.frame()

      for(comp in unique(data$component)) {
        comp_data <- data[data$component == comp, ]

        # Get data at 25°C and 55°C
        data_25 <- comp_data[comp_data$temp_c == 25, ]
        data_55 <- comp_data[comp_data$temp_c == 55, ]

        if(nrow(data_25) > 0 & nrow(data_55) > 0) {
          # Calculate average lambda at each temperature
          lambda25_avg <- mean(data_25$lambda)
          lambda55_avg <- mean(data_55$lambda)

          # Arrhenius: ln(lambda) = ln(lambda0) + (Ea/k) * (1/298 - 1/T)
          # Use the two points to solve for parameters
          T25_K <- 25 + 273  # 298K
          T55_K <- 55 + 273  # 328K

          invT25 <- 1/T25_K
          invT55 <- 1/T55_K

          # Calculate A (Ea/k) parameter
          A <- (log(lambda55_avg) - log(lambda25_avg)) / (invT55 - invT25)

          # Calculate lambda0 (failure rate at reference temperature 25°C = 298K)
          lambda0 <- lambda25_avg / exp(A * (1/298 - 1/T25_K))
          # Simplified: lambda0 = lambda25_avg since 1/298 - 1/298 = 0
          # So lambda0 should equal lambda25_avg
          lambda0 <- lambda25_avg

          # Store fit parameters
          fits[[comp]] <- list(lambda0 = lambda0, A = A)

          # Store results with MTBF in days
          results <- rbind(results, data.frame(
            component = comp,
            lambda0_25C = round(lambda0, 6),
            A = round(A),
            MTBF_25C = round(1/lambda25_avg, 1),  # Days
            MTBF_55C = round(1/lambda55_avg, 1)   # Days
          ))
        }
      }

      # CREATE PREDICTION DATAFRAME: 25 to 50°C in 1°C steps
      temps <- seq(25, 55, by=1)
      predictions <- data.frame()

      for(comp in names(fits)) {
        lambda0 <- fits[[comp]]$lambda0
        A <- fits[[comp]]$A

        for(temp in temps) {
          T_K <- temp + 273
          # Arrhenius equation: lambda = lambda0 * exp(A * (1/298 - 1/T))
          lambda_pred <- lambda0 * exp(A * (1/298 - 1/T_K))
          mtbf_pred_days <- 1 / lambda_pred  # MTBF in days

          predictions <- rbind(predictions, data.frame(
            component = comp,
            temp_c = temp,
            lambda_pred = round(lambda_pred, 6),
            MTBF_days = round(mtbf_pred_days, 1)  # Days with 1 decimal
          ))
        }
      }

      # Add the original data points to predictions for reference
      original_points <- data.frame(
        component = data$component,
        temp_c = data$temp_c,
        MTBF_days = data$hdbk_217f,
        type = "original"
      )

      # Print summary results
      print("=========================================")
      print("Fit Parameters (lambda in failures per day):")
      print("=========================================")
      print(results)

      print("=========================================")
      print("Predictions 25-50°C (MTBF in days):")
      print("=========================================")
      print(predictions)

    endsubmit;
    import r=predictions data=workx.mtbf_predictions_25_55C  ;
    import r=results     data=workx.mtbf_fit_parameters;
    run;



    /**************************************************************************************************************************/
    /* R                                                      | SLC                                                           */
    /* Arrhenius equation: lambda=lambda0*exp(A*(1/298-1/T))  |                                                               */
    /*                                                        |                                                               */
    /* [1] "========================================="        | WORKX.MTBF_FIT_PARAMETERS total obs=1 23APR2026:13:32:26      */
    /* [1] "Fit Parameters (lambda in failures per day):"     |                                                               */
    /* [1] "========================================="        |                    lambda0_                                   */
    /*       component lambda0_25C     A MTBF_25C MTBF_55C    | Obs   component       25C       A    MTBF_25C MTBF_55C        */
    /* 1 6GK6015-0AM23    0.038981 -2217     25.7       13    |                                                               */
    /*                                                        |  1  6GK6015-0AM23  0.038981   -2217    25.7      13           */
    /* [1] "========================================="        |                                                               */
    /* [1] "Predictions 25-50Â°C (MTBF in days):"             | WORKX.MTBF_PREDICTIONS_25_55C total obs=31                    */
    /* [1] "========================================="        |                                 lambda_    MTBF_  Observed    */
    /*        component temp_c lambda_pred MTBF_days          | Obs    component   temp_c      pred       days  Mean Days     */
    /*                                                        |                                                               */
    /* 1  6GK6015-0AM23     25    0.038981      25.7          |   1  6GK6015-0AM23   25      0.038981     25.7  26.0          */
    /* 2  6GK6015-0AM23     26    0.038023      26.3          |   2  6GK6015-0AM23   26      0.038023     26.3                */
    /* 3  6GK6015-0AM23     27    0.037094      27.0          |   3  6GK6015-0AM23   27      0.037094     27.0                */
    /* 4  6GK6015-0AM23     28    0.036194      27.6          |   4  6GK6015-0AM23   28      0.036194     27.6                */
    /* 5  6GK6015-0AM23     29    0.035322      28.3          |   5  6GK6015-0AM23   29      0.035322     28.3                */
    /* 6  6GK6015-0AM23     30    0.034477      29.0          |   6  6GK6015-0AM23   30      0.034477     29.0                */
    /* 7  6GK6015-0AM23     31    0.033657      29.7          |   7  6GK6015-0AM23   31      0.033657     29.7                */
    /* 8  6GK6015-0AM23     32    0.032862      30.4          |   8  6GK6015-0AM23   32      0.032862     30.4                */
    /* 9  6GK6015-0AM23     33    0.032090      31.2          |   9  6GK6015-0AM23   33      0.032090     31.2                */
    /* 10 6GK6015-0AM23     34    0.031342      31.9          |  10  6GK6015-0AM23   34      0.031342     31.9                */
    /* 11 6GK6015-0AM23     35    0.030615      32.7          |  11  6GK6015-0AM23   35      0.030615     32.7                */
    /* 12 6GK6015-0AM23     36    0.029910      33.4          |  12  6GK6015-0AM23   36      0.029910     33.4                */
    /* 13 6GK6015-0AM23     37    0.029226      34.2          |  13  6GK6015-0AM23   37      0.029226     34.2                */
    /* 14 6GK6015-0AM23     38    0.028561      35.0          |  14  6GK6015-0AM23   38      0.028561     35.0                */
    /* 15 6GK6015-0AM23     39    0.027916      35.8          |  15  6GK6015-0AM23   39      0.027916     35.8                */
    /* 16 6GK6015-0AM23     40    0.027290      36.6          |  16  6GK6015-0AM23   40      0.027290     36.6                */
    /* 17 6GK6015-0AM23     41    0.026681      37.5          |  17  6GK6015-0AM23   41      0.026681     37.5                */
    /* 18 6GK6015-0AM23     42    0.026089      38.3          |  18  6GK6015-0AM23   42      0.026089     38.3                */
    /* 19 6GK6015-0AM23     43    0.025515      39.2          |  19  6GK6015-0AM23   43      0.025515     39.2                */
    /* 20 6GK6015-0AM23     44    0.024956      40.1          |  20  6GK6015-0AM23   44      0.024956     40.1                */
    /* 21 6GK6015-0AM23     45    0.024413      41.0          |  21  6GK6015-0AM23   45      0.024413     41.0                */
    /* 22 6GK6015-0AM23     46    0.023885      41.9          |  22  6GK6015-0AM23   46      0.023885     41.9                */
    /* 23 6GK6015-0AM23     47    0.023372      42.8          |  23  6GK6015-0AM23   47      0.023372     42.8                */
    /* 24 6GK6015-0AM23     48    0.022873      43.7          |  24  6GK6015-0AM23   48      0.022873     43.7                */
    /* 25 6GK6015-0AM23     49    0.022388      44.7          |  25  6GK6015-0AM23   49      0.022388     44.7                */
    /* 26 6GK6015-0AM23     50    0.021916      45.6          |  26  6GK6015-0AM23   50      0.021916     45.6                */
    /* 27 6GK6015-0AM23     51    0.021456      46.6          |  27  6GK6015-0AM23   51      0.021456     46.6                */
    /* 28 6GK6015-0AM23     52    0.021009      47.6          |  28  6GK6015-0AM23   52      0.021009     47.6                */
    /* 29 6GK6015-0AM23     53    0.020574      48.6          |  29  6GK6015-0AM23   53      0.020574     48.6                */
    /* 30 6GK6015-0AM23     54    0.020150      49.6          |  30  6GK6015-0AM23   54      0.020150     49.6                */
    /* 31 6GK6015-0AM23     55    0.019738      50.7          |  31  6GK6015-0AM23   55      0.019738     50.7   55.6         */
    /**************************************************************************************************************************/

    /*                   _      _   _
     _ __ ___   ___   __| | ___| | | | ___   __ _
    | `_ ` _ \ / _ \ / _` |/ _ \ | | |/ _ \ / _` |
    | | | | | | (_) | (_| |  __/ | | | (_) | (_| |
    |_| |_| |_|\___/ \__,_|\___|_| |_|\___/ \__, |
                                            |___/
    */
    1                                          Altair SLC        13:34 Thursday, April 23, 2026

    NOTE: Copyright 2002-2025 World Programming, an Altair Company
    NOTE: Altair SLC 2026 (05.26.01.00.000758)
          Licensed to Roger DeAngelis
    NOTE: This session is executing on the X64_WIN11PRO platform and is running in 64 bit mode

    NOTE: AUTOEXEC processing beginning; file is C:\wpsoto\autoexec.sas
    NOTE: AUTOEXEC source line
    1       +  ï»¿ods _all_ close;

    NOTE: AUTOEXEC processing completed

    1         proc delete data=workx.mtbf_predictions_25_55C workx.mtbf_fit_parameters;
    2         run;quit;
    NOTE: Deleting "WORKX.MTBF_FIT_PARAMETERS" (memtype="DATA")
    NOTE: WORKX.MTBF_PREDICTIONS_25_55C (memtype="DATA") was not found, and has not been deleted
    NOTE: Procedure delete step took :
          real time : 0.000
          cpu time  : 0.000


    3
    4         options validvarname=v7;
    5         options set=RHOME "C:\\Progra~1\\R\\R-4.5.2\\bin\\r";
    6         proc r;
    NOTE: Using R version 4.5.2 (2025-10-31 ucrt) from C:\Program Files\R\R-4.5.2
    7           export data=workx.cleanup r=data;
    NOTE: Creating R data frame 'data' from data set 'WORKX.cleanup'

    8           submit;
    9           library(ggplot2)
    10
    11          # Calculate failure rates (failures per day) from MTBF in days
    12          data$lambda <- 1 / data$hdbk_217f  # Failures per day
    13          data$T_K <- data$temp_c + 273
    14          data$invT <- 1/data$T_K
    15          data$log_lambda <- log(data$lambda)
    16
    17          # Store fits and create prediction dataframe
    18          fits <- list()
    19          results <- data.frame()
    20
    21          for(comp in unique(data$component)) {
    22            comp_data <- data[data$component == comp, ]
    23
    24            # Get data at 25?C and 55?C
    25            data_25 <- comp_data[comp_data$temp_c == 25, ]
    26            data_55 <- comp_data[comp_data$temp_c == 55, ]
    27
    28            if(nrow(data_25) > 0 & nrow(data_55) > 0) {
    29              # Calculate average lambda at each temperature
    30              lambda25_avg <- mean(data_25$lambda)
    31              lambda55_avg <- mean(data_55$lambda)
    32
    33              # Arrhenius: ln(lambda) = ln(lambda0) + (Ea/k) * (1/298 - 1/T)
    34              # Use the two points to solve for parameters
    35              T25_K <- 25 + 273  # 298K
    36              T55_K <- 55 + 273  # 328K
    37
    38              invT25 <- 1/T25_K
    39              invT55 <- 1/T55_K
    40
    41              # Calculate A (Ea/k) parameter
    42              A <- (log(lambda55_avg) - log(lambda25_avg)) / (invT55 - invT25)
    43
    44              # Calculate lambda0 (failure rate at reference temperature 25?C = 298K)
    45              lambda0 <- lambda25_avg / exp(A * (1/298 - 1/T25_K))
    46              # Simplified: lambda0 = lambda25_avg since 1/298 - 1/298 = 0
    47              # So lambda0 should equal lambda25_avg
    48              lambda0 <- lambda25_avg
    49
    50              # Store fit parameters
    51              fits[[comp]] <- list(lambda0 = lambda0, A = A)
    52
    53              # Store results with MTBF in days
    54              results <- rbind(results, data.frame(
    55                component = comp,
    56                lambda0_25C = round(lambda0, 6),
    57                A = round(A),
    58                MTBF_25C = round(1/lambda25_avg, 1),  # Days
    59                MTBF_55C = round(1/lambda55_avg, 1)   # Days
    60              ))
    61            }
    62          }
    63
    64          # CREATE PREDICTION DATAFRAME: 25 to 50?C in 1?C steps
    65          temps <- seq(25, 55, by=1)
    66          predictions <- data.frame()
    67
    68          for(comp in names(fits)) {
    69            lambda0 <- fits[[comp]]$lambda0
    70            A <- fits[[comp]]$A
    71
    72            for(temp in temps) {
    73              T_K <- temp + 273
    74              # Arrhenius equation: lambda = lambda0 * exp(A * (1/298 - 1/T))
    75              lambda_pred <- lambda0 * exp(A * (1/298 - 1/T_K))
    76              mtbf_pred_days <- 1 / lambda_pred  # MTBF in days
    77
    78              predictions <- rbind(predictions, data.frame(
    79                component = comp,
    80                temp_c = temp,
    81                lambda_pred = round(lambda_pred, 6),
    82                MTBF_days = round(mtbf_pred_days, 1)  # Days with 1 decimal
    83              ))
    84            }
    85          }
    86
    87          # Add the original data points to predictions for reference
    88          original_points <- data.frame(
    89            component = data$component,
    90            temp_c = data$temp_c,
    91            MTBF_days = data$hdbk_217f,
    92            type = "original"
    93          )
    94
    95          # Print summary results
    96          print("=========================================")
    97          print("Fit Parameters (lambda in failures per day):")
    98          print("=========================================")
    99          print(results)
    100
    101         print("=========================================")
    102         print("Predictions 25-50?C (MTBF in days):")
    103         print("=========================================")
    104         print(predictions)
    105
    106       endsubmit;

    NOTE: Submitting statements to R:

    >   library(ggplot2)
    Warning message:
    package 'ggplot2' was built under R version 4.5.3
    >
    >   # Calculate failure rates (failures per day) from MTBF in days
    >   data$lambda <- 1 / data$hdbk_217f  # Failures per day
    >   data$T_K <- data$temp_c + 273
    >   data$invT <- 1/data$T_K
    >   data$log_lambda <- log(data$lambda)
    >
    >   # Store fits and create prediction dataframe
    >   fits <- list()
    >   results <- data.frame()
    >
    >   for(comp in unique(data$component)) {
    +     comp_data <- data[data$component == comp, ]
    +
    +     # Get data at 25?C and 55?C
    +     data_25 <- comp_data[comp_data$temp_c == 25, ]
    +     data_55 <- comp_data[comp_data$temp_c == 55, ]
    +
    +     if(nrow(data_25) > 0 & nrow(data_55) > 0) {
    +       # Calculate average lambda at each temperature
    +       lambda25_avg <- mean(data_25$lambda)
    +       lambda55_avg <- mean(data_55$lambda)
    +
    +       # Arrhenius: ln(lambda) = ln(lambda0) + (Ea/k) * (1/298 - 1/T)
    +       # Use the two points to solve for parameters
    +       T25_K <- 25 + 273  # 298K
    +       T55_K <- 55 + 273  # 328K
    +
    +       invT25 <- 1/T25_K
    +       invT55 <- 1/T55_K
    +
    +       # Calculate A (Ea/k) parameter
    +       A <- (log(lambda55_avg) - log(lambda25_avg)) / (invT55 - invT25)
    +
    +       # Calculate lambda0 (failure rate at reference temperature 25?C = 298K)
    +       lambda0 <- lambda25_avg / exp(A * (1/298 - 1/T25_K))
    +       # Simplified: lambda0 = lambda25_avg since 1/298 - 1/298 = 0
    +       # So lambda0 should equal lambda25_avg
    +       lambda0 <- lambda25_avg
    +
    +       # Store fit parameters
    +       fits[[comp]] <- list(lambda0 = lambda0, A = A)
    +
    +       # Store results with MTBF in days
    +       results <- rbind(results, data.frame(
    +         component = comp,
    +         lambda0_25C = round(lambda0, 6),
    +         A = round(A),
    +         MTBF_25C = round(1/lambda25_avg, 1),  # Days
    +         MTBF_55C = round(1/lambda55_avg, 1)   # Days
    +       ))
    +     }
    +   }
    >
    >   # CREATE PREDICTION DATAFRAME: 25 to 50?C in 1?C steps
    >   temps <- seq(25, 55, by=1)
    >   predictions <- data.frame()
    >
    >   for(comp in names(fits)) {
    +     lambda0 <- fits[[comp]]$lambda0
    +     A <- fits[[comp]]$A
    +
    +     for(temp in temps) {
    +       T_K <- temp + 273
    +       # Arrhenius equation: lambda = lambda0 * exp(A * (1/298 - 1/T))
    +       lambda_pred <- lambda0 * exp(A * (1/298 - 1/T_K))
    +       mtbf_pred_days <- 1 / lambda_pred  # MTBF in days
    +
    +       predictions <- rbind(predictions, data.frame(
    +         component = comp,
    +         temp_c = temp,
    +         lambda_pred = round(lambda_pred, 6),
    +         MTBF_days = round(mtbf_pred_days, 1)  # Days with 1 decimal
    +       ))
    +     }
    +   }
    >
    >   # Add the original data points to predictions for reference
    >   original_points <- data.frame(
    +     component = data$component,
    +     temp_c = data$temp_c,
    +     MTBF_days = data$hdbk_217f,
    +     type = "original"
    +   )
    >
    >   # Print summary results
    >   print("=========================================")
    >   print("Fit Parameters (lambda in failures per day):")
    >   print("=========================================")
    >   print(results)
    >
    >   print("=========================================")
    >   print("Predictions 25-50?C (MTBF in days):")
    >   print("=========================================")
    >   print(predictions)
    >

    NOTE: Processing of R statements complete

    107       import r=predictions data=workx.mtbf_predictions_25_55C  ;
    NOTE: Creating data set 'WORKX.mtbf_predictions_25_55C' from R data frame 'predictions'
    NOTE: Data set "WORKX.mtbf_predictions_25_55C" has 31 observation(s) and 4 variable(s)

    108       import r=results     data=workx.mtbf_fit_parameters;
    NOTE: Creating data set 'WORKX.mtbf_fit_parameters' from R data frame 'results'
    NOTE: Data set "WORKX.mtbf_fit_parameters" has 1 observation(s) and 5 variable(s)

    109       run;
    NOTE: Procedure r step took :
          real time : 1.604
          cpu time  : 0.031


    110
    ERROR: Error printed on page 1

    NOTE: Submitted statements took :
          real time : 1.683
          cpu time  : 0.109

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
