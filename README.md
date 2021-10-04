# 911 Calls Dataset
For this project we will be analyzing some 911 call data from Kaggle. We will be performing some data analysis as well as visualisation on the set of data.

The data contains the following fields: </br>
(lat : String variable, Latitude), (lng: String variable, Longitude), (desc: String variable, Description of the Emergency Call), (zip: String variable, Zipcode), (title: 
String variable, Title), (timeStamp: String variable, YYYY-MM-DD HH:MM:SS), (twp: String variable, Township), (addr: String variable, Address),
(e: String variable, Dummy variable (always 1))

## Data Source : - Kaggle

## Project Outcomes:
#### By the time we finish this project we would have successfully answered a few questions:

### Importing the database
```
calls_df = pd.read_csv('911.csv')
```
### Viewing first 5 rows of our database
```
calls_df.head()
```
### Basic information about our database
```
calls_df.info()
```
### What are the top 5 zipcodes for 911 calls?
```
calls_df['zip'].value_counts().head()
```
### What are the top 5 townships (twp) for 911 calls?
```
calls_df['twp'].value_counts().head()
```

### Take a look at the 'title' column, how many unique title codes are there?
```
calls_df['title'].nunique()
```

### In the titles column there are "Reasons/Departments" specified before the title code. These are EMS, Fire, and Traffic. Create a new column called "Reason" that contains this string value.
For example, if the title column value is EMS: BACK PAINS/INJURY , the Reason column value would be EMS.
```
calls_df['reason'] = calls_df['title'].apply(lambda x: x.split(':')[0])
calls_df['reason']
```

### What is the most common Reason for a 911 call based off of this new column?
```
calls_df['reason'].value_counts()
```

### Now use seaborn to create a countplot of 911 calls by Reason.
```
sns.countplot(x='reason', data=calls_df, lw=3, ec='white', hatch='/',palette='Set1')
```

### What is the data type of the objects in the timeStamp column?
```
type(calls_df['timeStamp'].iloc[0])
```

### Convert the column from strings to DateTime objects.
```
calls_df['timeStamp'] = pd.to_datetime(calls_df['timeStamp'])
calls_df.dtypes
```

### Create 3 new columns called Hour, Month, and Day of Week.
```
calls_df['Hour'] = calls_df['timeStamp'].dt.hour
calls_df['Month'] = calls_df['timeStamp'].dt.month
calls_df['Day'] = calls_df['timeStamp'].dt.dayofweek
```

###Map the actual string names to the day of the week:
```
calls_df["week_map"] = calls_df['Day'].map({0:'Mon', 1:'Tue', 2:'Wed', 3:'Thurs', 4:'Fri',5:'Sat',6:'Sun'})
calls_df['week_map']
```

### Create a countplot of the Day of Week column with the different Reasons present for contacting the police
```
sns.countplot(x='week_map',data=calls_df,lw=2,ec='white',hue='reason',hatch='/',palette='viridis')
plt.legend(bbox_to_anchor=(1.05, 1),loc='best')
```

### Same as above for Month:
```
sns.countplot(x='Month',data=calls_df,lw=2,ec='white',hue='reason',hatch='/',palette='flare')
plt.legend(bbox_to_anchor=(1.25,1))
```

### The above graph is missing a few months therefore to recifty the error we can do the following:
```
month_by=calls_df.groupby(calls_df['Month']).count()
month_by
month_by['twp'].plot()
```

### Use seaborn's lmplot() to create a linear fit on the number of calls per month.
```
sns.lmplot(x='Month',y='twp',data=month_by.reset_index(),truncate=True,x_jitter=True,y_jitter=True)
plt.grid()
```

### Create a new column called 'Date' that contains the date from the timeStamp column.
```
calls_df['date']=calls_df['timeStamp'].dt.date
calls_df['date'].head()
```

### Now groupby this Date column with the count() aggregate and create a plot of counts of 911 calls.
```
calls_df.groupby(calls_df['date']).count()['twp'].plot(figsize=(8,6))
plt.grid(linestyle='-')
```

### Recreate the above plot but create 3 separate plots with each plot representing a Reason for the 911 call
```
calls_df[calls_df['reason']=='Traffic'].groupby(calls_df['date']).count()['twp'].plot(figsize=(8,6))
plt.grid()
plt.title('Traffic')
```
```
calls_df[calls_df['reason']=='EMS'].groupby(calls_df['date']).count()['twp'].plot(figsize=(12,6))
plt.grid()
plt.title('EMS')
```
```
calls_df[calls_df['reason']=='Fire'].groupby(calls_df['date']).count()['twp'].plot(figsize=(11,6))
plt.grid()
plt.title('Fire')
```

### Restructure the dataframe so that the columns become the Hours and the Index becomes the Day of the Week.
```
Hour_Day = calls_df.groupby(['Day','Hour']).count()['reason'].unstack()
Hour_Day
```

### Create a heatmap of the data made above
```
plt.figure(figsize=(10,5))
sns.heatmap(Hour_Day,cmap="viridis")
```

### Create a clustermap of the same data
```
sns.clustermap(Hour_Day,cmap='viridis')
```

### Now repeat these same plots and operations, for a DataFrame that shows the Month as the column.
```
Day_Month = calls_df.groupby(['Day','Month']).count()['twp'].unstack()
Day_Month
```
### Create a heatmap of the above data
```
plt.figure(figsize=(10,5))
sns.heatmap(Day_Month)
```

### Create a clustermap of the same data
```
sns.clustermap(Day_Month)
```

## Project Setup:
To clone this repository you need to have Python compiler installed on your system alongside pandas and seaborn libraries. I would rather suggest that you download jupyter notebook if you've not already.

To access all of the files I recommend you fork this repo and then clone it locally. Instructions on how to do this can be found here: https://help.github.com/en/github/getting-started-with-github/fork-a-repo

The other option is to click the green "clone or download" button and then click "Download ZIP". You then should extract all of the files to the location you want to edit your code.

Installing Jupyter Notebook: https://jupyter.readthedocs.io/en/latest/install.html<br>
Installing Pandas library: https://pandas.pydata.org/pandas-docs/stable/install.html
