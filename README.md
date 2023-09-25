# sqlite_database_operations

# %% [markdown]
# # **Week 3 Homework Assignment: Introduction to Databases with SQLite**
# 
# # **Objective**:
# Introduction to the world of databases, starting with SQLite. Integrate data processing with Python, utilize Pandas for exploratory data analysis, and conduct database operations using SQLite.
# 
# # **Instructions**:
# 
# 

# %% [markdown]
# ## **1. Data Exploration and Analysis**:
# 1.1 Import pricing transparency datasets from at least two different hospital systems into a Pandas DataFrame. You can use one of the examples provided in the WK3/data folder, or try finding a hospital on your own. The google search approach I used to find these ones was: `{hospital name} price transparency machine readible` - replacing hospital name with the name of the hospital you want to find data for. There will typically be a page that contains download links for a .CSV, .JSON, or .XLS(X) file.  
#   - Conduct a basic exploratory analysis using Python, focusing on aspects such as data distribution, missing values, and basic statistics.
#   - Document any captivating insights or observations derived from your analysis.
#   - My expection is to have at least basic descriptive statistics for numerical columns, and frequency counts for categorical columns
# 
# 

# %% [markdown]
# ### Import packages and files

# %%
import pandas as pd
import numpy as np

sbu = pd.read_csv(r"C:\Users\Xavier\Downloads\stonybrook.csv")
stjoe = pd.read_excel(r'c:\Users\Xavier\Downloads\1013998426_wsnchs-north-inc.-dba-st.-joseph-hospital_standardcharges.xlsx')

# %% [markdown]
# ### Analyze the first Hospital

# %% [markdown]
# #### Cleaning Data
# 

# %%
sbu.columns.str.strip()
sbu.columns.str.replace(' ','_')

# %%
sbu.shape

# %%
sbu.head(5)

# %%
sbu.tail(5)

# %% [markdown]
# #### Descriptive Statistics

# %%

sbu.isnull().sum()

# %%
sbu = sbu.fillna(0)
sbu.isnull().sum()

# %%
sbu.describe()

# %%
median_GrossCharge = sbu['Gross charge'].median()
print('The median gross charge is', median_GrossCharge)

median_DiscountedCashPrice = sbu['Discounted cash price'].median()
print('The median discounted cash price is', median_DiscountedCashPrice)

median_DerivedContractedRate = sbu['Derived contracted rate'].median()
print('The median derived contacted rate is', median_DerivedContractedRate)

# %%
mode_GrossCharge = sbu['Gross charge'].mode()
print('The median gross charge is', mode_GrossCharge)

mode_DiscountedCashPrice = sbu['Discounted cash price'].mode()
print('The median discounted cash price is',mode_DiscountedCashPrice)

mode_DerivedContractedRate = sbu['Derived contracted rate'].mode()
print('The median derived contacted rate is', mode_DerivedContractedRate)

# %% [markdown]
# #### Frequency Counts

# %%
sbu.columns.values

# %%


# %% [markdown]
# ### Analyze The Second Hospital: St Joseph's Hospital

# %% [markdown]
# #### Cleaning the Data

# %%
stjoe.head(5)

# %%
stjoe.tail(5)

# %%
stjoe.drop_duplicates(inplace = True)

# %%
stjoe.tail(5)

# %% [markdown]
# I thought that some values under Charge Description might be duplicates but they vary in their National Drug Codes

# %%
stjoe.isnull().sum()

# %%
stjoe.describe()

# %% [markdown]
# #### Descriptive Statistics

# %%
mean_price = stjoe['Price'].mean()
print('the mean price is', mean_price)

median_price = stjoe['Price'].median()
print('the median price is', median_price)

mode_price = stjoe['Price'].mode().iloc[0]
print('the mode price is', mode_price)

std_price = stjoe['Price'].std()
print('the standard dcviation of price is', std_price)

var_price = stjoe['Price'].var()
print('the standard dcviation of price is', var_price)


# %% [markdown]
# #### Frequency Counts

# %%
stjoe.columns = stjoe.columns.str.strip()
stjoe.columns = stjoe.columns.str.replace('/','_')
stjoe.columns

# %%
freq_BillingChargeCode = stjoe['Billing_Charge Code'].value_counts()
print('\nThis is the frequency count for Billing/Charge Code\n', freq_BillingChargeCode)

freq_ChargeDescription = stjoe['Charge Description'].value_counts()
print('\nThis is the frequency count for charge description\n',freq_ChargeDescription)

# %%
freq_RevCode = stjoe['Rev Code'].value_counts()
print('\nThis is the frequency count for revenue code\n', freq_RevCode)

# %% [markdown]
# ## **2. SQLite Database Operations**:
# 
# 

# %% [markdown]
# ### 2.1 Create a local SQLite database called `health.db`
# 

# %%
import sqlite3
connection = sqlite3.connect('health16.db')


# %% [markdown]
# ### 2.2 *Manual table creation*: Manually create a table (schema) using `CREATE TABLE` SQL query. 
# #### The table should contain at least 5 columns. Please have at least two columns be numerical columns, and two columns should be categorical (string) columns. Then write at least one `INSERT INTO` SQL query to populate 1 or 2 rows worth of fake data. 
# 

# %%
c.close
c = connection.cursor()
c.execute('''
CREATE TABLE stjosephs (firstName TEXT, lastName TEXT, dob INT, income INT, zipCpde INT)
''')

# %%
c.execute("""
            SELECT name
            FROM sqlite_master
            WHERE type='table'
            ORDER BY name;
          """)
print(c.fetchall())

# %%
c.execute('''
INSERT INTO stjosephs(firstName , lastName , dob , income ,zipCpde )
VALUES('XAVIER',    'ALEXANDER',    12021997,    100000,    11413)
    ''')

c.execute('SELECT * FROM stjosephs')
print(c.fetchall())


# %%
connection.commit()
connection.close()

# %% [markdown]
# ### 2.3. *Automatic table creation*: Implement the `to_sql` function from Pandas to take the example data from Part 1 of this assignment into the SQLite database.
# 

# %%
con = sqlite3.connect('health22.db')


stjoe.to_sql('Price', con=con, if_exists='replace')

cur = con.cursor()
res = cur.execute('''
                  SELECT * FROM Price  
                  ''')
print(res.fetchone())

# %% [markdown]
# ## **Submission**:
# - Initiate a new GitHub repository titled `sqlite_database_operations` in your GitHub account.
# - Leverage components of code from prior assignments where necessary.
# - Configure your GitHub repository to include:
#   - Python scripts (or colab notebook) illustrating your exploratory data analysis and database transactions.
#   - Your custom SQL queries and analysis (if you embarked on this optional task).
#   - A comprehensive README.md file that provides:
#     - Details on the datasets you've selected.
#     - An account of the exploratory data analysis process.
#     - Instructions to replicate your SQLite database setup.
#     - Any other pertinent documentation or guidelines.
# - Present the URL of your repository as your assignment submission.
# - Confirm that your repository is public to ensure it's available for evaluation.
# 
# **Tip**: The process of data preprocessing prior to its transfer to any database is crucial. Not only does it guarantee data accuracy, but it also fosters efficient storage and querying.

# %% [markdown]
# 

# %% [markdown]
# 


