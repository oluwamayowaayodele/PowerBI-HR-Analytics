
# People Analytics in Power BI

Atlas Labs HR Analytics Project
Overview

This project centres on people analytics Atlas Labs, a fictitious software company with a focus on monitoring key employee metrics and identifying trends relating to onboarding, employee ethnicity, education, attrition and more. 

The analysis was conducted using Power BI, focusing on data importation, transformation, modeling, and visualization to offer Atlas Lab's people team a platform to derive insights with respect to its employees.

### Steps followed

- Step 1 : Loaded data into Power BI desktop. Data split across multiple csv files.
- Step 2 : Distinguished dimension tables from fact table by applying 'Dim' and 'Fact' prefixes to respective tables.
- Step 3 : Created DimDate table using DimDate.txt DAX and connect to FactPerformanceRating table.
- Step 4 : Established inactive relationship between DimDate and DimEmployee, in addition to DimEducationLevel to DimEmployee.
- Step 5 : Provided context to JobSatisfaction, WorkLifeBalance, RelationshipSatisfaction and EnvironmentSatisfaction by connecting DimSatisfiedLevel with EnvironmentSatisfaction as active relationship.
- Step 6 : Provided context to SelfRating and ManagerRating columns by connecting with DimRatingLevel and establishing SelfRating as active relationship.
- Step 7 : Wrote the following DAX measures to begin Exploratory data analyses and provide high level insights for Overview Visualizations.

        - TotalEmployees -- DISTINCTCOUNT(DimEmployee[EmployeeID])
        - ActiveEmployees -- ActiveEmployees = CALCULATE(COUNT(DimEmployee[Attrition]),DimEmployee[Attrition] = "No")
        - InactiveEmployees -- InactiveEmployees = CALCULATE(COUNT(DimEmployee[Attrition]),DimEmployee[Attrition] = "Yes")
        - % Attrition Rate -- % Attrition Rate = CALCULATE(DIVIDE([InactiveEmployees],[TotalEmployees]))
        - TotalEmployeesDate -- TotalEmployeesDate = CALCULATE([TotalEmployees],USERELATIONSHIP(DimEmployee[HireDate],DimDate[Date]))

- Step 8 : Following the initial modeling and creation of high level views on the Overview page, focus proceeded to build Visualizations on the demographic, experience and turnover of Atlas Labs employees during the period in question. 
- Step 9 : Created cards to visualise oldest and youngest employees across the Atlas Labs cohort.
- Step 10 : Displayed age distribution of the Atlas Labs cohort by use of conditional columns to create relevant age bins.

        - = Table.AddColumn(#"Changed Type", "AgeBins", each if [Age] >= 50 then "50>" else if [Age] >= 40 then "40-49" else if [Age] >= 30 then "30-39" else if [Age] >= 20 then "20-29" else "<20")


- Step 11 : Used a clustered column bar chart to breakdown the age cohort of Atlas Labs employees by gender. I created an additional visual to illustrate Atlas Labs employees by their education level, and a further visual to view employees by their marital status. Finally, I created a visualization to see all employees by their ethnicity and average salary - education level was also added on this chart. 

        - AverageSalary = CALCULATE(AVERAGE(DimEmployee[Salary]))

    ![Atlas Labs HR Analytics2-1](https://github.com/user-attachments/assets/66138e9f-4aaf-4ece-a00c-c14f3b5f3f52)


- Step 12 : Opened Power Query Editor and combined 'FirstName' and 'LastName' to create 'FullName'.
- Step 13 : Used this as a slicer on a new page titled 'Select Employee'.
- Step 14 : Created a card visual with 'HireDate' column; renamed this 'StartDate'.
- Step 15 : Created new measure 'LastReviewDate' to display employees most recent review date.

        - LastReviewDate = IF(CALCULATE(MAX(FactPerformanceRating[ReviewDate])) = BLANK(), "No Review Yet", MAX(FactPerformanceRating[ReviewDate]))
- Step 16 : Created new measure 'NextReviewDate' to display employees next review date. 

        - NextReviewDate = VAR reviewOrHire =
               IF(
                MAX(FactPerformanceRating[ReviewDate]) = BLANK(),
                MAX(DimEmployee[HireDate]),
                MAX(FactPerformanceRating[ReviewDate])
                ) RETURN    
                reviewOrHire + 365

![Atlas Labs HR Analytics2-2](https://github.com/user-attachments/assets/095cc007-65b1-4d32-aff2-cc28e4cdac13)


- Step 17 : Moved to visualising experience outcomes at Atlas Labs. First created a JobSatisfaction measure to show employee responses over the years. 

        - JobSatisfaction = MAX(FactPerformanceRating[JobSatisfaction])
- Step 18 : Created measures for the remaining satisfaction metrics without an active connection to DimSatisfiedLevel ('EnvironmentSatisfaction', 'RelationshipSatisfaction', 'WorkLifeBalance') using USERELATIONSHIP( ) function. Also created SelfRating and ManagerRating measures and visualised them by YEAR as well as including RatingLevel and SatisfactionLevel tables.

        - EnvironmentSatisfaction = CALCULATE(
                            MAX (   FactPerformanceRating[EnvironmentSatisfaction]  ),
                            USERELATIONSHIP (   FactPerformanceRating[EnvironmentSatisfaction], DimSatisfiedLevel[SatisfactionID]   ))

        - RelationshipSatisfaction = CALCULATE(
                           MAX( FactPerformanceRating[RelationshipSatisfaction]  ),
                           USERELATIONSHIP(FactPerformanceRating[RelationshipSatisfaction],DimSatisfiedLevel[SatisfactionID]    ))
                           
        - WorkLifeBalance = CALCULATE(
                    MAX(    FactPerformanceRating[WorkLifeBalance]  ),
                    USERELATIONSHIP(FactPerformanceRating[WorkLifeBalance], DimSatisfiedLevel[SatisfactionID]   ))

        - SelfRating = CALCULATE(
            MAX(    FactPerformanceRating[SelfRating]   ),
            USERELATIONSHIP(FactPerformanceRating[SelfRating], DimRatingLevel[RatingID] ))
            
        - ManagerRating = CALCULATE(
                MAX(    FactPerformanceRating[ManagerRating]    ),
                USERELATIONSHIP(FactPerformanceRating[ManagerRating], DimRatingLevel[RatingID]  ))

![Atlas Labs HR Analytics2-3](https://github.com/user-attachments/assets/3853694d-d9f7-4b90-a22e-90bf475cf8d5)
        
- Step 19 : Now moving to creating metrics around attrition at different points in time, use of the USERELATIONSHIP( ) function alongside HireDate helps with creating measures for InactiveEmployeesDate and ActiveEmployeesDate are created - these are used for creating % Attrition Date which shows attrition at different points in time.

        - EnvironmentSatisfaction = CALCULATE(
                            MAX (   FactPerformanceRating[EnvironmentSatisfaction]  ),
                            USERELATIONSHIP (   FactPerformanceRating[EnvironmentSatisfaction], DimSatisfiedLevel[SatisfactionID]   ))

        - RelationshipSatisfaction = CALCULATE(
                           MAX( FactPerformanceRating[RelationshipSatisfaction]  ),
                           USERELATIONSHIP(FactPerformanceRating[RelationshipSatisfaction],DimSatisfiedLevel[SatisfactionID]    ))
                           
        - WorkLifeBalance = CALCULATE(
                    MAX(    FactPerformanceRating[WorkLifeBalance]  ),
                    USERELATIONSHIP(FactPerformanceRating[WorkLifeBalance], DimSatisfiedLevel[SatisfactionID]   ))

        - SelfRating = CALCULATE(
            MAX(    FactPerformanceRating[SelfRating]   ),
            USERELATIONSHIP(FactPerformanceRating[SelfRating], DimRatingLevel[RatingID] ))
            
        - ManagerRating = CALCULATE(
                MAX(    FactPerformanceRating[ManagerRating]    ),
                USERELATIONSHIP(FactPerformanceRating[ManagerRating], DimRatingLevel[RatingID]  ))

- Step 20 : Finally visualise Attrition over the entire time period, HireDate, Tenure, Business Travel patterns, Overtime patterns, sex and department/job role.

![Atlas Labs HR Analytics2-4](https://github.com/user-attachments/assets/3759dca9-a792-4789-925e-9eabb069732f)





### TLDR of the above
#### The steps above prioritised:


Data Acquisition and Preparation

The dataset comprises various employee attributes, including demographics, job roles, performance metrics, and attrition indicators. The following steps were undertaken:

    Data Importation: CSV files containing employee data were loaded into Power BI.

    Data Cleaning: Handled missing values, corrected inconsistencies, and standardized data formats to ensure accuracy.

    Data Modeling: Established relationships between tables using a star schema, with fact tables capturing employee records and dimension tables detailing attributes like departments and job roles.

Exploratory Data Analysis (EDA)

Initial analysis focused on understanding the workforce composition and identifying trends:

    Employee Demographics: Analyzed distributions of age, gender, marital status, and ethnicity to understand the diversity within the company. 

    Departmental Distribution: Examined employee counts across departments and job roles to identify staffing patterns.

    Hiring Trends: Assessed hiring patterns over time to determine periods of significant growth.

Attrition Analysis

Understanding employee attrition was crucial. The following analyses were performed:

    Attrition Rate Calculation: Computed the percentage of employees leaving the company over a specified period.

    Attrition by Job Role and Department: Identified roles and departments with high turnover rates.

    Impact of Overtime and Travel: Analyzed how factors like overtime and travel frequency correlate with attrition.

    Later iterations will almost certainly look more closely at the relationship between demographics, ethnicity and attrition alongside outcomes in overall happiness at the company.

Relevant Findings

    High Attrition in Specific Roles: Sales roles exhibited higher attrition rates, indicating potential issues related to job satisfaction, workload or necessity.

    Influence of Overtime and Travel: Employees required to work overtime or travel frequently showed higher attrition rates, suggesting a need for work-life balance initiatives.

    Tenure and Attrition: Employees with shorter tenure, particularly those with less than a year at the company, had the highest attrition rates, highlighting the importance of improving onboarding and early engagement strategies.

    Earning differentials: Mixed ethnic groups on average earned 8% less than their white, and Native American counterparts. Conversely
    


Conclusion

The analysis provided insights into factors affecting employee retention at Atlas Labs. Recommendations include:

    Addressing High-Turnover Roles: Implement targeted retention strategies for roles with high attrition.

    Work-Life Balance Initiatives: Develop policies to mitigate the impact of overtime and travel on employee satisfaction.

    Enhanced Onboarding Programs: Strengthen support for new employees to reduce early-stage attrition.

With the above, a company like Atlas Labs can be in a stronger position to make data-driven decisions and improve employee satisfaction as well as retention.
