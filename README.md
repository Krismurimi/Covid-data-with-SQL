# Covid-data-with-SQL
Analysing Covid-19 data using SQL


## Data preview
For this analysis, two tables have been used to make up our database. There is a 'covid deaths' table and a 'covid vaccinations' table.
To get a look at the data we open the tables using the select all query.

```{sql}
SELECT *
FROM `premium-summit-342213.covid_data.covid_deaths`;
```
The above qives us the covid deaths table.

```{sql}
SELECT *
FROM `premium-summit-342213.covid_data.covid_vaccinations`;
```
The above gives us the vaccinations table.

## Using the data to gain insights
--Likelihood of dying if you contract covid in Africa
```{sql}
SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS death_percentage
FROM `premium-summit-342213.covid_data.covid_deaths`
WHERE location = 'Africa'
ORDER BY 1,2;
```

---Looking at total cases vs population
```
SELECT location, date, population, total_cases, (total_cases/population)*100 AS case_percentage
FROM `premium-summit-342213.covid_data.covid_deaths`
WHERE location = 'Africa'
ORDER BY 1,2;
```
-case_percentage shows the percentage of the population that got covid

---Looking at countries with the highest infection rate
```
SELECT location, population, MAX(total_cases) AS highest_infection_count,
    MAX((total_cases/population))*100 AS percent_of_pop_infected
FROM `premium-summit-342213.covid_data.covid_deaths`
GROUP BY location, population
ORDER BY percent_of_pop_infected DESC;
```

---Countries with highest death count per population
```
SELECT location, MAX(cast(total_deaths as int)) AS total_death_count 
FROM `premium-summit-342213.covid_data.covid_deaths`
WHERE continent is not NULL 
GROUP BY location
ORDER BY total_death_count DESC;
```

---Break down by continent
```
SELECT continent,MAX(cast(total_deaths as int)) AS total_death_count
FROM `premium-summit-342213.covid_data.covid_deaths`
WHERE continent is not NULL 
GROUP BY continent
ORDER BY total_death_count DESC;
```


---Global data
```
SELECT SUM(new_cases) AS global_cases, SUM(cast(new_deaths AS int)) AS global_deaths,
 SUM(cast(new_deaths as int))/SUM(new_cases)*100 AS percentage_death
FROM `premium-summit-342213.covid_data.covid_deaths`
WHERE continent is not NULL 
ORDER BY 1,2;
```

---combining the tables
```
SELECT *
FROM `premium-summit-342213.covid_data.covid_deaths` dea
JOIN `premium-summit-342213.covid_data.covid_vaccinations` vac
    ON dea.location = vac.location
    AND dea.date = vac.date;
```

---Looking at total population vs vaccinations
```
SELECT dea.location, dea.population, MAX(vac.people_fully_vaccinated) AS total_vaccinated,
    (MAX(vac.people_fully_vaccinated)/dea.population)*100 AS percentage_pop_fully_vaccinated
FROM `premium-summit-342213.covid_data.covid_deaths` dea
JOIN `premium-summit-342213.covid_data.covid_vaccinations` vac
    ON dea.location = vac.location
    AND dea.date = vac.date
WHERE dea.continent is not NULL
GROUP BY dea.location, dea.population
ORDER BY dea.location;
```



