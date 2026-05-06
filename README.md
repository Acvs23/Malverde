# Malverde - Sanctions Data Processing
## Datapipeline that produces a reducded dataset of individuals and entities sanctioned by the UK Goverment

The aim if this project was to process and cleanse raw sanctions data from the UK sanctions list (.csv) to create a structured dataset suitable for comparison with the customer records of a ficticious bank.

This file will explain the function and usage of all three files contained in the directory. Given the Size of the original "UK-Sanctions-List.csv", it was unable to be uploaded.

1.) "Pipeline.ipynb"

This .ipynb file is the datapipeline that identifies and extracts the dataset from the original sanctions list. Upon laoding the csv file, it firstly defines two new variables: 
"Name_combined" - Composed of the initial table columns: ["Name 1", "Name 2", "Name 3", "Name 4", "Name 5", "Name 6"]
"Address_combined" - Composed of the initial variables: [""Address Line 1", "Address Line 2", "Address Line 3", "Address Line 4", "Address Line 5", "Address Line 6", "Address Postal Code""]

Secondly, a function called "clean_dob" is used on the variable "D.O.B" (Date of Birth) to clean the variable and return only the known components of the date. For example, A sanctioned individual known to be born in March 1969 would return 03/1969, as oppossed to dd/03/1969 which would be returend withou cleaning.

The next kernel creates the reducded dataset. Beyond the created "Name_combined" and "Address_combined", this dataset requires the following variables from the "UK-Sanctions-List.csv":

    "Unique ID",
    "Last Updated",
    "Gender",
    "Name type",
    "Designation source",
    "Designation Type",
    "D.O.B",
    "Address Country",
    "Country of birth",
    "Nationality(/ies)",
    "National Identifier number",
    "Passport number",
    "Sanctions Imposed",
    "Other Information"

The data is filtered to prevent any duplicate entries. An entry is deemed a duplicate if it contains an identical "Unique ID", "Name_combined" and "D.O.B" to another entry. Additionally, a new column is created called "Address Variations". THis variable counts the number of duplicated events that contain unique "Address_combined" columns.

The following columns are renamed to avoid confusion and make focused searching easier:

   "Name_combined": "Name",
    "D.O.B": "Date of Birth",
    "Country of birth": "Birth Country",
    "Nationality(/ies)": "Nationalities"

2.) "UK_Sanctions_Reduced.csv"

This is the reducded dataset created via the pipeline. It contains 16 columns and 17,103 rows. The values are as followed:

    "Unique ID"                     - Unique ID associated to an given Individual or Entity
    "Name"                          - Full name provided
    "Date of Birth"                 - Date of birth of Individual (If applicable)
    "Gender"                        - Gender of Individual (If applicable)
    "Name Type"                     - Designates if name is an Alias or not
    "Designation Source"            - Organisation or Goverement who applied sanctions
    "Designation Type"              - Designates if sanction is against an Individual or an Entity
    "Last Updated"                  - Time sanctions was lsat revised or updated
    "Address Country"               - Address Country of the Individual or Entity
    "Birth Country"                 - Birth Country of Individual (If applicable)
    "Nationalities"                 - Nationalities of Individual (If applicable)
    "National Identifier Number"    - National identifier number of Individual (If applicable)
    "Passport Number"               - Passport number of Individual (If applicable)
    "Sanctions Imposed"             - Sanctions imposed on Individual or Entity
    "Other Information"             - Explanation of why sanctions were imposed
    "Address Variations"            - Counts the number of unique addresses associated to a given unique entry (Identical Name, Date of birth and Unique ID but alternate addresses)

3.) "Testbed.ipynb"

This is simply an ipynb file where I import the reducded santions list and use the columns to test filtered searching.

There are two types of filtering:

# Exact filtering - This method searches a column for exact words

Format: Dataset[Dataset[" Target Column"] == "Exact Target Word"]

Example: Dataset[Dataset["Unique ID"] == "AFG0001"]

# Contain Filtering - This Method searches a column for a partial component

Format: Dataset[Dataset["Target Column"].str.contains("Target phrase", na=False)]

Example: Dataset[Dataset["Date of Birth"].str.contains("03/1969", na=False)]
