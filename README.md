Data Wrangling: Clinical Trial Dataset (Auralin vs Novodra)
A pandas data cleaning project on a clinical trial dataset for an insulin drug (Auralin) compared against an existing one (Novodra). Four messy tables go in, two clean and analysis-ready CSV files come out.
Tools: Python, pandas, NumPy, regex, Jupyter Notebook

---
The problem

The trial data came split across four files, and each one had quality problems: wrong spellings, mixed formats, missing values, wrong data types, impossible measurements, and data that belonged in a different table. I followed a Assess → Define → Clean → Test approach: find the issues first, write down what to fix, fix it, then check that the fix worked.
Table	Rows	What it holds
`patients`	503	Demographics, address, contact, weight, height, BMI
`treatments`	280	Insulin dose range and HbA1c at start and end of trial
`treatments_cut`	~70	Extra treatment rows that belong with `treatments`
`adverse_reactions`	–	Reactions reported by patients

Output
File	Description
`patients_fully_cleaned.csv`	503 rows, 15 columns, one row per patient
`treatments_fully_cleaned.csv`	349 rows, one row per patient per insulin type, with adverse reaction merged in

---
What I fixed

Quality issues (dirty data)
Table	Issue	Type	Fix

patients	Misspelled name (`Dsvid`)	Accuracy	Corrected to `David`

patients	State written as full name in some rows, abbreviation in others	Consistency	Mapped abbreviations to full names

patients	4-digit zip codes (leading zero lost)	Validity	Padded back to 5 digits

patients	Missing address, city, state, zip, country, contact for 12 patients	Completeness	Filled with `No Data`

patients	Wrong data types on sex, zip code, birthdate	Validity	Converted to correct types (`birthdate` to datetime, `zip_code` to string)

patients	Weight of 48.8 lbs (impossible given the BMI)	Accuracy	Recalculated from BMI and height, set to 107.5

patients	Height of 27 inches (impossible given the BMI)	Accuracy	Recalculated from BMI and weight, set to 72

patients / treatments	Names in inconsistent case	Consistency	Standardised capitalisation

patients / treatments	`given_name`, `surname`, `assigned_sex` naming	Consistency	Renamed to `first_name`, `last_name`, `sex`

treatments	`hba1c_change` missing for many rows, and wrong in some	Completeness / Accuracy	Recomputed as `hba1c_start - hba1c_end` for every row

treatments	`u` unit stuck on dose values	Validity	Removed

treatments	`-` used as a placeholder for "not on this drug"	Validity	Dropped during reshape

Structure issues (messy data)

Contact column held phone and email together. Used regex to pull the email out, 

stripped the rest down to digits for the phone number, then dropped the original column.

Two treatment tables with the same layout. Concatenated `treatments` and `treatments_cut` into one table.

Auralin and Novodra were two separate columns, each holding a dose range as text (`41u - 48u`). 

Reshaped with `melt` into a `type` column, then split the range into `dosage_start` and `dosage_end`.

`adverse_reactions` should not be its own table. Merged into treatments on first and last name.

---
Final columns
patients_fully_cleaned.csv
`patient_id, sex, first_name, last_name, address, city, state, zip_code, country, birthdate, weight, height, bmi, email, phone`

treatments_fully_cleaned.csv
`first_name, last_name, hba1c_start, hba1c_end, hba1c_change, type, dosage_start, dosage_end, adverse_reaction`


```
Skills shown

Systematic data assessment (visual and programmatic)
Handling missing values, wrong types, and impossible values
Regex for splitting combined fields
Reshaping with `melt`, combining with `concat` and `merge`
Documenting a cleaning process so someone else can follow it
Notes
The data is a clinical trial dataset used for practice. It is not real patient data.
```
