# Data Cleaning Project Melbourne House Sales
**Data Cleaning using SQL**!

![Image](https://user-images.githubusercontent.com/129844542/235351094-9f97e963-d961-42bd-8013-2c9212e3dc41.jpeg)

In this Project I will be cleaning the raw data, using Substring, Parsename, Update, Upper, Lower, etc. to make the data cleaner and easier to analyze and visualize.

## Full Query for Data Cleaning Project
**Check the Full Query for Data Cleaning Project [here](https://github.com/DenidyaFadiya/MelbourneHouseSales/blob/main/Full%20Query%20-%20Data%20Cleaning%20Melbourne%20House%20Sales).**

In this Data Cleaning project I will be cleaning data that I took from [Kaggle Data Source](https://www.kaggle.com/datasets/dansbecker/melbourne-housing-snapshot).

## Data Cleaning Project

Prior to the project, I changed few columns to make it messier for this Data Cleaning Project.

**Quick Overview**
**Raw Data (Before)**

 ![image](https://user-images.githubusercontent.com/129844542/235073933-61c4e0ea-8c16-4944-9841-efcf32c58f9a.png)


**Cleaned Data (After)**

 ![image](https://user-images.githubusercontent.com/129844542/235073945-cd3a9a77-c7a2-4c55-8ecd-8634bea0ecb2.png)





## Steps for Data Cleaning

### Change the Date Format
First thing First, change the format for the Date column. Going from Date and Time to Date only.
```
select SalesDate, convert(Date,SalesDate) as Date
from dbo.salesdata

update dbo.salesdata
set SalesDate = convert(Date,SalesDate)
```

 ![image](https://user-images.githubusercontent.com/129844542/235073971-f8b951a5-1ebe-4655-b5b8-54051fea4d4a.png)

If the table is still not updating, try using Alter Table to insert the update
 ```
 alter table dbo.salesdata
Add SaleDateUpdated Date;

update dbo.salesdata
set SaleDateUpdated = convert(Date,SalesDate)
```
![image](https://user-images.githubusercontent.com/129844542/235074075-fe3e94d3-04fb-4151-b72f-3e4d9dc97a7f.png)

![image](https://user-images.githubusercontent.com/129844542/235074093-739483ad-dd3c-4c03-9572-59b87fae50e8.png)

### Divide the Suburb, Address, and the PostCode From the Property Address Column
I divide the Suburb, Address and the Post Code, so that could help me to easier identifies the data later on the Exploratory Project.
 ```
 select
	PropertyAddress,
	parsename(replace(PropertyAddress, ',', '.') , 3) as Suburb,
	parsename(replace(PropertyAddress, ',', '.') , 2) as Address,
	parsename(replace(PropertyAddress, ',', '.') , 1) as PostCode
from dbo.SalesData
order by house_id
```
![image](https://user-images.githubusercontent.com/129844542/235074118-bba67108-f067-4a84-95f5-cbf20459885c.png)

 ![image](https://user-images.githubusercontent.com/129844542/235074140-8b6b8ed4-4b82-4f4a-a5b1-10d3b570ddcc.png)

Query to Update the Suburb, Address and PostCode
```
alter table dbo.SalesData
add Suburb nvarchar(255);

update dbo.SalesData
set Suburb = parsename(replace(PropertyAddress, ',', '.') , 3)


alter table dbo.SalesData
add Address nvarchar(255);

update dbo.SalesData
set Address = parsename(replace(PropertyAddress, ',', '.') , 2)


alter table dbo.SalesData
add PostCode nvarchar(255);

update dbo.SalesData
set PostCode = parsename(replace(PropertyAddress, ',', '.') , 1)
```
 ![image](https://user-images.githubusercontent.com/129844542/235074174-e22f061a-3e24-4dc7-be58-fd528d1364c7.png)


### Trim amd update any unecesarry Spaces
 ```
select suburb,
trim(suburb) as Suburb_Fixed
from dbo.SalesData
order by House_id

update dbo.SalesData
set Suburb = trim(Suburb)
```
![image](https://user-images.githubusercontent.com/129844542/235074199-85393c04-82c2-47fc-a025-f51d9788ac35.png)

![image](https://user-images.githubusercontent.com/129844542/235074241-d83c302b-a08b-40ca-920f-3ea787ea146b.png)

### Dividing the Suburb
In the Suburb column there are suburbs that contained more than one word for the Suburb name. We are going to capitalized the first letter of each Suburb, but we cannot do that to 2 word Suburb, hence why I am going to divide the 2 word Suburb to each column. 
 ```
 select 
	suburb,
	substring(Suburb, 1, charindex(' ', Suburb)) as FirstSuburb,
	substring(Suburb, charindex(' ', Suburb) +1 , len(Suburb)) as LastSuburb
from dbo.SalesData
order by house_id

alter table dbo.SalesData
Add FirstSuburb Nvarchar(255);

update dbo.SalesData
set FirstSuburb = substring(Suburb, 1, charindex(' ', Suburb))


alter table dbo.SalesData
Add LastSuburb Nvarchar(255);

update dbo.SalesData
set LastSuburb = substring(Suburb, charindex(' ', Suburb) +1 , len(Suburb))
```
![image](https://user-images.githubusercontent.com/129844542/235074293-4859a6ae-d740-4320-bd32-c401aa43ceda.png)

Result on two word Suburb:
 
![image](https://user-images.githubusercontent.com/129844542/235074309-668d2dce-8e68-40dd-9ce1-84556b1dedad.png)


### Capitalized First Character on Each Suburb Column
I have separated two column now I will capitalize each suburb name
```
select suburb, 
		upper(left(FirstSuburb,1))+lower(substring(FirstSuburb,2,len(FirstSuburb))) as FirstSuburb,
		upper(left(LastSuburb,1))+lower(substring(LastSuburb,2,len(LastSuburb)))  as LastSuburb
from dbo.SalesData

update dbo.SalesData
set FirstSuburb = upper(left(FirstSuburb,1))+lower(substring(FirstSuburb,2,len(FirstSuburb)))


update dbo.SalesData
set LastSuburb = upper(left(LastSuburb,1))+lower(substring(LastSuburb,2,len(LastSuburb)))
```
 
![image](https://user-images.githubusercontent.com/129844542/235074339-c98df71c-6c2b-4ddf-b18f-c01e9f4854ae.png)

 ![image](https://user-images.githubusercontent.com/129844542/235074378-f8036d49-f923-4393-97e2-bfce8166ea9a.png)


### Concat the FirstSuburb and the LastSuburb as FixedSuburb
I have already updated the First and Last Suburb, now they are now on their column. I will concat those two column as we have already done cleaning them. 
 ```
 select
	FirstSuburb,
	LastSuburb,
	concat(FirstSuburb, ' ', LastSuburb) as FixedSuburb
from dbo.SalesData

alter table dbo.SalesData
Add FixedSuburb Nvarchar(255);

update dbo.SalesData
set FixedSuburb = concat(FirstSuburb, ' ', LastSuburb)
```
 ![image](https://user-images.githubusercontent.com/129844542/235074391-fde7e6ad-9a87-4d1c-8369-bef5f320dcba.png)

![image](https://user-images.githubusercontent.com/129844542/235074399-4ac0a635-8142-4664-b22d-1bfc2a87048d.png)

### Trim the Concat Fixed Suburb
After the concat, I found some Suburb still have unecesarry spaces at the front. Trim function will be needed to be used. 
 ```
 select FixedSuburb,
trim(FixedSuburb) 
from dbo.SalesData
order by House_id

update dbo.SalesData
set FixedSuburb = trim(FixedSuburb)
```
 ![image](https://user-images.githubusercontent.com/129844542/235074420-4dd72c66-b468-414f-ab14-8bc8d72f42af.png)

![image](https://user-images.githubusercontent.com/129844542/235074517-e5cf586d-453f-4a23-9b4f-bf6405018600.png)

### Dividing the Council Area and Region Name
I will be dividing the Council Area and the Region Name from Region column, I will do it like how I divide Suburb, Address and Post Code above.
 ```
 select 
	Region,
	substring(Region, 1, charindex(',', Region)-1) as CouncilArea,
	substring(Region, charindex(',', Region) +1 , len(Region)) as RegionName
from dbo.SalesData
order by House_id


alter table dbo.SalesData
Add CouncilArea Nvarchar(255);

update dbo.SalesData
set CouncilArea = substring(Region, 1, charindex(',', Region)-1)


alter table dbo.SalesData
Add RegionName Nvarchar(255);

update dbo.SalesData
set RegionName = substring(Region, charindex(',', Region) +1 , len(Region))
```
Result :
![image](https://user-images.githubusercontent.com/129844542/235078498-5d441a6d-7161-464d-8a0b-d86f6a026f8a.png)


### Drop Unecesarry Column
I am going to drop some of the column because we are done cleaning it. 
 ```
 alter table dbo.SalesData
drop column PropertyAddress, 
			SalesDate, 
			Region, 
			FirstSuburb, 
			LastSuburb, 
			FixedSuburb,
			SaleDateUpdated
```
![image](https://user-images.githubusercontent.com/129844542/235074680-1f8da977-f89b-4885-b83f-7af6dc7f2413.png)


### Change the FixedSuburb to Suburb
I can change the FixedSuburb to Suburb once now the Suburb column has gone
![image](https://user-images.githubusercontent.com/129844542/235074699-abc7c6fb-6007-4afe-a758-30a80858f9d5.png)
```
alter table dbo.SalesData
Add Suburb Nvarchar(255);

update dbo.SalesData
set Suburb = fixedsuburb
```

### Change the name of SaleDataUpdated to Date
![image](https://user-images.githubusercontent.com/129844542/235074731-d1af6b08-7af8-4f7a-b2e8-320d87d9d1af.png)
```
alter table dbo.SalesData
add Date nvarchar(255)

update dbo.SalesData
set Date = SaleDateUpdated
```

### Final Result
![image](https://user-images.githubusercontent.com/129844542/235074759-fadced25-e14b-45d7-8f83-bee7293fb3ad.png)


