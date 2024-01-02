

| Sales Dashboard | Data Model Image |
| --- | --- |
| ![Sales Dashboard](images/SalesDashboard.png) | ![Data Model Image](images/datamodel.png) |

| SQL Statements | CSV |
| --- | --- |
| ![Sql](images/sql.png) | ![csv](images/customercsc.png) |

# Business Request & User Stories

The business request for this data analyst project was an executive sales report for sales managers. Based on the request that was made from the business, the following user stories were defined to fulfill delivery and ensure that acceptance criteria were maintained throughout the project.

## User Stories

| # | Role               | Request                                            | User Value                                             | Acceptance Criteria                                  |
|---|--------------------|----------------------------------------------------|--------------------------------------------------------|------------------------------------------------------|
| 1 | Sales Manager      | To get a dashboard overview of internet sales      | Can follow better which customers and products sell the best | A Power BI dashboard which updates data once a day   |
| 2 | Sales Representative | A detailed overview of Internet Sales per Customers | Can follow up my customers that buy the most and who we can sell more to | A Power BI dashboard which allows me to filter data for each customer |
| 3 | Sales Representative | A detailed overview of Internet Sales per Products  | Can follow up my Products that sell the most             | A Power BI dashboard which allows me to filter data for each Product |
| 4 | Sales Manager       | A dashboard overview of internet sales             | Follow sales over time against budget                    | A Power Bi dashboard with graphs and KPIs comparing against budget |

## Data Cleansing & Transformation (SQL)
The following tables were retrieved using SQL in order to generate the required data model for conducting analysis and meeting the business needs outlined in the user stories.
Sales budgets, one data source, were supplied in Excel format and, at a later stage of the procedure, were linked in the data model.
The SQL statements for transforming and cleaning the required data are listed below.

### DIM_Calendar:

<pre>
<code>
-- Cleansed DIM_Date Table --
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  -- [DayNumberOfWeek], 
  [EnglishDayNameOfWeek] AS Day, 
  -- [SpanishDayNameOfWeek], 
  -- [FrenchDayNameOfWeek], 
  -- [DayNumberOfMonth], 
  -- [DayNumberOfYear], 
  -- [WeekNumberOfYear],
  [EnglishMonthName] AS Month, 
  Left([EnglishMonthName], 3) AS MonthShort,   -- Useful for front end date navigation and front end graphs.
  -- [SpanishMonthName], 
  -- [FrenchMonthName], 
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year -- [CalendarSemester], 
  -- [FiscalQuarter], 
  -- [FiscalYear], 
  -- [FiscalSemester] 
FROM 
  [AdventureWorksDW2019].[dbo].[DimDate]
WHERE 
  CalendarYear >= 2019;
</code>
</pre>

### DIM_Customers:

<pre>
  <code>
    -- Cleansed DIM_Customer's Tables -- 
SELECT 
  c.customerkey AS CustomerKey, 
  -- ,[GeographyKey]
  --,[CustomerAlternateKey]
  --,[Title]
  c.firstname AS [FirstName], 
  --  ,[MiddleName]
  c.lastname AS [LastName], 
  c.firstname + ' ' + lastname AS [Full Name], -- Combined First and LastName
  -- ,[NameStyle]
  -- ,[BirthDate]
  -- ,[MaritalStatus]
  -- ,[Suffix]
  case c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender, 
  --,[EmailAddress]
  -- ,[YearlyIncome]
  -- ,[TotalChildren]<
  -- ,[NumberChildrenAtHome]
  -- ,[EnglishEducation]
  -- ,[SpanishEducation]
  -- ,[FrenchEducation]
  --,[EnglishOccupation]
  -- ,[SpanishOccupation]
  -- ,[FrenchOccupation]
  -- ,[HouseOwnerFlag]
  --,[NumberCarsOwned]
  -- ,[AddressLine1]
  --  ,[AddressLine2]
  -- ,[Phone]
  [DateFirstPurchase], 
  -- ,[CommuteDistance]
  g.City AS [Customer City]  -- Joined Customer City from Geography Table
FROM 
  dbo.DimCustomer AS c 
  LEFT JOIN dbo.DimGeography AS g ON g.GeographyKey = c.GeographyKey
ORDER BY
	CustomerKey ASC -- Order List by CustomerKey
  </code>
</pre>

### DIM_Products

<pre>
  <code>
    -- Cleansed DIM_Products Table --
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS [ProductItemCode], 
  --,[ProductSubcategoryKey]
  --,[WeightUnitMeasureCode]
  --,[SizeUnitMeasureCode]
  p.[EnglishProductName] AS [ProductName], 
  ps.[EnglishProductSubcategoryName] AS [Sub Category], 
  pc.[EnglishProductCategoryName] AS [Product Category], 
  --,[SpanishProductName]
  --,[FrenchProductName]
  --,[StandardCost]
  --,[FinishedGoodsFlag]
  p.[Color] AS [Product Color], 
  --,[SafetyStockLevel]
  --,[ReorderPoint]
  --,[ListPrice]
  p.[Size] AS [Product Size], 
  --,[SizeRange]
  --,[Weight]
  --,[DaysToManufacture]
  p.[ProductLine] AS [Product Line], 
  -- ,[DealerPrice]
  --,[Class]
  --,[Style]
  p.[ModelName] AS [Product Model Name], 
  --,[LargePhoto]
  p.[EnglishDescription] AS [Product Description], 
  --,[FrenchDescription]
  --,[ChineseDescription]
  --,[ArabicDescription]
  --,[HebrewDescription]
  --,[ThaiDescription]
  --,[GermanDescription]
  --,[JapaneseDescription]
  --,[TurkishDescription]
  --,[StartDate]
  --,[EndDate]
  ISNULL(p.Status, 'Outdated') AS [Product Status] 
FROM 
  dbo.DimProduct AS p 
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubCategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON pc.ProductCategoryKey = pc.ProductCategoryKey 
ORDER BY 
  p.ProductKey ASC
  </code>
</pre>

### FactInternetSales
<pre>
  <code>
    SELECT [ProductKey]
      ,[OrderDateKey]
      ,[DueDateKey]
      ,[ShipDateKey]
      ,[CustomerKey]
      --,[PromotionKey]
      --,[CurrencyKey]
      --,[SalesTerritoryKey]
      ,[SalesOrderNumber]
      --,[SalesOrderLineNumber]
      --,[RevisionNumber]
      --,[OrderQuantity]
      --,[UnitPrice]
     -- ,[ExtendedAmount]
     -- ,[UnitPriceDiscountPct]
     -- ,[DiscountAmount]
     -- ,[ProductStandardCost]
      --,[TotalProductCost]
      ,[SalesAmount]
      --,[TaxAmt]
      --,[Freight]
      --,[CarrierTrackingNumber]
      --,[CustomerPONumber]
      --,[OrderDate]
      --,[DueDate]
      --,[ShipDate]
  FROM [AdventureWorksDW2019].[dbo].[FactInternetSales]
  WHERE OrderDateKey >= CONVERT(VARCHAR, DATEADD(YEAR, -2, GETDATE()), 112)

  ORDER BY 
  OrderDateKey ASC

  </code>
</pre>
