### Question 1: How is the total revenue of the web site break down to month for the most recent year, and the ratio of the month revenue to the year revenue? 
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

![Result table](https://drive.google.com/file/d/1Q8bZwjYKGwWRtecJJYnxr_WwI2wXx4so/view?usp=drive_link)

# Question 2: Best selling category by month for most recent year?

## SQL Queries:	

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
		

## Answer:	Calculate the most recent year by finding the maximum year - 2017 , then calculate the best-selling category by finding the total product quantity for each category for 		each month of 2017. It might help decide which product category should be promoted at which time of the year.
## Answer table:	https://drive.google.com/file/d/1gYX43Zhjn3VfIRHFgufffGI9T-jpZXI_/view?usp=sharing



# Question 3: In what way that visitor have access to the website?

## SQL Queries:	
	SELECT 	chanelgrouping,
		COUNT(chanelgrouping)	AS	totalchanelgrouping,
		(COUNT(chanelgrouping)::numeric(10,2)/(SELECT	COUNT(*)	FROM	allsessions)::numeric(10,2) *100)::numeric(10,2)	  AS 		chanelgroupingratio
		FROM	allsessions
		GROUP BY	chanelgrouping
		ORDER BY	COUNT(chanelgrouping)	DESC

## Answer:	Visitor can access the website from Organic Search, Direct,Referral,Paid Search,Affiliates,Display, and (Other). The query will calculate the total number for each kind of 	accessing the website. Then it will divide that total number for the total number of sessions. From the data and the query, we can see that organic search take up over 57% of the 	way. Then Dirrect and Refferal is a bit smaller number with only 19.8 and 17,05 percent. And the rest of the chanel just take a small percentage of the total
## Answer table:	https://drive.google.com/file/d/1iI07Khm05jyfwMvRKP1sk-cUH1dolPGG/view?usp=drive_link


