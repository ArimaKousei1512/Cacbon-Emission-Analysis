# Carbon-Emission-Analysis

# 1.Explore Data
## 1.1 Table 'product_name'
### 
```sql
SELECT * FROM product_emissions LIMIT 5;
```

| id           | company_id | country_id | industry_group_id | year | product_name                                                    | weight_kg | carbon_footprint_pcf | upstream_percent_total_pcf | operations_percent_total_pcf | downstream_percent_total_pcf | 
| -----------: | ---------: | ---------: | ----------------: | ---: | --------------------------------------------------------------: | --------: | -------------------: | -------------------------: | ---------------------------: | ---------------------------: | 
| 10056-1-2014 | 82         | 28         | 2                 | 2014 | Frosted Flakes(R) Cereal                                        | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10056-1-2015 | 82         | 28         | 15                | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10222-1-2013 | 83         | 28         | 8                 | 2013 | Office Chair                                                    | 20.68     | 73                   | 80.63                      | 17.36                        | 2.01                         | 
| 10261-1-2017 | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1488                 | 30.65                      | 5.51                         | 63.84                        | 
| 10261-2-2017 | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1818                 | 25.08                      | 4.51                         | 70.41                        | 



## 1.2 Table 'industry_groups'
###
```sql
SELECT * FROM industry_groups LIMIT 5;
```
| id | industry_group                                                         | 
| -: | ---------------------------------------------------------------------: | 
| 1  | "Consumer Durables, Household and Personal Products"                   | 
| 2  | "Food, Beverage & Tobacco"                                             | 
| 3  | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 
| 4  | "Mining - Iron, Aluminum, Other Metals"                                | 
| 5  | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 
## 1.3 Table 'companies'
###
```sql
SELECT * FROM companies LIMIT 5;
```
| id | company_name                  | 
| -: | ----------------------------: | 
| 1  | "Autodesk, Inc."              | 
| 2  | "Casio Computer Co., Ltd."    | 
| 3  | "Cisco Systems, Inc."         | 
| 4  | "CNX Coal Resources, LP"      | 
| 5  | "Coca-Cola Enterprises, Inc." | 
## 1.4 Table 'countries'
###
```sql
SELECT * FROM countries LIMIT 5;
```
| id | country_name | 
| -: | -----------: | 
| 1  | Australia    | 
| 2  | Belgium      | 
| 3  | Brazil       | 
| 4  | Canada       | 
| 5  | Chile        | 

# 2. Questions to research
## 2.1 Which products contribute the most to carbon emissions?
### AVG Top 10 prodcuct
```sql
SELECT product_name, AVG(carbon_footprint_pcf) AS avg_carbon_footprint
FROM product_emissions
GROUP BY product_name
ORDER BY avg_carbon_footprint DESC
LIMIT 10;
```
| product_name                                                                                                                       | avg_carbon_footprint | 
| ---------------------------------------------------------------------------------------------------------------------------------: | -------------------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | 3718044.0000         | 
| Wind Turbine G132 5 Megawats                                                                                                       | 3276187.0000         | 
| Wind Turbine G114 2 Megawats                                                                                                       | 1532608.0000         | 
| Wind Turbine G90 2 Megawats                                                                                                        | 1251625.0000         | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.0000          | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.0000          | 
| TCDE                                                                                                                               | 99075.0000           | 
| Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.0000           | 
| Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.0000           | 
| Mercedes-Benz SL (SL 350)                                                                                                          | 72000.0000           | 

## 2.2 What are the industry groups of these products?
### 10 industry product 
```sql
WITH top10 AS (
    SELECT product_name, industry_group_id, ROUND(AVG(carbon_footprint_pcf)) AS avg_carbon_footprint
    FROM product_emissions
    GROUP BY product_name, industry_group_id
    ORDER BY avg_carbon_footprint DESC
    LIMIT 10
)
SELECT tp.product_name, i.industry_group, tp.avg_carbon_footprint
FROM top10 tp
JOIN industry_groups i ON tp.industry_group_id = i.id
ORDER BY tp.avg_carbon_footprint DESC;
```
| product_name                                                                                                                       | industry_group                     | avg_carbon_footprint | 
| ---------------------------------------------------------------------------------------------------------------------------------: | ---------------------------------: | -------------------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | Electrical Equipment and Machinery | 3718044              | 
| Wind Turbine G132 5 Megawats                                                                                                       | Electrical Equipment and Machinery | 3276187              | 
| Wind Turbine G114 2 Megawats                                                                                                       | Electrical Equipment and Machinery | 1532608              | 
| Wind Turbine G90 2 Megawats                                                                                                        | Electrical Equipment and Machinery | 1251625              | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | Automobiles & Components           | 191687               | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | Materials                          | 167000               | 
| TCDE                                                                                                                               | Materials                          | 99075                | 
| Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | Automobiles & Components           | 91000                | 
| Mercedes-Benz S-Class (S 500)                                                                                                      | Automobiles & Components           | 85000                | 
| Mercedes-Benz SL (SL 350)                                                                                                          | Automobiles & Components           | 72000                | 
## What are the industries with the highest contribution to carbon emissions?

###
```sql
WITH TopIndustries AS (
    SELECT industry_group_id, AVG(carbon_footprint_pcf) AS avg_carbon_footprint
    FROM product_emissions
    GROUP BY industry_group_id
    ORDER BY avg_carbon_footprint DESC
    LIMIT 10
)
SELECT i.industry_group, ti.avg_carbon_footprint
FROM TopIndustries ti
JOIN industry_groups i ON ti.industry_group_id = i.id
where ti.avg_carbon_footprint = (
  select max(avg_carbon_footprint) from TopIndustries);
```
| industry_group                     | avg_carbon_footprint | 
| ---------------------------------: | -------------------: | 
| Electrical Equipment and Machinery | 891050.7273          | 
## 2.3 What are the companies with the highest contribution to carbon emissions?
###
```sql
WITH TopProducts AS (
    SELECT company_id, AVG(carbon_footprint_pcf) AS avg_carbon_footprint
    FROM product_emissions
    GROUP BY company_id
    ORDER BY avg_carbon_footprint DESC
    LIMIT 1
)
SELECT c.company_name, tp.avg_carbon_footprint, i.industry_group
FROM TopProducts tp
JOIN companies c ON tp.company_id = c.id
JOIN product_emissions pe ON tp.company_id = pe.company_id
JOIN industry_groups i ON pe.industry_group_id = i.id
GROUP BY c.company_name, i.industry_group, tp.avg_carbon_footprint
ORDER BY tp.avg_carbon_footprint DESC
LIMIT 1;
```
| company_name                           | avg_carbon_footprint | industry_group                     | 
| -------------------------------------: | -------------------: | ---------------------------------: | 
| "Gamesa Corporación Tecnológica, S.A." | 2444616.0000         | Electrical Equipment and Machinery | 
## 2.4 What are the companies with the highest contribution to carbon emissions?
###
```sql
SELECT c.company_name, SUM(p.carbon_footprint_pcf) AS total_emissions
FROM product_emissions p
JOIN companies c ON p.company_id = c.id
GROUP BY c.company_name
ORDER BY total_emissions DESC
LIMIT 10;
```
| company_name                            | total_emissions | 
| --------------------------------------: | --------------: | 
| "Gamesa Corporación Tecnológica, S.A."  | 9778464         | 
| Daimler AG                              | 1594300         | 
| Volkswagen AG                           | 655960          | 
| "Mitsubishi Gas Chemical Company, Inc." | 212016          | 
| "Hino Motors, Ltd."                     | 191687          | 
| Arcelor Mittal                          | 167007          | 
| Weg S/A                                 | 160655          | 
| General Motors Company                  | 137007          | 
| "Lexmark International, Inc."           | 132012          | 
| "Daikin Industries, Ltd."               | 105600          | 
## 2.5 What are the countries with the highest contribution to carbon emissions?
###    
```sql
SELECT co.country_name, SUM(p.carbon_footprint_pcf) AS total_emissions
FROM product_emissions p
JOIN countries co ON p.country_id = co.id
GROUP BY co.country_name
ORDER BY total_emissions DESC
LIMIT 10;
```
| country_name | total_emissions | 
| -----------: | --------------: | 
| Spain        | 9786130         | 
| Germany      | 2251225         | 
| Japan        | 653237          | 
| USA          | 518381          | 
| South Korea  | 186965          | 
| Brazil       | 169337          | 
| Luxembourg   | 167007          | 
| Netherlands  | 70417           | 
| Taiwan       | 62875           | 
| India        | 24574           | 
## 2.6 What is the trend of carbon footprints (PCFs) over the years?
###
```sql
SELECT year, AVG(carbon_footprint_pcf) AS total_emissions
FROM product_emissions
GROUP BY year
ORDER BY year ASC;
```
| year | total_emissions | 
| ---: | --------------: | 
| 2013 | 2399.3190       | 
| 2014 | 2457.5827       | 
| 2015 | 43188.9044      | 
| 2016 | 6891.5210       | 
| 2017 | 4050.8452       | 
## 2.7 Which industry groups have demonstrated the most notable decrease in carbon footprints (PCFs) over time?
### 
``` SQL
SELECT 
    ind_gr.industry_group AS "Industry Group",
    ROUND(AVG(CASE WHEN pr_em.year = 2013 THEN pr_em.carbon_footprint_pcf ELSE NULL END), 2) AS "2013 Emission",
    ROUND(AVG(CASE WHEN pr_em.year = 2014 THEN pr_em.carbon_footprint_pcf ELSE NULL END), 2) AS "2014 Emission",
    ROUND(AVG(CASE WHEN pr_em.year = 2015 THEN pr_em.carbon_footprint_pcf ELSE NULL END), 2) AS "2015 Emission",
    ROUND(AVG(CASE WHEN pr_em.year = 2016 THEN pr_em.carbon_footprint_pcf ELSE NULL END), 2) AS "2016 Emission",
    ROUND(AVG(CASE WHEN pr_em.year = 2017 THEN pr_em.carbon_footprint_pcf ELSE NULL END), 2) AS "2017 Emission"
FROM 
    product_emissions AS pr_em
LEFT JOIN 
    industry_groups AS ind_gr 
ON 
    ind_gr.id = pr_em.industry_group_id
GROUP BY 
    ind_gr.industry_group
ORDER BY 
    ind_gr.industry_group;
```
| Industry Group                                                         | 2013 Emission | 2014 Emission | 2015 Emission | 2016 Emission | 2017 Emission | 
| ---------------------------------------------------------------------: | ------------: | ------------: | ------------: | ------------: | ------------: | 
| "Consumer Durables, Household and Personal Products"                   | [NULL]        | [NULL]        | 116.38        | [NULL]        | [NULL]        | 
| "Food, Beverage & Tobacco"                                             | 94.25         | 99.44         | 0.00          | 4011.56       | 143.73        | 
| "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | [NULL]        | [NULL]        | 685.31        | [NULL]        | [NULL]        | 
| "Mining - Iron, Aluminum, Other Metals"                                | [NULL]        | [NULL]        | 2727.00       | [NULL]        | [NULL]        | 
| "Pharmaceuticals, Biotechnology & Life Sciences"                       | 16135.50      | 40215.00      | [NULL]        | [NULL]        | [NULL]        | 
| "Textiles, Apparel, Footwear and Luxury Goods"                         | [NULL]        | [NULL]        | 14.33         | [NULL]        | [NULL]        | 
| Automobiles & Components                                               | 26037.80      | 20910.45      | 37146.68      | 40138.09      | [NULL]        | 
| Capital Goods                                                          | 5015.83       | 10411.00      | 3505.00       | 796.13        | 18989.80      | 
| Chemicals                                                              | [NULL]        | [NULL]        | 1949.03       | [NULL]        | [NULL]        | 
| Commercial & Professional Services                                     | 144.63        | 119.25        | [NULL]        | 96.33         | 370.50        | 
| Consumer Durables & Apparel                                            | 286.70        | 113.10        | [NULL]        | 40.07         | [NULL]        | 
| Containers & Packaging                                                 | [NULL]        | [NULL]        | 373.50        | [NULL]        | [NULL]        | 
| Electrical Equipment and Machinery                                     | [NULL]        | [NULL]        | 891050.73     | [NULL]        | [NULL]        | 
| Energy                                                                 | 750.00        | [NULL]        | [NULL]        | 2506.00       | [NULL]        | 
| Food & Beverage Processing                                             | [NULL]        | [NULL]        | 7.05          | [NULL]        | [NULL]        | 
| Food & Staples Retailing                                               | [NULL]        | 77.30         | 70.60         | 0.50          | [NULL]        | 
| Gas Utilities                                                          | [NULL]        | [NULL]        | 61.00         | [NULL]        | [NULL]        | 
| Household & Personal Products                                          | 0.00          | [NULL]        | [NULL]        | [NULL]        | [NULL]        | 
| Materials                                                              | 4177.35       | 1513.56       | [NULL]        | 1401.06       | 11217.74      | 
| Media                                                                  | 2411.25       | 2411.25       | 479.75        | 602.67        | [NULL]        | 
| Retailing                                                              | [NULL]        | 6.33          | 5.50          | [NULL]        | [NULL]        | 
| Semiconductors & Semiconductor Equipment                               | [NULL]        | 16.67         | [NULL]        | 2.00          | [NULL]        | 
| Semiconductors & Semiconductors Equipment                              | [NULL]        | [NULL]        | 1.00          | [NULL]        | [NULL]        | 
| Software & Services                                                    | 1.50          | 29.20         | 1523.73       | 2538.44       | 690.00        | 
| Technology Hardware & Equipment                                        | 1053.45       | 1780.44       | 1895.66       | 65.25         | 788.34        | 
| Telecommunication Services                                             | 52.00         | 45.75         | 45.75         | [NULL]        | [NULL]        | 
| Tires                                                                  | [NULL]        | [NULL]        | 1011.00       | [NULL]        | [NULL]        | 
| Tobacco                                                                | [NULL]        | [NULL]        | 1.00          | [NULL]        | [NULL]        | 
| Trading Companies & Distributors and Commercial Services & Supplies    | [NULL]        | [NULL]        | 39.83         | [NULL]        | [NULL]        | 
| Utilities                                                              | 61.00         | [NULL]        | [NULL]        | 61.00         | [NULL]        | 






