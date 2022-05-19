-- creating a table for the crime data joining all months from January,2017 to December, 2018.
select * into PoliceCrimeD
from (select * from jan2017
union all select * from jan2018
union all select * from feb2017
union all select * from feb2018
union all select * from march2017
union all select * from march2018
union all select * from April2017
union all select * from april2018
union all select * from may2017
union all select * from may2018
union all select * from june2017
union all select * from june2018
union all select * from july2017
union all select * from july2018
union all select * from August2017
union all select * from aug2018
union all select * from sept2017
union all select * from sept2018
union all select * from oct2017
union all select * from oct2018
union all select * from nov2017
union all select * from nov2018
union all select * from dec2017
union all select * from dec2018) as CrimeD


--Viewing the table
SELECT *
FROM PoliceCrimeD;

--adding a new column to the table
ALTER TABLE [CrimeWorks].[dbo].[PoliceCrimeD]
ADD [GeoLocation] GEOGRAPHY; 

--generate geo points usings longitudes and latitudes
UPDATE [CrimeWorks].[dbo].[PoliceCrimeD]
SET [GeoLocation] = GEOGRAPHY::Point([Latitude], [Longitude], 4326)
WHERE [Longitude] IS NOT NULL
AND [Latitude] IS NOT NULL
AND CAST([Latitude] AS decimal(10, 6)) BETWEEN -90 AND 90
AND CAST([Longitude] AS decimal(10, 6)) BETWEEN -90 AND 90;

--adding a primary key to the crimedata table
ALTER TABLE [CrimeWorks].[dbo].[PoliceCrimeD]
ADD ID INT IDENTITY;

ALTER TABLE [CrimeWorks].[dbo].[PoliceCrimeD]
ADD CONSTRAINT ID_PK 
PRIMARY KEY NONCLUSTERED (ID);
GO


--Report 1 (SUMMARIZED CRIME REPORT OF GREATER MANCHESTER AND THE POPULATION IN EACH LSOA)
create view New_Summ_Greater_Manc_vehicleCrime_Report
as
select * 
from [PoliceCrimeD] 
where [Crime type] = 'vehicle crime';




--REPORT 2 ANTISOCIAL-BEHAVIOURS CRIMES IN SALFORD 
create view NEW_ANTI_SOCIAL_BEHAVIOURS_CRIMES_IN_SALFORD
as
select *
from [PoliceCrimeD]
where [LSOA name] like 'salf%' and [Crime type] = 'Anti-social behaviour';

select * into TableANTI_SOCIAL_BEHAVIOURS_CRIMES_IN_SALFORD
from ANTI_SOCIAL_BEHAVIOURS_CRIMES_IN_SALFORD;




--Report 3 (THE NUMBER OF ANTI SOCIAL BEHAVIOR CRIMES IN EACH LSOA in SALFORD AND POPULATION)
CREATE VIEW INDEPENDENT_ANTISOCIAL_BEHAVIOR_SALFORD_LSOAs 
AS 
select A.[LSOA name] AS SALFORD_LSOA, [Crime type], COUNT(A.[Crime type]) as number_of_crimes, [all ages] as Population
FROM policecrimed as A
INNER join [output area] as B 
ON A.[LSOA NAME] = B.[LSOA NAME]
where [Crime type] = 'Anti-social behaviour' and A.[LSOA name] LIKE 'SALFORD%'
GROUP BY A.[LSOA name], [all ages], [Crime type]
ORDER BY number_of_crimes DESC;





--REPORT 4 (AN ANALYSIS ON TOTAL CRIMES IN GREATER MANCHESTER)
select ([Crime type]) AS GREATER_MANCHESTER_CRIMES, COUNT(A.[Crime type]) as number_of_crimes
FROM policecrimed as A
INNER join [output area] as B 
ON A.[LSOA NAME] = B.[LSOA NAME]
GROUP BY [Crime type]
order by number_of_crimes desc;


--Report 5 The summarised crime report in greater manchester LSOAs, their population and number of crimes in each.
select A.[LSOA name] AS LSOA, COUNT(A.[Crime type]) as number_of_crimes, [all ages] as Population
FROM policecrimed as A
INNER join [output area] as B 
ON A.[LSOA NAME] = B.[LSOA NAME]
GROUP BY A.[LSOA name], [all ages]
order by number_of_crimes desc;







--Report 6 (THE NUMBER OF VEHICLE CRIMES IN EACH LSOA in GREATER MANCHESTER AND POPULATION)
select A.[LSOA name] AS GreaterManchester_LSOA, [Crime type], COUNT(A.[Crime type]) as number_of_crimes, [all ages] as Population
FROM policecrimed as A
INNER join [output area] as B 
ON A.[LSOA NAME] = B.[LSOA NAME]
where [Crime type] = 'vehicle crime'
GROUP BY A.[LSOA name], [all ages], [Crime type]
order by number_of_crimes desc;
