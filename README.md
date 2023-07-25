# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals

To clean the dataset, access necessary data to answer the folling questions.
### Question 1: Which cities and countries have the highest level of transaction revenues on the site?
### Question 2: What is the average number of products ordered from visitors in each city and country?
### Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?
### Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?
### Question 5: Can we summarize the impact of revenue generated from each city/country?
### Question 6: How is the total revenue of the web site break down to month for the most recent year, and the ratio of the month revenue to the year revenue? 
### Question 7: Best selling category by month for most recent year?
### Question 8: In what way that visitor have access to the website?



## Process
### Explore the data and look for the necessary data to answer the question. Some of the finds are:
	-	The data needed are the city, the country, ordered date, product id, product name, product category, product price, product ordered quantity, the chanel the customer get to 		the site
### Data accessing, cleaning and quality assurance
	-	All the data needed will be collected, cleaned, and quality assured by the querry in cleaning_data.md file
	-	There are many row in allsession table don't have data for the quantity ordered, so the querry attempts to fill posible quantity data by JOIN data from table analytics with 		the key vitsitor id, visit id, and unit price. For all the row that is NUll in productquantity, the quantity is got from unitprice collumn of puclic.analytics table.	In 		case both of the previous one is NULL, the quantity will be allsessions.totaltransactions/allsessions.	
	-	All product price will be assumed as the productprice column in public.allsessions column
	-	There is some row in the allsession table that have city in the wrong country, so country will be change for those row.
	-	Product category column in allsession table is unclear so it will be formated to only contain the category name
	-	Product price will be divided by 1,000,000
	-	A new view with cleaned data will be created
	-	If the quantity is still NULL or equal 0, don't select the row.
	-	All row with unidentified city and country will not be included in the cleaned data
	-	Ordered date have to be before or equal current date
### Answering question by writing queries to analyze cleaned data
	

## Results

### Question 1: Which cities and countries have the highest level of transaction revenues on the site?
		Result from calculating the total revenue(productprice*productquantity) from all the transactions in each city and country
		Top 3 citis with higest revenue:	https://drive.google.com/file/d/1nrMftZXLk5HFhYhVbZCBIe8WsZJ2bZxH/view?usp=sharing
		Top 3 countries with higest revenue:	https://drive.google.com/file/d/1vMrauD8dOD99c6-4YIDWLf3klL7gKQV0/view?usp=sharing\

### Question 2: What is the average number of products ordered from visitors in each city and country?
		Average number of products ordered from visitors in each city and country:	https://drive.google.com/file/d/1osUZB5lRot-ASERmtSFaqrIuSFGc7W_8/view?usp=drive_link
		Calculated by geting the total quatity of product ordered in each city or country and then dividing by the number of visitor
	
### Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?
		The first query generates a table with all the city, different category ordered from each city and the total number of products ordered for each of those categories. The 		second query generate a table with all the country, different category ordered from each country and the total number of products ordered for each of those categories. From 		there, we can see that most US city ordered most product in Apparel category, when some of the US city also ordered more product from Nest category; by contrast, there is 		no significant pattern for city in other country. And when looking at the table from second query, it shows that over 36% of the transaction in the US is in Apparel 			category, 32% of the transaction in the US is in Nest category, and all other categories only share a small percentage of transaction. Besides, there's no significant 			pattern recognized from other countries.
		Number of transaction in each category in each country:	https://drive.google.com/file/d/1YLL6tbsG-6RI1qnKkeBfpLsqdIa5Xyym/view?usp=drive_link
		Number of transaction in each category in each city:	https://drive.google.com/file/d/16EcikQ498HZxoQ7oInLwtzwR3gUVizdz/view?usp=sharing

### Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?
		City and best-selling product:	https://drive.google.com/file/d/14fm9foWEOSihPfhU9weg2UCR04P2D2PN/view?usp=sharing
		Country and best-selling product:	https://drive.google.com/file/d/15-AiwcpODmhDjb78SrBf-EjGao8zq1Tk/view?usp=sharing
		Answered by calculating the total number of quantity ordered for each product, then filter out the product with highest sale for each city and country. There is no 			significant pattern spotted for the product sold

### Question 5: Can we summarize the impact of revenue generated from each city/country?
		Answered by calculating the total revenue of each city and country, and the total revenue for the dataset, then calculated the ratio of revenue of each city over total 		revenue for the data set. From the result, we see that the US is accounted for near 89% of the total revenue, Irasel is about 5%, Autralia is just over 3% and the rest is 		less than 1%.The cites generated the most revenue are Mountain View, San Franisco and Sunnyvale which generates 14.9%, 14.7% and 13.2% respectively

		City and revenue and ratio of city revenue over total revenue:	https://drive.google.com/file/d/1uH7C2icxt7d8Kom-zdyjshPOo2Jnod9M/view?usp=drive_link
		Country and revenue and ratio of ccountry revenue over total revenue:	https://drive.google.com/file/d/1MnCG-ZLqmIDYv5XwWQqXqc0Q-slsSTnB/view?usp=sharing

### Question 6: How is the total revenue of the web site break down to month for the most recent year, and the ratio of the month revenue to the year revenue? 
		Calculate the most recent year by finding the maximum year - 2017 , then calculate the total of productprice * productquantity group by all the months of the year 			2017
		Answer table:	https://drive.google.com/file/d/1jg8ati5okACq9cD4DuMD9kTJgwL-5P3O/view?usp=sharing

### Question 7: Best selling category by month for most recent year?
		Calculate the most recent year by finding the maximum year - 2017 , then calculate the best-selling category by finding the total product quantity for each category for 		each month of 2017. It might help decide which product category should be promoted at which time of the year.
		Answer table:	https://drive.google.com/file/d/1gYX43Zhjn3VfIRHFgufffGI9T-jpZXI_/view?usp=sharing

### Question 8: In what way that visitor have access to the website?
		Visitor can access the website from Organic Search, Direct,Referral,Paid Search,Affiliates,Display, and (Other). The query will calculate the total number for each kind of 		accessing the website. Then it will divide that total number for the total number of sessions. From the data and the query, we can see that organic search take up over 57% 		of the 	way. Then Dirrect and Refferal is a bit smaller number with only 19.8 and 17,05 percent. And the rest of the chanel just take a small percentage of the total
		Answer table:	https://drive.google.com/file/d/1iI07Khm05jyfwMvRKP1sk-cUH1dolPGG/view?usp=drive_link


## Challenges 
### A lot of data in different table of the data is NULL and inconsistent data from different tables of the data set. 
### Meaning of some attributes are not clear
### Trouble finding links between tables

## Future Goals
### Clean data for the whole data set
### Finding other ways to fill up more NULL data
