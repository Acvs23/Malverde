# Malverde - Sanctions Data Processing
### Datapipeline that produces a reduced dataset of individuals and entities sanctioned by the UK Goverment

The aim of this project was to process and cleanse raw sanctions data from the UK sanctions list (.csv) to create a structured dataset suitable for comparison with the customer records of a fictitious bank.

This file will explain the function and usage of all three files contained in the directory. Given the size of the original "UK-Sanctions-List.csv", it was unable to be uploaded to GitHub. It can be downloaded here: https://www.gov.uk/government/publications/the-uk-sanctions-list

#### Dependencies
- Python 3.x
- pandas
- Jupyter Notebook

## 1.) "Pipeline.ipynb"

One of the key goals of the pipeline is to create a reducded dataset that is smaller than the 58x57,000 original datset whilst maintaing unique identifiers capable of identyfying sanctioned individuals.

This .ipynb file is the data pipeline that identifies and extracts the dataset from the original sanctions list. Upon loading the csv file, it firstly defines two new variables: 
"Name_combined" - Composed of the initial table columns: ["Name 1", "Name 2", "Name 3", "Name 4", "Name 5", "Name 6"], and 
"Address_combined" - Composed of the initial variables: [""Address Line 1", "Address Line 2", "Address Line 3", "Address Line 4", "Address Line 5", "Address Line 6", "Address Postal Code""]

Another kernel creates the reduced dataset. Beyond the created "Name_combined" and "Address_combined", this dataset requires the following variables from the "UK-Sanctions-List.csv":

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

The fields "Address Country", "Birth Country", and "Nationalities" collectively represent the associated countries of an individual or entity.

Additionally, a new column is created called "Address Variations". This variable counts the number of duplicated events that contain unique "Address_combined" columns (See Data Quality Assessment).

The following columns are renamed to avoid confusion and make focused searching easier:

    "Name_combined": "Name",
    "D.O.B": "Date of Birth",
    "Country of birth": "Birth Country",
    "Nationality(/ies)": "Nationalities"

#### Data Quality Assessment

    - Numerous fields were either empty or NaN values, either due to missing data or not applying to entities. 
        - These values were universally filled and replaced with "Unknown" for visual improvement of the dataset 
    - A function called "clean_dob" was used on the variable "D.O.B" (Date of Birth) to clean the variable and return only the known components of the date. 
        -For example, A sanctioned individual known to be born in March 1969 would return 03/1969, as oppossed to dd/03/1969 which would be returned without cleaning.
    - Many entries were deemed to be duplicate entries - entries containing an identical combination of "Unique ID", "Name_combined" and "D.O.B" to another entry. 
        - A majority of the duplicate events varied only in terms of in-country address. 
        - Consequently, only a single event is taken from any duplicate events and added to the reduced datasets. 
        - The number of varying addresses is recorded and stored in the column "Address Variations".

#### How to Run the Pipeline

1. Download the UK Sanctions List from:
https://www.gov.uk/government/publications/the-uk-sanctions-list

2. Save the CSV file as:
UK-Sanctions-List.csv

3. Place it in the same directory as Pipeline.ipynb

4. Install dependencies:
pip install pandas

5. Run Pipeline.ipynb

6. Output:
UK_Sanctions_Reduced.csv will be generated

## 2.) "UK_Sanctions_Reduced.csv"

This is the reduced dataset created via the pipeline. It contains 16 columns and 17,103 rows. These columns were choosen as the optimal columns for cross checking sacntioned individuals against a fictitious banks customer records. The columns are as followed:

    "Unique ID"                     - Unique ID associated to a given Individual or Entity                        - Information
    "Name"                          - Full name provided                                                          - Identifier
    "Date of Birth"                 - Date of birth of Individual (If applicable)                                 - Identifier
    "Gender"                        - Gender of Individual (If applicable)                                        - Identifier
    "Name Type"                     - Designates if name is an Alias or not                                       - Information
    "Designation Source"            - Organisation or Government who applied sanctions                            - Information
    "Designation Type"              - Designates if sanction is against an Individual or an Entity                - Information
    "Last Updated"                  - Time sanctions was last revised or updated                                  - Information
    "Address Country"               - Address Country of the Individual or Entity                                 - Identifier
    "Birth Country"                 - Birth Country of Individual (If applicable)                                 - Identifier
    "Nationalities"                 - Nationalities of Individual (If applicable)                                 - Identifier
    "National Identifier Number"    - National identifier number of Individual (If applicable)                    - Identifier
    "Passport Number"               - Passport number of Individual (If applicable)                               - Identifier
    "Sanctions Imposed"             - Sanctions imposed on Individual or Entity                                   - Information
    "Other Information"             - Explanation of why sanctions were imposed                                   - Information
    "Address Variations"            - Counts the number of unique addresses associated to a given unique entry    - Information

## 3.) "Testbed.ipynb"

This is simply a .ipynb file where I import the reduced sanctions list and use the columns to test filtered searching.

Due to visual display limitations of Github, "Testbed.ipynb" must be downloaded to view the tables without it being cut off by the screen. To run focused searches of your own, "UK_Sanctions_Reduced.csv" will also need to be downloaded and kept in the same directory in order for "Testbed.ipynb" to function properly.

There are two types of filtering:

#### Exact filtering - This method searches a column for exact words

    Format: Dataset[Dataset["Target Column"] == "Exact Target Word"]

    Example:
    Dataset[Dataset["Unique ID"] == "AFG0001"]

#### Contain Filtering - This Method searches a column for a partial component

    Format: Dataset[Dataset["Target Column"].str.contains("Target phrase", na=False)]

    Example:
    Dataset[Dataset["Date of Birth"].str.contains("03/1969", na=False)]
