### Question 1: How is the total revenue of the web site break down to month for the most recent year, and the ratio of the month revenue to the year revenue? 
- Data is access from the cleaned_data table
- The querry will create a table with these collumn:
	- mothh: all the month of the most recent year
   	- year: the most recent year
   	- monthlyrevenue:  monthly revenue for each month of the most recent year
   	- revenueratio: ratio of monthly revenue for each month compare to the most recent year
- The most recent year will be selected by this querry
```
SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data
```
- The monthly revenue for each month of the most recent year is calculated in the SELECT clause
```
SUM(productquantity*productprice)::float(2)	AS monthlyrevenue
```
- The total revenue of the most recent year is calculated
```
SELECT	SUM(productquantity*productprice)
FROM	public.cleaned_data
WHERE	EXTRACT(year FROM	ordereddate) = (SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data)
```
- The ratio is then calculated by this querry
```
SUM(productquantity*productprice)/
	(
		SELECT	SUM(productquantity*productprice)
		FROM	public.cleaned_data
		WHERE	EXTRACT(year FROM	ordereddate) = (SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data)
	)*100	::	float(2)	AS	revenueratio
```
- The GROUP BY is used to group all the revenue for the sum by month and year, and then sort the result by monthly revenue
```
GROUP BY	TO_CHAR(ordereddate,'Month'),EXTRACT(year FROM	ordereddate)
ORDER BY	monthlyrevenue	DESC
```
- Final SQL Queries:	
```
SELECT 	TO_CHAR(ordereddate,'Month')	AS	month,
	EXTRACT(year FROM	ordereddate)	AS year,
	SUM(productquantity*productprice)::float(2)	AS monthlyrevenue,
	SUM(productquantity*productprice)/
	(
		SELECT	SUM(productquantity*productprice)
		FROM	public.cleaned_data
		WHERE	EXTRACT(year FROM	ordereddate) = (SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data)
	)*100	::	float(2)	AS	revenueratio
FROM	public.cleaned_data
WHERE	EXTRACT(year FROM	ordereddate) = (SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data)
GROUP BY	TO_CHAR(ordereddate,'Month'),EXTRACT(year FROM	ordereddate)
ORDER BY	monthlyrevenue	DESC
```

![Result table](https://live.staticflickr.com/65535/53150801124_8224b0ac10.jpg)

### Question 2: Best selling category by month for most recent year?
- Data is access from the cleaned_data table
- The most recent year will be selected by this querry
```
SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data
```
- The cte is create to extract the month in number, the month, the year, product category, and calculated the total product sold for each product category for each month of the most recent year
```
WITH cte	AS(
SELECT	EXTRACT(month FROM	ordereddate)	AS	monthnumber,
	TO_CHAR(ordereddate,'Month') AS month,
	EXTRACT(year FROM	ordereddate)	AS year,
	productcategory,
	SUM(productquantity)	categorysale
FROM	public.cleaned_data
WHERE	EXTRACT(year FROM	ordereddate) = (SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data)
GROUP BY	EXTRACT(month FROM	ordereddate),
		TO_CHAR(ordereddate,'Month'),
		EXTRACT(year FROM	ordereddate),
		productcategory
),
```
- The cte2 is create to find the category that have the most sale for each month of the most recent year
```
cte2	AS(
SELECT	month,
	MAX(categorysale)	AS	maxcategorysale
FROM	cte
GROUP BY	month
)
```
- Then both cte and cte2 is inner join by the month and filter the results to get only the category with the best total sale. The result is then sorted by month
```
SELECT	cte.month,
	cte.year,
	cte.productcategory,
	cte.categorysale
FROM	cte
JOIN	cte2	ON	cte.month=cte2.month
WHERE	cte2.maxcategorysale=cte.categorysale
ORDER BY	cte.monthnumber
```
- Final SQL Queries:	
```
WITH cte	AS(
SELECT	EXTRACT(month FROM	ordereddate)	AS	monthnumber,
	TO_CHAR(ordereddate,'Month') AS month,
	EXTRACT(year FROM	ordereddate)	AS year,
	productcategory,
	SUM(productquantity)	categorysale
FROM	public.cleaned_data
WHERE	EXTRACT(year FROM	ordereddate) = (SELECT	MAX(EXTRACT(year FROM	ordereddate))	FROM	public.cleaned_data)
GROUP BY	EXTRACT(month FROM	ordereddate),
		TO_CHAR(ordereddate,'Month'),
		EXTRACT(year FROM	ordereddate),
		productcategory
),
cte2	AS(
SELECT	month,
	MAX(categorysale)	AS	maxcategorysale
FROM	cte
GROUP BY	month
)
SELECT	cte.month,
	cte.year,
	cte.productcategory,
	cte.categorysale
FROM	cte
JOIN	cte2	ON	cte.month=cte2.month
WHERE	cte2.maxcategorysale=cte.categorysale
ORDER BY	cte.monthnumber
```		

![Answer table](https://live.staticflickr.com/65535/53151062710_9bb28ab23b.jpg)



### Question 3: In what way that visitor have access to the website?
- Data is access from the allsessions table
- The total number of visit for each chanel is retrieved by calculated COUNT all the visit for GROUP BY the chanelgrouping collumn
```
COUNT(chanelgrouping)	AS	totalchanelgrouping
```
- The total number of visit is calculated
```
(SELECT	COUNT(*)	FROM	allsessions)::numeric(10,2)
```
- Then the ratio between the number of visit for each chanel and the total number of visit is then calculated by this querry
```
(COUNT(chanelgrouping)::numeric(10,2)/(SELECT	COUNT(*)	FROM	allsessions)::numeric(10,2) *100)::numeric(10,2)	  AS 		chanelgroupingratio
```
- The category is then sorted by the total number of visit in a decendng order
```
ORDER BY	COUNT(chanelgrouping)	DESC
```
- Final SQL Queries:
``` 
	SELECT 	chanelgrouping,
		COUNT(chanelgrouping)	AS	totalchanelgrouping,
		(COUNT(chanelgrouping)::numeric(10,2)/(SELECT	COUNT(*)	FROM	allsessions)::numeric(10,2) *100)::numeric(10,2)	  AS 		chanelgroupingratio
		FROM	allsessions
		GROUP BY	chanelgrouping
		ORDER BY	COUNT(chanelgrouping)	DESC
```
![Result Table](https://www.flickr.com/photos/199030492@N02/53151163693/in/dateposted-public/)

