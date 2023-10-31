## Ecommerce Project

### Project/Goals

To clean the dataset, access necessary data to answer the folling questions:
- Question 1: Which cities and countries have the highest level of transaction revenues on the site?
- Question 2: What is the average number of products ordered from visitors in each city and country?
- Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?
- Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?
- Question 5: Can we summarize the impact of revenue generated from each city/country?
- Question 6: How is the total revenue of the web site break down to month for the most recent year, and the ratio of the month revenue to the year revenue? 
- Question 7: Best selling category by month for most recent year?
- Question 8: In what way that visitor have access to the website?



### Process

- Explore the data and look for the necessary data to answer the question. Some of the finds are:
	- The data needed are the city, the country, ordered date, product id, product name, product category, product price, product ordered quantity, the chanel the customer get to the site

- Data accessing, cleaning and quality assurance
	- All the data needed will be collected, cleaned, and quality assured by the querry in cleaning_data.md file
	- There are many row in allsession table don't have data for the quantity ordered, so the querry attempts to fill posible quantity data by JOIN data from table analytics with the key vitsitor id, visit id, and unit price. For all the row that is NUll in productquantity, the quantity is got from unitprice collumn of puclic.analytics table.	In case both of the previous one is NULL, the quantity will be allsessions.totaltransactions/allsessions.	
	- All product price will be assumed as the productprice column in public.allsessions column
	- There is some row in the allsession table that have city in the wrong country, so country will be change for those row.
	- Product category column in allsession table is unclear so it will be formated to only contain the category name
	- Product price will be divided by 1,000,000
	- A new view with cleaned data will be created
	- If the quantity is still NULL or equal 0, don't select the row.
 	-  All row with unidentified city and country will not be included in the cleaned data
	- Ordered date have to be before or equal current date
 
- Answering question by writing queries to analyze cleaned data

### Results

- From answering all the question. We see that the US is accounted for near 89% of the total revenue, Irasel is about 5%, Autralia is just over 3% and the rest is less than 1%.The cites generated the most revenue are Mountain View, San Franisco and Sunnyvale which generates 14.9%, 14.7% and 13.2% respectively. Therefore, US is the major market for the company
-  From the data table, we can see that most US city ordered most product in Apparel category, when some of the US city also ordered more product from Nest category; by contrast, there is no significant pattern for city in other country. Moreove, over 36% of the transaction in the US is in Apparel category, 32% of the transaction in the US is in Nest category, and all other categories only share a small percentage of transaction. Besides, there's no significant pattern recognized from other countries.
-  Over 57% of the visit is from Organic Search, around 20% from Direct, around 17% from Referral. So the company can use some marketing project to boost the Direct and Referral search as well.
  
### Challenges 
- A lot of data in different table of the data is NULL and inconsistent data from different tables of the data set. 
- Meaning of some attributes are not clear
- Trouble finding links between tables

### Future Goals
- Clean data for the whole data set
- Finding other ways to fill up more NULL data
