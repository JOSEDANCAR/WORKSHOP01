# Workshop 01 By Jose Carrera
# Candidate Data Analysis Project

## Overview
This project involves the analysis of candidate data stored in a MySQL database. It includes scripts for connecting to the database, retrieving data, performing basic analysis, and creating new tables with derived information. Additionally, an Exploratory Data Analysis (EDA) is conducted to uncover patterns and relationships within the data.
### Tools used

- ![Python](https://img.shields.io/badge/python-%2314354C.svg?style=for-the-badge&logo=python&logoColor=white) Python
- ![Jupyter](https://img.shields.io/badge/jupyter-%23F37626.svg?style=for-the-badge&logo=jupyter&logoColor=white) Jupyter Notebooks
- ![MySQL Workbench](https://img.shields.io/badge/MySQL%20Workbench-%2300f.svg?style=for-the-badge&logo=mysql&logoColor=white) MYSQL WorkBench
- ![Power BI](https://img.shields.io/badge/powerbi-F2C811.svg?style=for-the-badge&logo=powerbi&logoColor=black) Power BI
- ![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-F37626.svg?style=for-the-badge&logo=SQLAlchemy&logoColor=white) SQLAlchemy
## Prerequisites
1. Install Python: [Python Downloads](https://www.python.org/downloads/)
2. Install MySqlWorkbench: [MysqlWorkbench Downloads](https://www.mysql.com/products/workbench/)
3. Install Power BI: [Install Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)

## Required Python Libraries
- pandas
- mysql-connector-python
- SQLAlchemy
- matplotlib
- seaborn

You can install these libraries using pip:

pip install pandas mysql-connector-python SQLAlchemy matplotlib seaborn

## Setup Instructions
1. *Clone the project:*

    bash
    git clone https://github.com/Davidmelo9133/WorkShop1_ETL.git
    

2. *Go to the project directory:*

    bash
    cd WorkShop1_ETL
    

3. *Create a virtual environment for Python:*

    bash
    python -m venv venv
    

4. *Activate the virtual environment:*

   - On Windows:
     bash
     venv\Scripts\activate
     
   - On macOS/Linux:
     bash
     source venv/bin/activate
     

5. *Install the required libraries:*

    bash
    pip install -r requirements.txt
    

6. *Set Up the Database:*

    - Open MySQL Workbench or use the command line to connect to your MySQL server.
    - Create the database by running the following SQL command:
      sql
      CREATE DATABASE candidates;
      

7. *Load the Data:*

    - Import your candidate data into the candidates table in the 'candidates' database. You can use a CSV file and load it using MySQL Workbench or through a script if the data is available in a file format.

8. *Explore the Project:*

    - Start with the folder *db_operations*:
      - *database_setup*: This notebook focuses on database connection, table creation, and data insertion.
      - *transform_data*: Provides methods for processing and transforming data from a CSV file.

9. *Continue with the folder **notebooks:*

    - *eda*: In this notebook, we will dive into a comprehensive exploration of the dataset.

10. *Visualize Data in Power BI:*

    - Open Power BI and select the *MySqlWorkbench* option:
    
      ![MySqlWorkbench Option](https://i.imgur.com/JLpSDZv.png)

    - Search and select *MySqlWorkbench* as the data source:
    
      ![Search MySqlWorkbench](https://i.imgur.com/XGKEafL.png)

    - Enter your MySqlWorkbench server credentials:
    
      ![Enter Credentials](https://i.imgur.com/rZNqElp.png)

    - Select the *candidatos* table (since *candidatos_original* is a raw dataset):
    
      ![Select Table](https://i.imgur.com/Dx0biEM.png)
## Database Configuration
The project uses a MySQL database named 'candidates'. Make sure you have this database set up before running the scripts.

Database connection details:
- Host: localhost
- User: root
- Password: root
- Database: candidates

## Project Structure
The project consists of several steps:

1. Connecting to the MySQL database.
2. Retrieving data from the 'candidates' table.
3. Basic data exploration and manipulation.
4. Creating a new column 'contratado' based on specific conditions.
5. Creating a new table 'candidates_2' with the updated data.
6. *Exploratory Data Analysis (EDA)* on the candidates data.

## Usage
1. Ensure your MySQL server is running and the 'candidates' database is set up.
2. Run the Jupyter notebook or Python script to execute the data analysis steps.

## Key Functions

### Database Connection
python
import mysql.connector

config = {
    'user': 'root',
    'password': 'root',
    'host': 'localhost',
    'database': 'candidates'
}

connection = mysql.connector.connect(**config)


### Data Retrieval
python
query = "SELECT * FROM candidates"
cursor.execute(query)
df = pd.DataFrame(cursor.fetchall(), columns=[desc[0] for desc in cursor.description])


### Adding 'contratado' Column
python
df['contratado'] = (df['Code_Challenge_Score'] >= 7) & (df['Technical_Interview_Score'] >= 7)
df['contratado'] = df['contratado'].map({True: 'Sí', False: 'No'})


### Creating New Table
python
from sqlalchemy import create_engine

engine = create_engine(f'mysql+mysqlconnector://{config["user"]}:{config["password"]}@{config["host"]}/{config["database"]}')
df.to_sql(name='candidates_2', con=engine, if_exists='append', index=False)


## Exploratory Data Analysis (EDA)
The EDA is performed on the candidates and candidates_2 tables to explore data distribution, identify key patterns, and visualize important relationships. The analysis includes:

1. *Loading Data*: Data is loaded from the candidates table into a Pandas DataFrame.
2. *Initial Data Inspection*: Displaying the first few rows and information about the DataFrame.
3. *Missing and Duplicate Data Check*: Verifying if there are any null or duplicated values.
4. *Outlier Detection*: Identifying outliers in Code_Challenge_Score and Technical_Interview_Score columns.
5. *Data Visualization*: Creating plots to visualize the distribution and relationships between different variables.

### Example Code for EDA
python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sqlalchemy import create_engine

# Create a connection to the MySQL database
engine = create_engine('mysql+pymysql://root:root@localhost:3306/candidates')

# Load data from the 'candidates' table into a DataFrame
query = "SELECT * FROM candidates"
df = pd.read_sql(query, engine)

# Display the first few rows and data info
df.head()
df.info()

# Check for missing values
missing_values = df.isnull().sum()
print(missing_values)

# Check for duplicate values
duplicate_values = df.duplicated().sum()
print(duplicate_values)

# Detect outliers in the scores
score_columns = ['Code_Challenge_Score', 'Technical_Interview_Score']
for column in score_columns:
    sns.boxplot(x=df[column])
    plt.title(f'Outlier detection in {column}')
    plt.show()


## Notes
- The original dataset is loaded from a CSV file into the 'candidates' table. This step is not included in the provided code and should be done separately.
- The script creates a new table 'candidates_2' with an additional 'contratado' column. Make sure you have the necessary permissions to create new tables in the database.
- Always ensure proper error handling and connection closing in production environments.

## Caution
- The script that inserts data into the 'candidates_2' table is marked with "* No Tocar ni Ejecutar Mas *" (Do Not Touch or Execute Again), indicating it should only be run once to avoid duplicate data.

For any issues or questions, please contact the project maintainer.