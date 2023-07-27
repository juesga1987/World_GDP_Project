# World_GDP_Project
An individual project using Python Pandas and PowerBI to analyze the GDP behavior since 1961 per country and continent.

To develop the analysis we took a table from kaggle.com that has the GDP per country since 1.961 to 2.021. All the ETL was made with Pandas library and then the dataframe was exported to .CSV file we use to generate the dashboard in PowerBI.

# ETL process code

import numpy as np

import pandas as pd

1. Import dataset from the storage location.

```python
gdp = pd.read_csv(r'C:\Users\juesg\Desktop\DATA SCIENCE\Repasos Bootcampo\GDP\gdppercapita_us_inflation_adjusted.csv', sep = ',')
```

3. Take a look to the dataframe to understand the data types, number of null values, shape. Overall have a first impression or EDA. 

gdp.info()

gdp.Shape()

3. First of all im gonna change the type into string to make replacements easy and fast, in order to do that i created a function that accelerates the process.

def tipodato(dataframe, inicio, fin, tipo):

    for i in range(inicio, fin +1):
    
        dataframe[str(i)] = dataframe[str(i)].astype(tipo)
        
    return True

tipodato(gdp, 1960, 2021, str)

4. As you can see in the initial data, some values includes 'k' char that represents thousands. To transform those cells we will replace the 'k' to '' and then change the datatype to float and finally multiply by 1.000. In order to do accomplish the task I have created a function to automatize the process across the columns we indicate.

def extraer_caracter(caracter, inicio, final):

    for i in range(inicio, final+1):
    
        for ind, elemento in enumerate(gdp[str(i)]):
        
            if str(caracter) in gdp[str(i)][ind]:
            
                gdp[str(i)][ind] = gdp[str(i)][ind].replace('k', '')
                
                gdp[str(i)][ind] = (float(gdp[str(i)][ind])* 1000)
                
            else:
            
                gdp[str(i)][ind] = gdp[str(i)][ind]


extraer_caracter('k', 1960, 2021)

5. Now with an exception on 'NaN' values i need to convert all the the data to float type.

for year in gdp.columns[1:]:

    for ind, elemento in enumerate(gdp[str(year)]):
    
        if elemento == 'NaN':
        
            gdp[str(year)][ind] = (gdp[str(year)][ind])
            
        else:
        
            gdp[str(year)][ind] = float(gdp[str(year)][ind])

6. To allow the user of the data to realize deeper analysis I will assign the to each country the continent where each one of the countries belongs. To make that we first create lists of each Continent and then using the country column we check and add the Continent in Continent column recently added. Due to the encoding format Cote d'Ivoire needs to be trated separated.

africa = ['Nigeria','Ethiopia','Egypt','Congo, Dem. Rep.','Cape Verde','Congo, Rep.','Tanzania','South Africa','Kenya','Uganda','Sudan','Algeria','Morocco','Angola','Ghana','Mozambique','Madagascar','Cote dIvoire','Cameroon','Niger','Mali','Burkina Faso','	Malawi','Zambia','Chad','Somalia','Senegal','Zimbabwe','Guinea','Rwanda','Benin','Burundi','Tunisia','South Sudan','Togo','Sierra Leone','Libya','Congo','Central African Republic','Liberia','Mauritania','Eritrea','Gambia','Botswana','Namibia','Gabon','Lesotho','Guinea-Bissau','Equatorial Guinea','Mauritius','Eswatini','Djibouti','Comoros','Cabo Verde','Sao Tome Principe','Seychelles']

gdp['continent'] = ''

for ind, country in enumerate(gdp['country']):

    for elemento in africa:

        if country == elemento:
        
            gdp['continent'][ind] = 'africa'


europa = ['Falkland Islands','Isle of Man','Faeroe Islands','Russia','Slovak Republic','Cyprus','Czech Republic','Germany','United Kingdom','France','Italy','Spain','Poland','Ukraine','Romania','Netherlands','Belgium','Sweden','Czech Republic (Czechia)','Greece','Portugal','Hungary','Belarus','Austria','Switzerland','Serbia','Bulgaria','Denmark','Slovakia','Finland','Norway','Ireland','Croatia','Moldova','Bosnia and Herzegovina','Albania','Lithuania','Slovenia','North Macedonia','Latvia','Estonia','Luxembourg','Montenegro','Malta','Iceland','Andorra','Liechtenstein','Monaco','San Marino','Holy See']

for ind, country in enumerate(gdp['country']):

    for elemento in europa:
    
        if country == elemento:
        
            gdp['continent'][ind] = 'europe'

samerica = ['Brazil','Colombia','Argentina','Peru','Venezuela','Chile','Ecuador','Guatemala','Bolivia','Haiti','Cuba','Dominican Republic','Honduras','Paraguay','El Salvador','Nicaragua','Costa Rica','Panama','Uruguay','Jamaica','Trinidad and Tobago','Guyana','Suriname','Belize','Guadeloupe','Bahamas','Martinique'
           ,'Barbados','French Guiana','Saint Lucia','Curacao','Grenada','Aruba','Saint Vincent and the Grenadines','Antigua and Barbuda','Dominica','Cayman Islands','Bermuda','Greenland','Saint Kitts and Nevis','Turks and Caicos Islands','Sint Maarten','Saint Martin','British Virgin Islands','Caribbean Netherlands','Anguilla','Saint Barthélemy',
           'Saint Pierre and Miquelon','Montserrat','St. Vincent and the Grenadines','Sint Maarten (Dutch part)','Sao Tome and Principe','St. Kitts and Nevis','St. Lucia','Curaçao']

for ind, country in enumerate(gdp['country']):

    for elemento in samerica:
    
        if country == elemento:
        
            gdp['continent'][ind] = 'South_central_america'

namerica = ['Canada', 'United States', 'Mexico','United States Virgin Islands','Virgin Islands (U.S.)','Puerto Rico']

for ind, country in enumerate(gdp['country']):

    for elemento in namerica:
    
        if country == elemento:
        
            gdp['continent'][ind] = 'North_america'

oceania = ['Micronesia, Fed. Sts.','Australia','Papua New Guinea','New Zealand','Fiji','Solomon Islands','Micronesia','Vanuatu','American Samoa','Kiribati','Tonga','Marshall Islands','Palau','Nauru','Tuvalu','Samoa','French Polynesia']

for ind, country in enumerate(gdp['country']):

    for elemento in oceania:
    
        if country == elemento:
        
            gdp['continent'][ind] = 'oceania'


gdp['continent'][36] = 'africa'

for ind, country in enumerate(gdp['country']):

    if gdp['continent'][ind] == '':
    
            gdp['continent'][ind] = 'asia'


7. Replacement of 'nan' with 'aaa'. That will facilitate us the calculation of the mean per continent and year to fill each one of the countrys with 'NAN' data.


gdp.replace(np.nan,'aaa',regex=True, inplace=True)


8. Lets calculate the mean per continent per year. I created a function to calculate the mean another to insert the calculated mean and lastly with loops we use both functions to travel around each columns.

def calculo_media(anio, cont):

    conteo = 0
    
    gd = 0
    
    for ind, continenta in enumerate(gdp['continent']):
    
        if continenta == str(cont) and gdp[str(anio)][ind] != 'aaa' :
        
         conteo += 1

    for ind, continenta in enumerate(gdp['continent']):
    
        if continenta == str(cont) and gdp[str(anio)][ind] != 'aaa':
        
            gd += gdp[str(anio)][ind]
    
    media = gd / conteo

    return media

calculo_media(1960, 'South_central_america')

def insertar(anio, conti):

   for ind, elemento in enumerate(gdp[str(anio)]):
   
      if elemento == 'aaa' and gdp['continent'][ind] == str(conti):
      
         gdp[str(anio)][ind] = calculo_media(anio, conti)

for i in range(1960, 2021+1):
    insertar(i, 'africa')

for i in range(1960, 2021+1):
    insertar(i, 'europe')

for i in range(1960, 2021+1):
    insertar(i, 'South_central_america')

for i in range(1960, 2021+1):
    insertar(i, 'North_america')

for i in range(1960, 2021+1):
    insertar(i, 'oceania')

for i in range(1960, 2021+1):
    insertar(i, 'asia')

9. Change all the data types into float, using the function we created.

tipodato(gdp, 1960,2021,float).

10. Round all the numbers into 2 decimals.

for columna in gdp.columns[1:63]:
    for ind, elemento in enumerate(gdp[str(columna)]):
        gdp[str(columna)][ind] = round(gdp[str(columna)][ind],2)

11. In order to have a 'KPI' we use CAGR to calculate the average annual return per country between 1960 and 2021 and add the calculation into a new column named 'cagr'.

lista = []

for ind, elemento in enumerate(gdp['2021']):
    a = round((((elemento / gdp['1960'][ind])**(1/(2021-1960+1)))-1), 4)
    lista.append(a)

print(lista)

gdp['cagr'] = lista


12. Dataframe is ready to being exported to a .CSV file and then using PowerBI creates a dashboard to visualize and make analysis.

gdp.to_csv('gdp.csv', sep=',')

