
<a id="top"></a> <br>
# <div style="text-align: center"> Data mining Project in Python.</div>

Welcome to my data mining project in python.

## Contents.

1. [About this project](#1)
1. [Business Value](#2)
1. [Importing libraries(tools of trade)](#3)
1. [Data Ingestion](#4)
1. [Data Exploration](#5)
    1. [Variable and observations - Shape](#51)
    1. [Sample look at the data](#52)
    1. [Missing Values](#53)
1. [Data Preprocessing](#6)
    1. [Registration data - Patient codes](#61)
    1. [Registration data-Missing Dates of Birth](#62)
    1. [Patient Paid Visit Data-Patient codes](#63)
    1. [Patient Paid Visit Data-Visit Date.](#64)
    1. [Paid Visit Data-Dropping unwanted variables](#65)
1. [Merging data](#7)
    1. [Merged data-age calculation](#71)
    1. [Merged data - filltering out rows with nul vaues in Diagnosis column.](#72)
    1. [Merged Data - filtering out typhoid and age](73)
1. [The pivot table](#8) 
    1. [Visualizing the results](#81)
    1. [Answering the question.](#82)
    1. [Conclusion](#83)
    
 <a id="1"></a> <br> 
 
## About this project.

This project was innitialy posted by an organization as an initial test on the applicants abilities in data analysis and extraction.There was no specified programm to handle the project so i have decided to solve the problem here with python.

 <a id="2"></a> <br>
 
## Busines Value.

![title](typhoid.jpg)

Lets start with the business value of this project and see what will be end product.

This organization collects comprehensive medical data on their patients, and is sometimes required to report certain aggregate data to various stakeholders. One such stakeholder is the Ministry of Health (MoH).

The MoH is dispensing free medication to combat typhoid in children. The MoH wishes to supply only those medical centres that have a higher incidence of typhoid among their patients. Specifically, the MoH is willing to provide free typhoid medication to clinics that diagnose an average of 15 cases of typhoid per month in children under 5 years of age.

The “MoH reporting” file contains data on patient visits to this organization's Medical Centres from **January to September 2018**. The applicant is required to callculatethe number of typhoid cases per month for each medical centre, as well as the average number of cases for the full time period covered. 

Data is supplied in two worksheets namely (“PatientPaidVisit” and “RegistrationData”) to populate a table with the required information.

Based on the applicat analysis, he or she should determine whether each medical centre will be eligible for the free medicine from the MoH. 

The project looks simple from the explanation but there is a catch way in between:-data is messy!.

To tackle the project some packages will be handy in python.

 <a id="3"></a> <br>
## Importing libraries(tools of trade).

[go to top](#top)


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from datetime import datetime
import seaborn as sns
%matplotlib inline
```

The code above imports all the modules i will need in this project to manipulate the data.
 <a id="4"></a> <br>
## Data Ingestion

The data is provided in and an excel format.I will use pandas for this import into pythons working session.It is important that the data is given using a link to google docs available here.
https://docs.google.com/spreadsheets/d/1fJEV5ISyD1UisqmRSmR71wIXzkUmX_Mwco9tTn_AS_I/edit?usp=sharing.

In this project I downoaded the data in prior into my hard disk so i have not icluded data download codes here.


```python
PatientPaidVisit = pd.read_excel('/Users/admin/Downloads/BI.xlsx',sheet_name='PatientPaidVisit')
RegistrationData = pd.read_excel('/Users/admin/Downloads/BI.xlsx',sheet_name='RegistrationData')
```

The code above loads the worksheets containing PatientPaidVisit and Registration data respectively into the woking environment.
 <a id="5"></a> <br>
## Data Exploration.

After ingesting this data its important to take some time and inspect the data structure.
 <a id="51"></a> <br>
### Variables and observations(Shape)

[go to top](#top)


```python
PatientPaidVisit.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 59091 entries, 0 to 59090
    Data columns (total 12 columns):
    Visit date         59091 non-null object
    Payment Type       59091 non-null object
    Patient Code       59091 non-null object
    Patient Name       0 non-null float64
    Visit Code         59091 non-null object
    Gender             59091 non-null object
    Patient Address    0 non-null float64
    Diagnosis          41742 non-null object
    Phone no.          0 non-null float64
    New/Repeat         59091 non-null object
    Total invoice      59091 non-null float64
    Visit location     59091 non-null object
    dtypes: float64(4), object(8)
    memory usage: 5.4+ MB



```python
RegistrationData.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 84364 entries, 0 to 84363
    Data columns (total 2 columns):
    Patient Code     84364 non-null object
    Date of Birth    79445 non-null datetime64[ns]
    dtypes: datetime64[ns](1), object(1)
    memory usage: 1.3+ MB


We can see that the paid visit has 59091 observations and 12 variables while the registration data has 84364 observations and 2 variavles.

Also ,its imporrtant to note that the visit date in the patient paid visit sheet is not stored as a datetime object.This is where we will start working.
 <a id="52"></a> <br>
## Sample look at the data.


[go to top](#top)


```python
np.random.seed(3)
PatientPaidVisit.sample(5)
```
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Visit date</th>
      <th>Payment Type</th>
      <th>Patient Code</th>
      <th>Patient Name</th>
      <th>Visit Code</th>
      <th>Gender</th>
      <th>Patient Address</th>
      <th>Diagnosis</th>
      <th>Phone no.</th>
      <th>New/Repeat</th>
      <th>Total invoice</th>
      <th>Visit location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>38761</th>
      <td>16/07/2018</td>
      <td>Cash</td>
      <td>PAT807412018</td>
      <td>NaN</td>
      <td>V-103197</td>
      <td>Female</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>New</td>
      <td>650.0</td>
      <td>Embakasi</td>
    </tr>
    <tr>
      <th>39335</th>
      <td>18/07/2018</td>
      <td>Cash</td>
      <td>PAT8106620181</td>
      <td>NaN</td>
      <td>V-104160</td>
      <td>Male</td>
      <td>NaN</td>
      <td>Sorethroat</td>
      <td>NaN</td>
      <td>New</td>
      <td>550.0</td>
      <td>Kahawa West</td>
    </tr>
    <tr>
      <th>21821</th>
      <td>2018-10-02 00:00:00</td>
      <td>Cash</td>
      <td>PAT160422017</td>
      <td>NaN</td>
      <td>V-53388</td>
      <td>Male</td>
      <td>NaN</td>
      <td>skin disease</td>
      <td>NaN</td>
      <td>Repeat</td>
      <td>470.0</td>
      <td>Githurai 45</td>
    </tr>
    <tr>
      <th>49235</th>
      <td>15/06/2018</td>
      <td>Cash</td>
      <td>PAT41692</td>
      <td>NaN</td>
      <td>V-92030</td>
      <td>Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Repeat</td>
      <td>490.0</td>
      <td>Embakasi</td>
    </tr>
    <tr>
      <th>23819</th>
      <td>18/02/2018</td>
      <td>APA</td>
      <td>PAT643972018</td>
      <td>NaN</td>
      <td>V-55735</td>
      <td>Male</td>
      <td>NaN</td>
      <td>Septic tonsilitis</td>
      <td>NaN</td>
      <td>New</td>
      <td>2580.0</td>
      <td>Umoja</td>
    </tr>
  </tbody>
</table>



Just from the look of things we can spot a problem in the first column ,some dates are recorded as string literals.There are also missing values which some are understandable for anonymity and confidentiality purposes, common with medical data.

I have set a random seed to make this step reproducible.


```python
np.random.seed(7)
RegistrationData.sample(10)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Patient Code</th>
      <th>Date of Birth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>69699</th>
      <td>PAT542722017</td>
      <td>2014-12-31</td>
    </tr>
    <tr>
      <th>34009</th>
      <td>PAT514262017</td>
      <td>2006-12-31</td>
    </tr>
    <tr>
      <th>33027</th>
      <td>PAT623872018</td>
      <td>1994-12-31</td>
    </tr>
    <tr>
      <th>69141</th>
      <td>PAT724102018</td>
      <td>1984-12-30</td>
    </tr>
    <tr>
      <th>93</th>
      <td>PAT690642018</td>
      <td>1993-12-30</td>
    </tr>
    <tr>
      <th>54695</th>
      <td>PAT656182018</td>
      <td>2002-12-31</td>
    </tr>
    <tr>
      <th>75215</th>
      <td>PAT901532018</td>
      <td>2009-12-30</td>
    </tr>
    <tr>
      <th>37743</th>
      <td>PAT103122017</td>
      <td>1990-12-31</td>
    </tr>
    <tr>
      <th>37077</th>
      <td>PAT801562018</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>23362</th>
      <td>PAT156632017</td>
      <td>2013-12-31</td>
    </tr>
  </tbody>
</table>



We can see there exists some missing data in the date of birth column.For this data sheet,it will be resonable to drop rows with no birth dates since there is no other way we can re construct the dates if they are missing.

Bellow i am visualizing the missing values.
 <a id="53"></a> <br>
## Missing Values

[go to top](#top)


```python
sns.heatmap(pd.isnull(RegistrationData),yticklabels=False,cbar=False)
plt.title("Missing Values")
```




    Text(0.5, 1.0, 'Missing Values')




![png](output_12_1.png)


The plot above shows evidently that there exists quite a number of missing values.Lets see how many they are!


```python
print(RegistrationData.isnull().sum(axis=0))
print("=========="*3)
prop= round(RegistrationData.isnull().sum(axis=0)[1]/len(RegistrationData['Patient Code'])*100,2)
print("{}% of dates of birth are missing".format(prop))
```

    Patient Code        0
    Date of Birth    4919
    dtype: int64
    ==============================
    5.83% of dates of birth are missing


The code above shows that there are 4919 missing dates of birth which make around 6% of all the data.Also we saw some patient codes repeated so only unique values will be used for the analysis.
 <a id="6"></a> <br>
## Data Preprocessing.

In the above section we looked at what the data is made up of.In order to make correct analysis on data the data should obey the principles of a tidy data:
 <a id="61"></a> <br>
 
#### Properties of tidy data:

1. Every column is a variable.

1. Every row is an observation.

1. Every cell is a data point.
 <a id="62"></a> <br>
#### Registration data - Patient codes

Since patient codes column is a very crucial variable in this data set I will spend some time inspecting at it.

Some of the things I will do to  this column include:

1. Strip all the blank spaces in the patient code values.

1. Eliminate the last four digits of the patient code since probably they stand for the year.Also ,some of the codes lack the last four digits for the year.(To illustrate this i will first inspect the longest patient code and the shortest patient code.)

[go to top](#top)


```python
stripedcodes = []
for code in RegistrationData['Patient Code']:
    strcode = len(code.strip())
    stripedcodes.append(strcode)
shortestcode = min(stripedcodes)
longestcode = max(stripedcodes)
print("We can clearly see that the shortest patient code has {} characters and the longest code has {} characters".format(shortestcode,longestcode))
```

    We can clearly see that the shortest patient code has 8 characters and the longest code has 12 characters


Everything is self explanatory from the above output and based on that i will derive the new corrected patient codes.Below i create a function to strip the white spaces in the patient codes codes and extract the first 8 characters then return the corrected patient codes. 


```python
def correctCode(cols):
    return cols.strip()[:8]#
```


```python
RegistrationData['Patient Code'] = RegistrationData['Patient Code'].apply(correctCode)
```

<a id="63"></a> <br>
#### Registration data-Missing Dates of Birth.

Now its time to eliminate the rows with missing dates of birth because its obvious that the missing dates of births cannot be reconstructed unless for patients entered more than once,then drop duplicates.


```python
RegistrationData=RegistrationData.dropna().drop_duplicates()
```

And now lets look check if there are still missing values.


[go to top](#top)


```python
sns.heatmap(pd.isnull(RegistrationData),yticklabels=False,cbar=False)
plt.title("Missing Values")
```




    Text(0.5, 1.0, 'Missing Values')




![png](output_23_1.png)


Good!There are no missing values ,we are good to go.

<a id="64"></a> <br>
#### Patient Paid Visit Data-Patient codes

The patient codes will be treated the same way as the registration data.


```python
PatientPaidVisit['Patient Code'] = PatientPaidVisit['Patient Code'].apply(correctCode)
```

<a id="65"></a> <br>
#### Patient Paid Visit Data-Visit Date.

We saw earlier that patient paid visit data date column had irregularites.The following actions will be applied to this column.

1. Convert the string dates to datetime datatypes.

1. Correct formart the wrongly enterd dates.

The function below will be used to convert the dates into datetime object.

[go to top](#top)


```python
def dateCorrector(cols):
    if type(cols) == datetime:
        return cols
    else:
        return  datetime.strptime(cols,'%d/%m/%Y')
```


```python
PatientPaidVisit['Visit date'] = PatientPaidVisit['Visit date'].apply(dateCorrector)
```

Now lets look at this column in detail,you will realize that the months are past **September** which was categorically stated in the business value section by the job poster.This means that at some point the data clerks were starting with the months and then the dates.Infact in some instances the months are ambiguous.


```python
PatientPaidVisit['Visit date'].dt.month.unique()
```




    array([ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12])



The output above confirms the idea that the months are way past september,lets correct this.

I am going to create a cunction below that will be used to correct this mistake incase month is greater than september.


```python
def dateFormart(cols):
        if cols.month >9:
            return datetime.strptime(datetime.strftime(cols,'%Y-%m-%d'), '%Y-%d-%m' ) #checks if month is greater than 9(september)
        else:
            return cols
        
PatientPaidVisit['Visit date'] = PatientPaidVisit['Visit date'].apply(dateFormart)#new correct dates
PatientPaidVisit['Visit date'].dt.month.unique()
```




    array([1, 2, 3, 4, 5, 6, 7, 8, 9])



The code above simply checks if month is more than september and land swaps the day and month position.The output shows the result.At this point its important to also create a column for month that i will use in the final pivot.


[go to top](#top)


```python
def month_var(cols):
    return cols.strftime("%B")
PatientPaidVisit['Visit month'] = PatientPaidVisit['Visit date'].apply(month_var)
PatientPaidVisit['Visit month'].unique() #just to show the result of the function.
```




    array(['January', 'February', 'March', 'April', 'May', 'June', 'July',
           'August', 'September'], dtype=object)



#### Paid Visit Data-Dropping unwanted variables

Its evident that some variables will not be used in creating the final pivot table so i will drop them.


```python
PatientPaidVisit.drop(['Payment Type','Patient Name','Phone no.','Total invoice','Patient Address'],axis = 1,inplace=True)
```

<a id="7"></a> <br>
## Merging Data.

In order to stay on course with our hypothetical situation,i will merge the data based on the patient code column.


```python
mergeData = pd.merge(PatientPaidVisit,RegistrationData,on = "Patient Code",how = "inner")
```

<a id="71"></a> <br>
### Merged data age calculation

The project needed infants five years and below in diagnosed with typhoid so i will create an age column with the code below.


[go to top](#top)


```python
mergeData['age'] = round((mergeData['Visit date']-mergeData['Date of Birth'])/np.timedelta64(1,'Y'))
```

<a id="72"></a> <br>
### Merged data - filltering out rows with nul vaues in Diagnosis column.

Its important to note that the project is intrested in diagnosis so the records with no diagnosis are of ess important to us so they will be filtered out.


```python
mdfilt = mergeData[mergeData['Diagnosis'].notnull()]
```

<a id="73"></a> <br>
### Merged Data - filtering out typhoid and age.

To get to the core of the requirements ,I will filter the data v=based on the age and diagnosis column.

It should ne noted that some diagnostics were writen wrongly eg "tyfoid","typhiod" instead of "typhoid".I will therefore start by replacing the wrongly spelt typhoid names.
[go to top](#top)


```python
wrongspell = ['typhiod','tyfoid']
for word in wrongspell:
    mdfilt['Diagnosis'] = mdfilt.Diagnosis.str.replace(word,'typhoid',regex = False)
```

    /Users/admin/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      This is separate from the ipykernel package so we can avoid doing imports until



```python
cond1 = mdfilt['Diagnosis'].str.contains('typhoid',na =False)
cond2 = mdfilt['age']<=5
finaldata = mdfilt[cond1 & cond2]
```

Final quick view of the last 5 values of the data.

<a id="8"></a> <br>
## THE PIVOT TABLE

Finally at the end of the project I will create a pivot table to populate the values.


```python
pivot_table = finaldata.pivot_table(values='Diagnosis',index="Visit location",columns="Visit month",margins_name='Average',aggfunc='count',margins=np.average,)
pivot_table['Average'] = round(pivot_table['Average']/9)
pivot_table.fillna(0,inplace = True)
pivot_table
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Visit month</th>
      <th>April</th>
      <th>August</th>
      <th>February</th>
      <th>January</th>
      <th>July</th>
      <th>June</th>
      <th>March</th>
      <th>May</th>
      <th>September</th>
      <th>Average</th>
    </tr>
    <tr>
      <th>Visit location</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Embakasi</th>
      <td>43.0</td>
      <td>33.0</td>
      <td>32.0</td>
      <td>31.0</td>
      <td>43.0</td>
      <td>23.0</td>
      <td>23.0</td>
      <td>11.0</td>
      <td>4.0</td>
      <td>27.0</td>
    </tr>
    <tr>
      <th>Githurai 45</th>
      <td>19.0</td>
      <td>25.0</td>
      <td>28.0</td>
      <td>20.0</td>
      <td>32.0</td>
      <td>28.0</td>
      <td>13.0</td>
      <td>10.0</td>
      <td>3.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>Huruma</th>
      <td>9.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>2.0</td>
      <td>8.0</td>
      <td>8.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>Kahawa West</th>
      <td>22.0</td>
      <td>31.0</td>
      <td>32.0</td>
      <td>35.0</td>
      <td>46.0</td>
      <td>35.0</td>
      <td>26.0</td>
      <td>9.0</td>
      <td>6.0</td>
      <td>27.0</td>
    </tr>
    <tr>
      <th>Kayole</th>
      <td>1.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>Pipeline</th>
      <td>0.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Umoja</th>
      <td>40.0</td>
      <td>41.0</td>
      <td>28.0</td>
      <td>43.0</td>
      <td>52.0</td>
      <td>40.0</td>
      <td>37.0</td>
      <td>11.0</td>
      <td>11.0</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>Zimmerman</th>
      <td>17.0</td>
      <td>21.0</td>
      <td>21.0</td>
      <td>20.0</td>
      <td>36.0</td>
      <td>33.0</td>
      <td>17.0</td>
      <td>8.0</td>
      <td>7.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>Average</th>
      <td>151.0</td>
      <td>162.0</td>
      <td>144.0</td>
      <td>152.0</td>
      <td>220.0</td>
      <td>169.0</td>
      <td>118.0</td>
      <td>52.0</td>
      <td>32.0</td>
      <td>133.0</td>
    </tr>
  </tbody>
</table>



<a id="81"></a> <br>
#### **Which can visualized below this way**


[go to top](#top)


```python
plt.figure(figsize=(12,6))
sns.heatmap(pivot_table.iloc[:-1],annot=True,cmap='gist_stern',lw = 3,cbar = False)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a218027f0>




![png](output_51_1.png)


<a id="82"></a> <br>
## Answering the question.

Since i am required to indicate if a hospital is elligible for the covermont support,i will create a python function to assist me tackle this


```python
def eligibility(cols):
    if cols >15:
        return 'eligible'
    else:
        return 'not  eligible'

```


```python
pivot_table['Eligibility']=pivot_table['Average'].apply(eligibility)
```


```python
pivot_table.iloc[:-1][['January', 'February', 'March', 'April', 'May', 'June', 'July','August', 'September','Average','Eligibility']]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Visit month</th>
      <th>January</th>
      <th>February</th>
      <th>March</th>
      <th>April</th>
      <th>May</th>
      <th>June</th>
      <th>July</th>
      <th>August</th>
      <th>September</th>
      <th>Average</th>
      <th>Eligibility</th>
    </tr>
    <tr>
      <th>Visit location</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Embakasi</th>
      <td>31.0</td>
      <td>32.0</td>
      <td>23.0</td>
      <td>43.0</td>
      <td>11.0</td>
      <td>23.0</td>
      <td>43.0</td>
      <td>33.0</td>
      <td>4.0</td>
      <td>27.0</td>
      <td>eligible</td>
    </tr>
    <tr>
      <th>Githurai 45</th>
      <td>20.0</td>
      <td>28.0</td>
      <td>13.0</td>
      <td>19.0</td>
      <td>10.0</td>
      <td>28.0</td>
      <td>32.0</td>
      <td>25.0</td>
      <td>3.0</td>
      <td>20.0</td>
      <td>eligible</td>
    </tr>
    <tr>
      <th>Huruma</th>
      <td>2.0</td>
      <td>3.0</td>
      <td>2.0</td>
      <td>9.0</td>
      <td>3.0</td>
      <td>8.0</td>
      <td>8.0</td>
      <td>5.0</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>not  eligible</td>
    </tr>
    <tr>
      <th>Kahawa West</th>
      <td>35.0</td>
      <td>32.0</td>
      <td>26.0</td>
      <td>22.0</td>
      <td>9.0</td>
      <td>35.0</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>6.0</td>
      <td>27.0</td>
      <td>eligible</td>
    </tr>
    <tr>
      <th>Kayole</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>not  eligible</td>
    </tr>
    <tr>
      <th>Pipeline</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>not  eligible</td>
    </tr>
    <tr>
      <th>Umoja</th>
      <td>43.0</td>
      <td>28.0</td>
      <td>37.0</td>
      <td>40.0</td>
      <td>11.0</td>
      <td>40.0</td>
      <td>52.0</td>
      <td>41.0</td>
      <td>11.0</td>
      <td>34.0</td>
      <td>eligible</td>
    </tr>
    <tr>
      <th>Zimmerman</th>
      <td>20.0</td>
      <td>21.0</td>
      <td>17.0</td>
      <td>17.0</td>
      <td>8.0</td>
      <td>33.0</td>
      <td>36.0</td>
      <td>21.0</td>
      <td>7.0</td>
      <td>20.0</td>
      <td>eligible</td>
    </tr>
  </tbody>
</table>



[go to top](#top)
<a id="83"></a> <br>
## Conclusion.

From the final output above ,its clear that **Embakasi,Githurai 45,Kahawa west,Umoja and Zimmerman** health centers are eligible for the government program.



[<div style="text-align: center;font-size:40px;text-decoration:underline;">THE END</div>](#top)
