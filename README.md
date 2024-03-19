


# **Power BI Formulas**

**In this repository, I will be compiling all the DAX formulas I have used so far in Power BI. The main purpose of this repository is to provide a centralized location for all Power BI-related DAX formulas.**

---

## Year-Related Formulas
*(These formulas are according to the Fiscal Year)*

### Current Year Sales
```DAX
= CALCULATE(
    [Sales],
    FILTER(
        'Calendar Master',
        'Calendar Master'[Fiscal Year] = MAX('Calendar Master'[Fiscal Year])
        && 'Calendar Master'[Month Sort Order] <= MAX('Calendar Master'[Month Sort Order])
        && 'Calendar Master'[Date] <= MAX('Calendar Master'[Date])
    )
)
(Explanation: This formula calculates the sales for the current fiscal year up to the current date.)

**Previous Year Sales
DAX**

= CALCULATE(
    [Sales],
    SAMEPERIODLASTYEAR('Calendar Master'[Date])
)
(Explanation: This formula calculates the sales for the same period in the previous year.)

**Previous Year Sales Till Date**
DAX

= VAR maxDate = DATE(YEAR(MAX('Calendar Master'[Date])) - 1, MONTH(MAX('Calendar Master'[Date])), DAY(MAX('Calendar Master'[Date])))
VAR minDate = DATE(YEAR(MIN('Calendar Master'[Date])) - 1, MONTH(MIN('Calendar Master'[Date])), DAY(MIN('Calendar Master'[Date])))
RETURN
    CALCULATE(
        [Sales],
        FILTER(
            ALL('Calendar Master'),
            'Calendar Master'[Date] >= minDate && 'Calendar Master'[Date] <= maxDate
        )
    )
(Explanation: This formula calculates the sales for the same period in the previous year up to the current date.)

**MTD Net Sales**
DAX

= CALCULATE(
    [Sales],
    FILTER(
        'Calendar Master',
        'Calendar Master'[Month Sort Order] = MAX('Calendar Master'[Month Sort Order])
    )
)
(Explanation: This formula calculates the Month-to-Date (MTD) net sales.)

**Previous Month Till Date Net Sales**
DAX

= VAR a = CALCULATE(MIN('Calendar Master'[Date]), FILTER(ALL('Calendar Master'), 'Calendar Master'[Fiscal Year] = MAX('Calendar Master'[Fiscal Year]) && 'Calendar Master'[Month Index] = MAX('Calendar Master'[Month Index]) - 1))
VAR c = CALCULATE(MAX('Calendar Master'[Date]), ALL('Calendar Master'))
VAR him = DATE(YEAR(c), MONTH(c) - 1, DAY(c))
VAR d = CALCULATE(MAX('Calendar Master'[Date]), FILTER(ALL('Calendar Master'), 'Calendar Master'[Fiscal Year] = MAX('Calendar Master'[Fiscal Year]) && 'Calendar Master'[Month Index] = MAX('Calendar Master'[Month Index]) - 1))
VAR e = IF(MONTH(MAX('Calendar Master'[Date])) = MONTH(TODAY()), c, d)
VAR f = CALCULATE([Income], PREVIOUSMONTH('Calendar Master'[Date]))
VAR g = CALCULATE([Income], FILTER(ALL('Calendar Master'), 'Calendar Master'[Date] >= a && 'Calendar Master'[Date] <= him))

RETURN
    IF(MONTH(MAX('Calendar Master'[Date])) = MONTH(c), g, f)
(Explanation: This formula calculates the net sales from the previous month up to the same date as today.)

**QTD Net Sales**
DAX

= CALCULATE(
    [Sales],
    FILTER(
        'Calendar Master',
        'Calendar Master'[Fiscal Qtr] = MAX('Calendar Master'[Fiscal Qtr])
    )
)
(Explanation: This formula calculates the Quarter-to-Date (QTD) net sales.)

**Dynamic Measure and Dimension Selection**
Rank Formula
DAX

= SWITCH (
    TRUE (),
    SELECTEDVALUE ('field parameter') = "Table Name'[Column Name]",
    RANKX (
        ALLSELECTED ('Table Name'[Column Name]),
        [Regular Income],
        ,
        DESC
    ),
    SELECTEDVALUE ('field parameter') = "Table Name'[Column Name]",
    RANKX (
        ALLSELECTED ('Table Name'[Column Name]),
        [Regular Income],
        ,
        DESC
    )
)
(Explanation: This formula calculates the rank based on the selected measure and dimension.)

**Pareto Formula**
DAX

= VAR _total = CALCULATE([Income], ALLSELECTED('Table Name'[Column Name]))
VAR _current = [Income]
VAR _sumTable = SUMMARIZE(ALLSELECTED(Table Name), 'Table Name'[Column Name], "Name the column", [Income])
VAR _cumulativeSum = SUMX(FILTER(_sumTable, [Income] >= _current), [Income])

RETURN
    DIVIDE(_cumulativeSum, _total)
(Explanation: This formula calculates the Pareto analysis for the selected measure and dimension.)

**Selected Pareto**
DAX

= SWITCH(TRUE(), 
    SELECTEDVALUE(Dimensions1[dimension value]) = 0, [Sales Pareto],
    SELECTEDVALUE(Dimensions1[dimension value]) = 1, [Profit Pareto],
    SELECTEDVALUE(Dimensions1[dimension value]) = 2, [Loss Pareto],
    SELECTEDVALUE(Dimensions1[dimension value]) = 3, [Discount Pareto],
    [Sales Pareto]
)
(Explanation: This formula dynamically selects the appropriate Pareto analysis based on the selected dimension.)

**Arrow**
DAX

= IF(
    ISBLANK([TableName[ColumnName]]),
    BLANK(), 
    IF(
        TableName[ColumnName] >= 0,
        UNICHAR(129153),
        UNICHAR(129155)
    )
)
(Explanation: This formula generates an arrow (up or down) based on the value of a column.)

**Moving Average with Custom Period**
DAX

= CALCULATE(
    AVERAGE([YourMeasure]),
    FILTER(
        ALL('Date'),
        'Date'[Date] >= MAX('Date'[Date]) - 30 &&
        'Date'[Date] <= MAX('Date'[Date])
    )
)
(Explanation: This formula calculates the moving average with a custom period of 30 days.)

**Rolling 12 Months Average**
DAX

= AVERAGEX(
    DATESINPERIOD('Date'[Date], LASTDATE('Date'[Date]), -12, MONTH),
    [YourMeasure]
)
(Explanation: This formula calculates the rolling 12-month average for a measure.)


