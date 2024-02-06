## Asignment0 - Data Engineering

## Author: Rajesh Uppala

## Description

The Norman, Oklahoma police department regularly reports incidents, arrests, and other activities. This data is distributed to the public in the form of PDF files. (https://www.normanok.gov/public-safety/police-department/crime-prevention-data/department-activity-reports)

- To download the data given one incident pdf using url
- To Extract the below fields:
  - Time
  - Incident Number
  - Location
  - Nature
  - Incident ORI
- create a SQLite database to store the data;
- Insert the data into the database;
- Print each nature and the number of times nature appears

## Setting up the Initial installations 
In the project's virtual environment, we execute the following installations. 
~~~
pipenv install pypdf
~~~
The rest of the packages are part of the standard library, so there's no need for separate installations; they only need to be imported into the script.


## Packages Required:

- requests
- sqlite3
- pypdf
- re
- io
\
The projects have below files: 
## 1. main.py 
 
assignment0.py is imported into this file.
This file will get the url as argument. 
Functions for extracting data, parsing data and printing the data is present in assignment0 module. These functions are called from this file.

## 2. project0.py

### **fetchincidents(url)**

This function downloads a PDF document from a given URL
Makes an HTTP GET request to the specified URL to retrieve the PDF, then reads the PDF into a PdfReader object for processing.

### **extractincidents(incident_data)**

Extracts incident information from all pages of the provided PDF document.
Iterates through each page of the PDF, extracting text and processing it through parsePage to compile a comprehensive list of incidents.

On the first page and last page of pdf, some unnecessary text is removed using following logic

![image](https://github.com/rajeshuppala1449/EPAM_git/assets/48644047/adb1b3fb-5b13-40e9-8431-ce467812d23b)

replace() method is used to removed unwanted information present in the first pdf page of the incident file. In order to read the second lines of columns " \n" is replaced
to just space so that I can perform operations on the edited data. Then using regex function and sub() function are used to put || operators
between each row. So now the data is split into list near the "\n||" operator. So this will create a list, where the each row is considered as list.
A new empty list is created and this list data is appended to it.

On the remaining pages of pdf below operations will be done

![SS2](https://user-images.githubusercontent.com/96924488/157276054-a94f977c-bb7c-45e8-9ee4-dbc9e98ed633.png)

So the same operations that are mentioned above are performed onto all pages. Since there will be no unwanted data in reamining pages the function to replace unwanted data is not used in this.

![SS3](https://user-images.githubusercontent.com/96924488/157276113-7551669e-4ef2-4f50-9ac9-4c663c59c762.png)

**handling the missing values in the data**
<br>
We need the data to be present as a list of 5, beacuse there are 5 columns in the pdf, so upon research from the pdf's given I could see that only 3rd column and 4th column i.e 
location and nature will only be the missing columns. A new 5 length list is created where when the list is of 5 length then the same is copied into the list and if the
the list length is 4, I assumed that nature is missing in the row, and left the 4th list element empty and filled the other list elements.
When the list length is 3, I assumed that both location and nature columns are missing so 3rd and 4th list elements are kept empty, and other list elements are filled.

Finally as part of this function data which consists of the list of data as its elements will be returned.


### **createdb()**

![SS4](https://user-images.githubusercontent.com/96924488/157276729-ce708bdb-ed09-4d30-8b8c-4d5a79b3e5fd.png)
<br>
Database is named as data.db. Then connection is made to the SQLite database using **sqlite3.connect ()** and then this connection will be called as cursor and statement are executed on it.
A new table is created namely "incident" as shown above, table will be dropped if it exists before creating the table, so that we don't get error telling that table is already present.
This table is commited and connection is closed. Database name will be returned as part of this function.

### **populatedb(db_name, incidents)**

The list from extractincidents() and  db name from createdb() is passed as input parameters to **populatedb()**,

![SS5](https://user-images.githubusercontent.com/96924488/157276824-c7560c23-7437-4f02-b796-2378f8cd3d42.png)
<br>

In this function a connection is made to the database that is passed. 
And list elements of the incident data file will be read into the varibables and these will be used to insert data into the table.

### **status(db)**

![SS6](https://user-images.githubusercontent.com/96924488/157276845-b57e90d3-f674-449e-9966-dbaf60c08e4a.png)
<br>
Database is taken as the input parameter. A connection is made to the database. And the **SELECT** statement is executed, where nature is selected and it is counted. 
And the list is sorted first by the total number of incidents and secondarily, alphabetically by the nature. Each field of the row is separated by the pipe character "|".

## Pytest framework for the project :
I used the Pytest framework in Python to check for the individual test cases. To run the pytest framework, we need to first ensure if we have the pytest installed in our current project directory. I used the following command to install the pytest in my project's execution virtual environment.
~~~
pipenv install pytest
~~~

## 3.test_project0.py

The test_project0.py file contains the unit testcases to test each method of project0.py if it is working are not, in this file we written the below 5 testcases to test each method.
Below are imported for this file
- pytest
- NoneType from types
- os
- io
- sqlite3
- project0
<br>
In tests folder I attached a pdf file called sample.pdf to test my functions.

![SS7](https://user-images.githubusercontent.com/96924488/157276897-8e529aa1-cbc9-4147-b1de-97d02a14ff88.png)

<br>
os. getcwd() returns the absolute path of the working directory where Python is currently running as a string str.
So from the command

<br>

return os.path.join(cwd, 'tests', 'sample.pdf')
<br>
it returns the path to sample.pdf file from tests directory in the current directory

### **test_fetchincidents():**
<br>

![SS8](https://user-images.githubusercontent.com/96924488/157276943-3ca0ab7e-c1e8-4634-a84f-381c2439b69a.png)

<br>
In this test method operations of fetchincidents method will be done on the "sample file" and then it is checked if the file is of type bytes from assert isinstance.
As the fetchincidents function should have bytes as data.

### **test_extractincidents():**

![SS9](https://user-images.githubusercontent.com/96924488/157276967-806dc69a-7924-4fdd-be29-13aab0d523a9.png)
<br>
In this test case we test the extractincidents, the sample will be opened and operations of extractincidents method will be performed on this file. The list data length of the sample file I used is "237" so after performing the method to the data, it is checked if the result is same as the len of the data that I used, to check the method. If it is same then the test case will be passed.
<br>

### **test_createdb():**

![SS10](https://user-images.githubusercontent.com/96924488/157277006-e4afe9a2-13c0-4efe-9a8a-bb43429ab2b9.png)
<br>
In this testcase we would test the **createdb()**, A database is created, and it's path will be taken from below command.
~~~
db_path = os.path.join(os.getcwd(), db_name)
~~~
And if a database is created a path to that database will exists so it will be checked, if it is true then database connection is made. When database connection is made there will be no total changes in the connection so it will be checked. Finally the path of the database is removed in the end of test.

### **test_populatedb( ):**

![SS11](https://user-images.githubusercontent.com/96924488/157277066-8a4f6117-6794-474d-b636-d9aa26a3ef1e.png)
<br>
In this method we test the **populated()** method, few incidents will be pushed into the database. populate_db method will return the count of total changes, so this count will be taken after inserting the data and this is compared with length of the incidents that I pushed into the table, if both are same then the test case is passed. Finally the path to the database will be removed.

## **test_status( ):**

In this method we test the **status()** method.
<br>
 ![SS12](https://user-images.githubusercontent.com/96924488/157277117-c3821d7e-5670-491d-8e92-c2cb24d61d62.png)
<br>
Like previous function, incidents data will be pushed into the database. Then populatedb method will be executed on it, it will give a result and that result is compared with result that we already expect, and if they both are same test case will be passed.

## To run the Pytest : 
I used the following command to run my python tests for the given function.
~~~
 pipenv run python -m pytest
~~~

## 4.Assumptions/Bugs:

- In this project we assumed that only location and nature columns have missing values in the pdf document and we handled only the missing values in those columns, if any other column has the missing values in the pdf the code will fail. 
- After splitting the data by date the maximum length of the list we observed is 7, if the length of list is > 7 then the code may fail in this case.
- I assumed that among location and nature, if my data gives only list of length 4 then nature column is missing, this assumption is made after looking into the files, but if location is missed instead of nature then this code will fail.


## Steps to Run project0

- **Step1** \
clone the project directory using below command 
> git clone  https://github.com/VarshithaCVasireddy/Crime_Incidents_Reporter

- **Step2** \
Navigate to directory that we cloned from git and run the below command to install dependencies

> pipenv install

- **Step3** \
Then run the below command by providing URL

> pipenv run python project0/main.py --incidents **URL**

- **Step4** 

Then run the below command to test the testcases. 

> pipenv run python -m pytest -v

