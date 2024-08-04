# Data Cleaning With MySQL?
Kaggle Dataset for COVID 19 2019 layoffs.

---Data Cleaning

SELECT *
FROM layoffs;

 --1- Remove the Duplicate
-- 2- Standardize Data
-- 3- Null values or Blanks values
-- 4- Remove any columns and row

CREATE TABLE layoffs_staging
LIKE layoffs;

SELECT *
FROM layoffs_staging;


INSERT layoffs_staging
SELECT *
FROM layoffs;

SELECT *
FROM layoffs_staging;

SELECT *,
ROW_NUMBER() OVER(
PARTITION BY 
company,industry,total_laid_off,percentage_laid_off,`date`) AS row_num
FROM layoffs_staging;

WITH duplicate_cte AS
(
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY 
company,industry,total_laid_off,percentage_laid_off,`date`) AS row_num
FROM layoffs_staging
)
SELECT  *
FROM duplicate_cte
WHERE row_num>1;

SELECT *
FROM layoffs_staging
WHERE company LIKE 'Career%';


CREATE TABLE `layoffs_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

SELECT *
FROM layoffs_staging2;

INSERT INTO layoffs_staging2
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY 
company,industry,total_laid_off,percentage_laid_off,`date`) AS row_num
FROM layoffs_staging;


SELECT *
FROM layoffs_staging2
WHERE row_num >1
;
DELETE
FROM layoffs_staging2
WHERE row_num >1
;

-- 2- Standardize Data

SELECT DISTINCT company,(TRIM(company))
FROM layoffs_staging2;

SELECT company,(TRIM(company))
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET company =TRIM(company);


SELECT industry
FROM layoffs_staging2
WHERE industry LIKE 'Crypto%';

UPDATE layoffs_staging2
SET industry='Crypto'
WHERE industry LIKE 'Crypto%';

SELECT DISTINCT country, TRIM(TRAILING '.' FROM country )
FROM layoffs_staging2
order  by 1;


update layoffs_staging2
set country=TRIM(TRAILING '.' FROM country)
where country like 'United States%';

SELECT `date`,
STR_TO_DATE(`date`,'%m/%d/%Y')
FROM layoffs_staging2;

update layoffs_staging2
set `date` =STR_TO_DATE(`date`,'%m/%d/%Y');

alter table layoffs_staging2
modify column `date` DATE; 

-- 3- Null values or Blanks values
SELECT *
FROM layoffs_staging2
WHERE total_laid_off IS NULL 
AND percentage_laid_off IS NULL;



SELECT *
FROM layoffs_staging2
WHERE industry IS NULL OR industry ='';

SELECT *
FROM layoffs_staging2
WHERE company='Airbnb';

SELECT *
FROM layoffs_staging2 t1
JOIN layoffs_staging2 t2
ON t1.company=t2.company 
WHERE t1.industry Is NULL OR t2.industry=''
AND t2.industry IS NOT NULL; 

UPDATE layoffs_staging2
SET industry= null
WHERE industry='';

SELECT *
FROM layoffs_staging2;

SELECT *
FROM layoffs_staging2
WHERE total_laid_off IS NULL 
AND percentage_laid_off IS NULL;

DELETE
FROM layoffs_staging2
WHERE total_laid_off IS NULL 
AND percentage_laid_off IS NULL;

SELECT *
FROM layoffs_staging2;
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
