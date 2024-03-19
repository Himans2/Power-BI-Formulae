# Power-BI-Formulae


**In this Repository i will be writing the all DAX formulaes which i have used so far in Power BI.The main purpose of this repository is to make available all the Power BI related DAXs in one Place.**
 
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Year Related Formulaes**
**These formulaes are according to the Fiscal Year**

**Current Year Sales** =   CALCULATE([Sales],FILTER('Calendar Master','Calendar Master'[Fiscal Year]=MAX('Calendar Master'[Fiscal Year])
 && 'Calendar Master'[Month Sort Order] <= MAX('Calendar Master'[Month Sort Order]) && 'Calendar Master'[Date] <= max('Calendar Master'[Date])))

month sort order is nothing but the month number starting from 1 for april and so on


**Previous Year Sales** = Calculate([Sales],Sameperiodlastyear('Calendar Master'[Date]))

**Previous Year Sales Till Date** = 
var maxDate = 
DATE(YEAR(MAX('Calendar Master'[Date]))-1,MONTH(MAX('Calendar Master'[Date])), DAY(MAX('Calendar Master'[Date])))
var minDate = 
DATE(YEAR(MIN('Calendar Master'[Date]))-1,MONTH(MIN('Calendar Master'[Date])), DAY(MIN('Calendar Master'[Date])))
return
CALCULATE([Sales], FILTER(ALL('Calendar Master'), 
'Calendar Master'[Date] >= minDate && 'Calendar Master'[Date] <= maxDate))


**MTD Net Sales** = CALCULATE([Sales],FILTER('Calendar Master','Calendar Master'[Month Sort Order]=MAX('Calendar Master'[Month Sort Order])))


**If i have the current date till 12 march then it will calculate the previous month till date 12 feb and will compare the sales.So i have designed this formula according to that need**

**Previous Month Till Date Net Sales** = 

Var a = CALCULATE(MIN('Calendar Master'[Date]),FILTER(ALL('Calendar Master'), 'Calendar Master'[Fiscal Year] = MAX('Calendar Master'[Fiscal Year]) && 'Calendar Master'[Month Index] = MAX('Calendar Master'[Month Index])-1))
Var c = CALCULATE(MAX('Calendar Master'[Date]),ALL('Calendar Master'))
Var him=DATE(YEAR(c),MONTH(c)-1,DAY(c))
var d =CALCULATE(MAX('Calendar Master'[Date]),FILTER(ALL('Calendar Master'), 'Calendar Master'[Fiscal Year] = MAX('Calendar Master'[Fiscal Year]) && 'Calendar Master'[Month Index] = MAX('Calendar Master'[Month Index])-1))
Var e = IF(MONTH(MAX('Calendar Master'[Date]))=MONTH(TODAY()),c,d)
var f = CALCULATE([Income],PREVIOUSMONTH('Calendar Master'[Date]))
var g = CALCULATE([Income], FILTER(ALL('Calendar Master'), 
'Calendar Master'[Date] >= a && 'Calendar Master'[Date] <= him))

return
IF(MONTH(MAX('Calendar Master'[Date])) =MONTH(c),g,f)

**QTD Net Sales** = CALCULATE([Sales],FILTER('Calendar Master','Calendar Master'[Fiscal Qtr]=MAX('Calendar Master'[Fiscal Qtr])))


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Consider a scenario if you have the condition that you have to choose the dynamic dimension and measures and you have made the Top N filter using parameter and now you want if i select TopN filter as 5 then should give me dept and particular measure for the same and now how these filters will work if you choose dynamic dimension and measures then you have to use this formula** 

Rank Formula = 

SWITCH (
    TRUE (),
    SELECTEDVALUE ('field parameter')
        = "Table Name'[Column Name]",
        RANKX (
            ALLSELECTED ('Table Name'[Column Name]),
            [Regular Income],
            ,
            DESC
        ),
    SELECTEDVALUE ('field parameter')
        = "Table Name'[Column Name]",
        RANKX (
            ALLSELECTED ('Table Name'[Column Name]),
            [Regular Income],
            ,
            DESC

)
)


**Pareto Formula** 

 Pareto = 
VAR _total = CALCULATE([Income],ALLSELECTED('Table Name'[Column Name]))
VAR _current = [Income]
VAR _sumTable = SUMMARIZE(ALLSELECTED(Table Name),'Table Name'[Column Name],"Name the column",[Income])
VAR _cumulativeSum = SUMX(FILTER(_sumTable,[Income]>=_current),[Income])


RETURN 
DIVIDE(_cumulativeSum,_total)

**Consider a scenario of creating dynamic pareto then how you can create the scenaio for the same using DAX**

Selected Pareto = SWITCH(TRUE(), SELECTEDVALUE(Dimensions1[dimension value]) = 0,[Sales Pareto],
SELECTEDVALUE(Dimensions1[dimension value]) = 1,[Profit Pareto],
SELECTEDVALUE(Dimensions1[dimension value]) = 2,[Loss Pareto],
SELECTEDVALUE(Dimensions1[dimension value]) = 3,[Discount Pareto],
[Sales Pareto])-- this is by default you are giving sales pareto 


**If you want arrow as up and down simple then use this formula for considering the unichar**


Arrow = 

IF(ISBLANK([TableName[ColumnName]]), BLANK(), 
    IF(TableName[ColumnName] >= 0, 
        UNICHAR(129153) ,
        UNICHAR(129155) ))

**Moving Average with Custom Period**


Moving Avg = CALCULATE(
                 AVERAGE([YourMeasure]),
                 FILTER(
                     ALL('Date'),
                     'Date'[Date] >= MAX('Date'[Date]) - 30 &&
                     'Date'[Date] <= MAX('Date'[Date])
                 )
             )


**Rolling 12 months Average:**

Rolling 12 Months Avg = AVERAGEX(
                          DATESINPERIOD('Date'[Date], LASTDATE('Date'[Date]), -12, MONTH),
                          [YourMeasure]
                       )











