---
layout: post
title: Data Exploration of Covid Dataset
image: "Covid-19.jpg"
tags: [SQL, Covid]
---
I did this project when I had just started learning SQL and COVID was at its zenith, curiosity took the best out of me and while sipping some coffee alone at home NOT AT MY LOCAL FAVORITE COFFEE SHOP, I started exploring this COVID data set that I had found. 

Firstly I wanted to find out all the COVID related deaths by Continent.

SELECT * 
FROM PortfolioProject..CovidDeaths$
WHERE continent is not NULL
ORDER BY 3,4

Then I wanted to find out what's the vaccination count was at that point everywhere. 

SELECT * 
FROM PortfolioProject..CovidVaccinations$
ORDER BY 3,4

Then I was curious to find out Total Cases, Total Deaths, by Location 

SELECT Location, date, total_cases, new_cases, total_deaths, population
FROM PortfolioProject..CovidDeaths$
ORDER BY 1,2

Looking at total cases vs total deaths
Shows the likelihood of dying from Covid in your country;

SELECT Location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
FROM PortfolioProject..CovidDeaths$
WHERE location like '%Pakistan%'
ORDER BY 1,2

I wanted to look at total cases vs population
Finding what percentage of people from population contracted Covid-19. No guesses that despite being in USA at that time I was still curious how things are going in Pakistan

SELECT Location, date,population,total_cases, (total_cases/population)*100 as PercentofPopulationInfected
FROM PortfolioProject..CovidDeaths$
WHERE location like '%Pakistan%'
ORDER BY 1,2

I wanted to look at countries with highest infection rate to population:

SELECT Location, population, MAX(total_cases) AS HighestInfectionCount, MAX((total_cases/population))*100 as PercentofPopulationInfected
FROM PortfolioProject..CovidDeaths$
GROUP BY Location, Population
ORDER BY PercentofPopulationInfected Desc

I wanted to find out countries with highest death count per population:

SELECT Location, MAX(cast(total_deaths as int)) AS TotalDeathCount 
FROM PortfolioProject..CovidDeaths$
WHERE continent is not NULL
GROUP BY Location
ORDER BY TotalDeathCount Desc

I wanted to break things down by CONTINENT again, focusing on total deaths in each Continent:

SELECT continent, MAX(cast(total_deaths as int)) AS TotalDeathCount 
FROM PortfolioProject..CovidDeaths$
WHERE continent is not NULL
GROUP BY continent
ORDER BY TotalDeathCount Desc


Then I took a glance at the Global Stats:

SELECT date, SUM(new_cases) AS total_cases, SUM(cast(new_deaths as int)) AS total_Deaths,SUM(cast(new_deaths as int))/SUM(new_cases)*100 as DeathPercentage
FROM PortfolioProject..CovidDeaths$
WHERE continent is not NULL
GROUP BY date
ORDER BY 1,2

SELECT SUM(new_cases) AS total_cases, SUM(cast(new_deaths as int)) AS total_Deaths,SUM(cast(new_deaths as int))/SUM(new_cases)*100 as DeathPercentage
FROM PortfolioProject..CovidDeaths$
WHERE continent is not NULL
--GROUP BY date
ORDER BY 1,2

I wanted to look at Total Population vs Vaccination:

SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(Convert(int,vac.new_vaccinations)) OVER (Partition by dea.location ORDER BY dea.location,dea.Date) as RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths$ dea
JOIN PortfolioProject..CovidVaccinations$ vac
	ON  dea.location = vac.location
	AND dea.date = vac.date
WHERE dea.continent is not NULL
ORDER BY 2,3

-- USE CTE

With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(Convert(int,vac.new_vaccinations)) OVER (Partition by dea.location ORDER BY dea.location,dea.Date) as RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths$ dea
JOIN PortfolioProject..CovidVaccinations$ vac
	ON  dea.location = vac.location
	AND dea.date = vac.date
WHERE dea.continent is not NULL
--ORDER BY 2,3
)
SELECT *, (RollingPeopleVaccinated/Population)*100
FROM PopvsVac

-- TEMP TABLE

Drop Table #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into #PercentPopulationVaccinated
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(Convert(int,vac.new_vaccinations)) OVER (Partition by dea.location ORDER BY dea.location,dea.Date) as RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths$ dea
JOIN PortfolioProject..CovidVaccinations$ vac
	ON  dea.location = vac.location
	AND dea.date = vac.date
--WHERE dea.continent is not NULL
--ORDER BY 2,3

SELECT *, (RollingPeopleVaccinated/Population)*100
FROM #PercentPopulationVaccinated


-- Creating view to store data for later visualization


Create View PercentPopulationVaccinated as 

SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(Convert(int,vac.new_vaccinations)) OVER (Partition by dea.location ORDER BY dea.location,dea.Date) as RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths$ dea
JOIN PortfolioProject..CovidVaccinations$ vac
	ON  dea.location = vac.location
	AND dea.date = vac.date
WHERE dea.continent is not NULL
--ORDER BY 2,3


SELECT *
FROM PercentPopulationVaccinated
