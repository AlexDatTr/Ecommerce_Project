  
### Question 1: Which cities and countries have the highest level of transaction revenues on the site?

#### Top 3 citis with higest revenue
- Data is accessed from cleaned_data table
- The total revenue of each city will be calculated all the priduct price * product quantity for all the transaction made in that city, which is the following  in the SELECT clause
```
 SUM(productprice*productquantity) AS	totalcity
```
- The above SUM is then group by city and country to get the total revenue for each city
```
GROUP BY	city, country
```
- The result then get sorted by the total revenue in decending order. And only 3 biggest value are selected
``` 
ORDER BY	SUM(productprice*productquantity)	DESC
LIMIT 3;
```
- Final SQL querry:
```
SELECT 	city	AS	city,
	country	AS	country,
	SUM(productprice*productquantity) AS	totalcity
FROM public.cleaned_data 
GROUP BY	city, country
ORDER BY	SUM(productprice*productquantity)	DESC
LIMIT 3;
```

!['Result table'](https://live.staticflickr.com/65535/53151001069_18e4a63a68_w.jpg)


#### Top 3 countries with higest revenue
- Data is accessed from cleaned_data table
- The total revenue of each city will be calculated all the priduct price * product quantity for all the transaction made in that country, which is the following  in the SELECT clause
```
 SUM(productprice*productquantity) AS	totalcountry
```
- The above SUM is then group by city and country to get the total revenue for each country
```
GROUP BY	country
```
- The result then get sorted by the total revenue in decending order. And only 3 biggest value are selected
``` 
ORDER BY	SUM(productprice*productquantity)	DESC
LIMIT 3;
```
- Final SQL querry:
```
SELECT 	country	AS	country,
	SUM(productprice*productquantity) AS	totalcountry
FROM public.cleaned_data
GROUP BY	country
ORDER BY	SUM(productprice*productquantity)	DESC
LIMIT 3;
```
!['Result table'](https://live.staticflickr.com/65535/53150232717_a7a0310598_m.jpg)
	   



### Question 2: What is the average number of products ordered from visitors in each city and country?

- Data is accessed from cleaned_data table
- First, the total sale for each city is calculated by getting the total of product quantity for each city in the SELECT clause
```
SUM(productquantity)	OVER(PARTITION BY	city)
```
- Then the total number of visitor is calculated by getting the total count of distinct visitor
```
COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	city)
```
- Then the average number of products ordered from visitors for each city is calculated by dividing the total sale/total visitor
```
SUM(productquantity)	OVER(PARTITION BY	city)/COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	city) AS	avgproductbycity,
```
- The same logic apply to country, then we have this following query for the average number of products ordered from visitors in each city and country
```
SUM(productquantity)	OVER(PARTITION BY	country)/COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	country) AS	avgproductbycountry
```
- The DISTINCT is used in the SELECT clause to only pick the unique city and visitor id, and the order is softed by city
- Final SQL Queries:
```
SELECT 	DISTINCT	city,
	SUM(productquantity)	OVER(PARTITION BY	city)/COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	city) AS	avgproductbycity,
	country,
	SUM(productquantity)	OVER(PARTITION BY	country)/COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	country) AS	avgproductbycountry
FROM	public.cleaned_data
ORDER BY	city
```
![Result table](https://live.staticflickr.com/65535/53151287620_ed07c150ae_m.jpg)
	


### Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

#### To count number of transaction in each category in each city
- Data is accessd from public.cleaned_data table
- The number of sale per category for each city and country is calculated by a COUNT in the SELECT clause and GROUP BY city, country, and the product category, the COUNT collumn is then named totaltransactionpercategory
```
SELECT	country,
	city,
	productcategory,
	COUNT(productcategory)	AS	totaltransactionpercategory
FROM	public.cleaned_data
GROUP BY	city,country,productcategory
```
- Then the result is sorted by country, city, and the totaltransactionpercategory in decending order
```
ORDER BY	country,
		city,
		totaltransactionpercategory	DESC
```
- Final SQL Queries:
```
SELECT	country,
	city,
	productcategory,
	COUNT(productcategory)	AS	totaltransactionpercategory
FROM	public.cleaned_data
GROUP BY	city,country,productcategory
ORDER BY	country,
		city,
		totaltransactionpercategory	DESC
;
```
![Result table](https://live.staticflickr.com/65535/53150878962_7a26f1c518_m.jpg)

#### To count number of transaction in each category in each country
- Data is accessd from public.cleaned_data table
- The number of sale per category for each city and country is calculated by a COUNT in the SELECT clause and GROUP BY country, and the product category, the COUNT collumn is then named totaltransactionpercategory
```
SELECT	country,
	productcategory,
	COUNT(productcategory)	AS	totaltransactionpercategory
FROM	public.cleaned_data
GROUP BY	country,productcategory
```
- Then the result is sorted by country, and the totaltransactionpercategory in decending order
```
ORDER BY	country,
		city,
		totaltransactionpercategory	DESC
```
- Final SQL Queries:
```			
	SELECT	country,
		productcategory,
		COUNT(productcategory)	AS	totaltransactionpercategory	
	FROM	public.cleaned_data
	GROUP BY	country,productcategory
	ORDER BY	country,
			totaltransactionpercategory DESC
	;
```

![Result table](https://live.staticflickr.com/65535/53151957013_713c822177_m.jpg)

### Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

#### City and best-selling product
- Data is accessd from public.cleaned_data table
- The cte is create to calculated the total number of product sold for each  product product in each city and country, the total collumn is then named totalorderedquantity
```
WITH	cte AS(
SELECT 	country,
		city,
		productsku,
		productname,
		SUM(productquantity) AS	totalorderedquantity
FROM	public.cleaned_data
GROUP BY	country,
		city,
		productsku,
		productname
)
```
- Then cte2 is create to accessed data from the first cte. It main job is to get the max number of the totalorderedquantity in the first cte, GROUP  BY city
```
cte2 AS(
SELECT 	city	AS	city,
		MAX(cte.totalorderedquantity) AS	maxtotalorderedquantity
FROM	cte
GROUP BY	city
```
- Then the cte2 and cte is INNER JOIN using the city collumn as the key and only select the max number in totalorderedquantity (ie. maxtotalorderedquantity)
```
SELECT		cte.country,
		cte.city,
		cte.productsku,
		cte.productname,
		cte.totalorderedquantity
FROM	cte
JOIN	cte2 ON	cte.city=cte2.city
WHERE	cte.totalorderedquantity=cte2.maxtotalorderedquantity
```
- Final SQL Queries:
```
WITH	cte AS(
SELECT 	country,
		city,
		productsku,
		productname,
		SUM(productquantity) AS	totalorderedquantity
FROM	public.cleaned_data
GROUP BY	country,
		city,
		productsku,
		productname
),
cte2 AS(
SELECT 	city	AS	city,
		MAX(cte.totalorderedquantity) AS	maxtotalorderedquantity
FROM	cte
GROUP BY	city
)
SELECT		cte.country,
		cte.city,
		cte.productsku,
		cte.productname,
		cte.totalorderedquantity
FROM	cte
JOIN	cte2 ON	cte.city=cte2.city
WHERE	cte.totalorderedquantity=cte2.maxtotalorderedquantity
```
![Result table](https://live.staticflickr.com/65535/53150920307_bab6d2d715_m.jpg)

#### Country and best-selling product
- Data is accessd from public.cleaned_data table
- The cte is create to calculated the total number of product sold for each  product product in each city and country, the total collumn is then named totalorderedquantity
```
WITH	cte AS(
	SELECT 	country,
		productsku,
		productname,
		SUM(productquantity) AS	totalorderedquantity
	FROM	public.cleaned_data
	GROUP BY	country,
		productsku,
		productname
),
```
- Then cte2 is create to accessed data from the first cte. It main job is to get the max number of the totalorderedquantity in the first cte, GROUP  BY country
```
cte2 AS(
	SELECT 	country	AS	country,
			MAX(cte.totalorderedquantity) AS	maxtotalorderedquantity
	FROM	cte
	GROUP BY	country
)
```
- Then the cte2 and cte is INNER JOIN using the city collumn as the key and only select the max number in totalorderedquantity (ie. maxtotalorderedquantity)
```
SELECT		cte.country,
			cte.productsku,
			cte.productname,
			cte.totalorderedquantity
	FROM	cte
	JOIN	cte2 ON	cte.country=cte2.country
	WHERE	cte.totalorderedquantity=cte2.maxtotalorderedquantity
```
- Final SQL Queries:
```
	
	WITH	cte AS(
	SELECT 	country,
			productsku,
			productname,
			SUM(productquantity) AS	totalorderedquantity
	FROM	public.cleaned_data
	GROUP BY	country,
				productsku,
				productname
	),
	cte2 AS(
	SELECT 	country	AS	country,
			MAX(cte.totalorderedquantity) AS	maxtotalorderedquantity
	FROM	cte
	GROUP BY	country
	)
	SELECT		cte.country,
			cte.productsku,
			cte.productname,
			cte.totalorderedquantity
	FROM	cte
	JOIN	cte2 ON	cte.country=cte2.country
	WHERE	cte.totalorderedquantity=cte2.maxtotalorderedquantity
```
![Result table](https://live.staticflickr.com/65535/53151937465_aeb0d68b34_m.jpg)


### **Question 5: Can we summarize the impact of revenue generated from each city/country?**


- Final SQL Queries:

	-- city and revenue and ratio of city revenue over total revenue
	SELECT	DISTINCT	city, country,
		CAST(SUM(productquantity*productprice)	AS	float(2)) AS	cityrevenue,
		CAST(SUM(productquantity*productprice)/(SELECT SUM(productquantity*productprice)AS	totalrevenue	FROM	public.cleaned_data)*100 AS 	float(2)) AS	revenueratio
	FROM	public.cleaned_data	
	GROUP BY	city,country
	ORDER BY	revenueratio DESC
	-- country and revenue and ratio of ccountry revenue over total revenue
	SELECT	DISTINCT	country,
		CAST(SUM(productquantity*productprice)	AS	float(2)) AS	countryrevenue,
		CAST(SUM(productquantity*productprice)/(SELECT SUM(productquantity*productprice)AS	totalrevenue	FROM	public.cleaned_data)*100 AS float(2)) AS	revenueratio
	FROM	public.cleaned_data	
	GROUP BY	country
	ORDER BY	revenueratio DESC



## Answer: The querry calculated the total revenue of each city and country, and the total revenue for the dataset, then calculated the ratio of revenue of each city over total revenue for the data set. From the result, we see that the US is accounted for near 89% of the total revenue, Irasel is about 5%, Autralia is just over 3% and the rest is less than 1%.The cites generated the most revenue are Mountain View, San Franisco and Sunnyvale which generates 14.9%, 14.7% and 13.2% respectively
## Answer table:
- City and revenue and ratio of city revenue over total revenue:	https://drive.google.com/file/d/1uH7C2icxt7d8Kom-zdyjshPOo2Jnod9M/view?usp=drive_link
- Country and revenue and ratio of ccountry revenue over total revenue:	https://drive.google.com/file/d/1MnCG-ZLqmIDYv5XwWQqXqc0Q-slsSTnB/view?usp=sharing


