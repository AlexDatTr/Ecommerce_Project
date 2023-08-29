### What issues will you address by cleaning the data?

 #### There are many row in allsession table don't have data for the quantity ordered, so the querry attempts to fill posible quantity data by JOIN data from table analytics with the key vitsitor id, visit id, and unit price. For all the row that is NUll in productquantity, the quantity is got from unitprice collumn of puclic.analytics table.	In case both of the previous one is NULL, the quantity will be allsessions.totaltransactions/allsessions.	
 #### All product price will be assumed as the productprice column in public.allsessions column
 #### There is some row in the allsession table that have city in the wrong country, so country will be change for those row.
 #### Product category column in allsession table is unclear so it will be formated to only contain the category name
 #### Product price will be divided by 1,000,000
 #### A new view with cleaned data will be created
 #### Component of cleaned data in cleaned_data view
	- visitid: the visit id
	- visitorid: website visitor's id
	- ordereddate: ordered date 
	- productsku: id number of product
	- productname: name of product
	- productcategory: category of product
	- productprice:	unit price of product
	- productquantity: ordered quantity of product

### Quality Assurance
#### If the quantity is still NULL or equal 0, don't select the row. A W

#### All row with unidentified city and country will not be included in the cleaned data
#### Ordered date have to be before or equal current date
#### This  WHERE clause will be added to do the above tasks
```
WHERE	COALESCE(allsessions.productquantity,analytics.unitsold,CAST(allsessions.totaltransactionrevenue/allsessions.productprice	AS	int)) IS NOT NULL
AND	COALESCE(allsessions.productquantity,analytics.unitsold,CAST(allsessions.totaltransactionrevenue/allsessions.productprice	AS	int)) !=0
AND	allsessions.city != '(not set)'
AND	allsessions.city !=	'not available in demo dataset'
AND	allsessions.date <= current_date
```

#### Final Data Cleaning and QA Queries
CREATE	VIEW	cleaned_data	AS
SELECT 	DISTINCT	allsessions.visitorid	AS	visitorid,
			allsessions.visitid	AS	visitid,
			allsessions.city	AS	city,
			CASE
			WHEN	city='New York'	THEN	CAST('United States' AS varchar(100))
			ELSE	allsessions.country	
			END	AS	country,
			allsessions.date	AS	ordereddate,
			allsessions.productsku AS	productsku,
			allsessions.productname AS	productname,
			CASE	
			WHEN	productcategory	LIKE	'%Nest%'	OR	productname	LIKE	'%Nest%'	THEN	'Nest'
			WHEN	productcategory	LIKE	'%Apparel%'	OR	productname	LIKE	'%Men%'	
									OR	productname	LIKE	'%Women%'
									OR	productname	LIKE	'%Men%'		THEN	'Apparel'
			WHEN	productcategory	LIKE	'%Accessories%'	THEN	'Accesories'
			WHEN	productname	LIKE	'%Android Lunch Kit%'	THEN	'Houseware'
			WHEN	productname	LIKE	'%Android 17oz Stainless Steel Sport Bottle%'	THEN	'Drinkware'
			WHEN	productcategory	LIKE	'%Drinkware%'	THEN	'Drinkware'
			WHEN	productcategory	LIKE	'%Office%'	THEN	'Office'
			WHEN	productcategory	LIKE	'%Housewares%'	THEN	'Houseware'
			WHEN	productcategory	LIKE	'%Bags%'	THEN	'Bags'	
			WHEN	productcategory	LIKE	'%Lifestyle%'	THEN	'Lifestyle'	
			WHEN	productname	LIKE	'%Notebook%'	THEN	'Office'
			WHEN	productname	LIKE	'%Waze%'	THEN	'Waze'
			WHEN	productname	LIKE	'%Sticker%'	THEN	'Accesories'
			WHEN	productname	LIKE	'%Sunglasses'	THEN	'Apparel'
			END	AS	productcategory,
			CAST(allsessions.productprice AS	float(2))/1000000 AS	productprice,								COALESCE(allsessions.productquantity,analytics.unitsold,CAST(allsessions.totaltransactionrevenue/allsessions.productprice	AS	int))			AS	productquantity
			
FROM	public.allsessions
LEFT	JOIN	public.analytics	
	ON	allsessions.visitorid=analytics.visitorid
	AND	allsessions.visitid=analytics.visitid
	AND	allsessions.productprice=analytics.unitprice
		
WHERE	COALESCE(allsessions.productquantity,analytics.unitsold,CAST(allsessions.totaltransactionrevenue/allsessions.productprice	AS				int)) IS NOT NULL
	AND	COALESCE(allsessions.productquantity,analytics.unitsold,CAST(allsessions.totaltransactionrevenue/allsessions.productprice				AS	int)) !=0
	AND	allsessions.city != '(not set)'
	AND	allsessions.city !=	'not available in demo dataset'
	AND	allsessions.date <= current_date
ORDER BY	visitorid,
		visitid,
		city,
		country,
		ordereddate,
		productsku,
		productcategory,
		productprice,
		productquantity
;
```
