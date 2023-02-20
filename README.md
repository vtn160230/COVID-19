# COVID-19
Analyzing a data set of COVID-19 from the years of 2020-2021! Mainly analyzing the countries of the United States and Vietnam because those are the two that pertain to me the most as a Vietnamese American.

The dataset is from January 21, 2020 - April 30th, 2021

Using SSMS on this project because I found it difficult to import the excel sheet over in pgAdmin due to the null values and column data types. I'll come back to it and figure out how to do that in pgAdmin eventually though!

## Q1. What is the highest percentage of people in Vietnam and the United States that got vaccinated up until April 30th, 2021? 

````sql
with t1 as (
SELECT d.continent, d.location, d.date, d.population, v.new_vaccinations, SUM(CONVERT(int, v.new_vaccinations)) OVER (PARTITION BY d.location ORDER BY d.location, d.date) AS RollingPeopleVaccinated
FROM [Portfolio Project]..CovidDeaths d
JOIN [Portfolio Project]..CovidVaccinations v
ON d.location = v.location 
AND d.date = v.date
WHERE d.location = 'Vietnam' OR d.location = 'United States')

SELECT d.continent, d.location, MAX(RollingPeopleVaccinated/d.population)*100 AS highest_percent_vaccinated FROM PercentPopulationVaccinated p 
JOIN [Portfolio Project]..CovidDeaths d
ON d.location = p.location
WHERE d.location = 'Vietnam' OR d.location = 'United States'
GROUP BY d.continent, d.location
````

- Here I am looking to create a rolling number of the vaccinations using a temp table, and from there use that same temp table to grab the max total of each countries vaccination and dividing it by the population to give the highest vaccination percentage up until the data shows.
- Create temp table and request for continent, location, date, location, new vaccination along with a windows function that partitions by the location and order by the location and date
- After that you should have a rolling number of vaccinations for both countries
- Create another select statement but with MAX of the rolling number and divide it by the population and multiply by 100 to get a percentage
<br>
Answer: United States had 68.7% and Vietnam had 0.52% of the population administered the vaccine by April 30th, 2021
<br>

![oU76ilT](https://user-images.githubusercontent.com/122754787/219989004-963478f6-51ab-4961-8c72-4f6b2afdb608.png)

***

## Q2. After answering the last question, I was curious as to why vaccination percentage in Vietnam was incredily low compared to the United States. United States had  68.7% and Vietnam had as little as 0.52% of the population administered the vaccine.

I began this answering this question by first analyzing how many total cases both country had compared to their population.
<br>

````sql
with t1 as (
SELECT location, population, MAX(total_cases) AS highest_amt_cases from CovidDeaths
WHERE location = 'Vietnam' OR location = 'United States'
GROUP BY location, population)

SELECT location, (highest_amt_cases/population)*100 AS infection_percentage from t1
````
<br>

![infectionperc](https://user-images.githubusercontent.com/122754787/219993273-7ee27bda-a4f3-4846-a5d9-a4763e5b1eb9.png)
