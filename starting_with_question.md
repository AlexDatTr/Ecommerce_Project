  
### Question 1: Which cities and countries have the highest level of transaction revenues on the site?

#### Top 3 citis with higest revenue
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

!['Result table'](https://combo.staticflickr.com/pw/images/spaceout.gif)


#### top 3 countries with higest revenue
```
SELECT 	country	AS	country,
	SUM(productprice*productquantity) AS	totalcountry
FROM public.cleaned_data
GROUP BY	country
HAVING	SUM(productprice*productquantity) > 0
ORDER BY	SUM(productprice*productquantity)	DESC
LIMIT 3;
```
## Answer: The two queries calculated the higest revenue cities and countries by calculate the total revenue(productprice*productquantity) from all the transactions in each city and country
## Answer table:
- Top 3 citis with higest revenue:	https://drive.google.com/file/d/1nrMftZXLk5HFhYhVbZCBIe8WsZJ2bZxH/view?usp=sharing
- Top 3 countries with higest revenue:	https://drive.google.com/file/d/1vMrauD8dOD99c6-4YIDWLf3klL7gKQV0/view?usp=sharing
	   



# Question 2: What is the average number of products ordered from visitors in each city and country?


## SQL Queries:

	SELECT 	DISTINCT	city,
		SUM(productquantity)	OVER(PARTITION BY	city)/COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	city) AS	avgproductbycity,
		country,
		SUM(productquantity)	OVER(PARTITION BY	country)/COUNT(DISTINCT	visitorid)	OVER(PARTITION BY	country) AS	avgproductbycountry
	FROM	public.cleaned_data
	ORDER BY	city

## Answer: Calculated by geting the total quatity of product ordered in each city or country and then dividing by the number of visitor.
## Answer table: Average number of products ordered from visitors in each city and country https://drive.google.com/file/d/1osUZB5lRot-ASERmtSFaqrIuSFGc7W_8/view?usp=drive_link
	


# Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


## SQL Queries:

	--To count number of transaction in each category in each city
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

	--To count number of transaction in each category in each country			
	SELECT	country,
		productcategory,
		COUNT(productcategory)	AS	totaltransactionpercategory	
	FROM	public.cleaned_data
	GROUP BY	country,productcategory
	ORDER BY	country,
			totaltransactionpercategory DESC
	;

## Answer: The first query generates a table with all the city, different category ordered from each city and the total number of products ordered for each of those categories. The second query generate a table with all the country, different category ordered from each country and the total number of products ordered for each of those categories. From there, we can see that most US city ordered most product in Apparel category, when some of the US city also ordered more product from Nest category; by contrast, there is no significant pattern for city in other country. And when looking at the table from second query, it shows that over 36% of the transaction in the US is in Apparel category, 32% of the transaction in the US is in Nest category, and all other categories only share a small percentage of transaction. Besides, there's no significant pattern recognized from other countries.

## Answer table:
- Number of transaction in each category in each country:	https://drive.google.com/file/d/1YLL6tbsG-6RI1qnKkeBfpLsqdIa5Xyym/view?usp=drive_link
- Number of transaction in each category in each city:	https://drive.google.com/file/d/16EcikQ498HZxoQ7oInLwtzwR3gUVizdz/view?usp=sharing


# Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


## SQL Queries:

	-- city and best-selling product
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

	--	country and best-selling product
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



## Answer: These two queries calculated the total number of quantity ordered for each product, then filter out the product with highest sale for each city and country. There is no significant pattern spotted for the product sold
## Answer table:
- City and best-selling product:	https://drive.google.com/file/d/14fm9foWEOSihPfhU9weg2UCR04P2D2PN/view?usp=sharing
- Country and best-selling product:	https://drive.google.com/file/d/15-AiwcpODmhDjb78SrBf-EjGao8zq1Tk/view?usp=sharing
	



# Question 5: Can we summarize the impact of revenue generated from each city/country?**

## SQL Queries:

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


