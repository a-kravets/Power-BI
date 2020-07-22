# DATE TABLE

- Date = CALENDARAUTO()
- YearMonth = FORMAT('Date'[Date], "YYYY-MM")
- Year = YEAR('Date'[Date])
- Quarter = "Q" & FORMAT(QUARTER('Date'[Date]), "")
- Month number = MONTH('Date'[Date])
- Month = FORMAT('Date'[Date], "MMM")
- Week number = WEEKNUM('Date'[Date])
- Day = DAY('Date'[Date])
- DateWithTransactions = 'Date'[Date] <= MAX(transactions[Date])


# METRICS

**Transactions**

```Transactions Amount = SUM(transactions[Amount])```

**Budget**

```
Budget Amnt YTD = 
CALCULATE(
    [Budget Amt],
    CALCULATETABLE(
        DATESYTD('Date'[Date]),
        'Date'[DateWithTransactions] = TRUE
        )
    )
```
```
Budget Amt = 
  CALCULATE(
    SUM('Monthly Budget'[Budget]),
    USERELATIONSHIP('Date'[YearMonth], 'Monthly Budget'[YearMonth])
  )
```
```
Budget Remainder (%) = DIVIDE([Budget Remainder ($)], [Budget Amt], 0)
```
```
Budget Remainder ($) = [Budget Amt] - [Expenses]
```
```
Budget Period = "Expenses vs. Budget for " &
  SELECTEDVALUE('Date'[Month]) &
  SELECTEDVALUE('Date'[Year])
```

**Expenses**

```
Expenses = CALCULATE(
    [Transactions Amount],
    FILTER(transactions, transactions[Type] = "debit"),
    Category[Category] <> "transfer",
    Category[Category] <> "Credit Card Payment"
)
```
```
Expenses PY = 
    CALCULATE([Expenses],
    SAMEPERIODLASTYEAR('Date'[Date])
    )
```
```
Expenses PY YTD = 
    CALCULATE([Expenses YTD], SAMEPERIODLASTYEAR('Date'[Date]))
```

**Income**

```
Income = CALCULATE(
    [Transactions Amount],
    FILTER(transactions, transactions[Type] = "credit"),
    Category[Category] <> "transfer",
    Category[Category] <> "Credit Card Payment"
)
```
```
Income PY = 
  CALCULATE([Income],
  SAMEPERIODLASTYEAR('Date'[Date])
  )
```
```
Income PY YTD = CALCULATE([Income YTD], SAMEPERIODLASTYEAR('Date'[Date]))
```
```
Income YTD = 
CALCULATE(
    [Income],
    CALCULATETABLE(
        DATESYTD('Date'[Date]),
        'Date'[DateWithTransactions] = TRUE
        )
    )
```

**Net Income**

```
Net Income = [Income] - [Expenses]
```

```
Net Income YTD = 
CALCULATE(
    [Net Income],
    CALCULATETABLE(
        DATESYTD('Date'[Date]),
        'Date'[DateWithTransactions] = TRUE
        )
    )
```

# MONTHLY BUDGET TABLE

```Monthly Budget = CROSSJOIN(SUMMARIZE('Date', 'Date'[YearMonth]), Budget)```
