# Reading the data and cleaning it
```python
import pandas as pd
import matplotlib.pyplot as plt
```
```python
#This tells pandas to read the file in one go and figure it out better:

Aviation_df=pd.read_csv("/content/Aviation_Data.csv", low_memory=False)

Aviation_df.head()
```

```python
Aviation_df.info()
```

# Key Observations: You have 90,348 rows and 31 columns.

Many columns like Latitude, Longitude, Aircraft.Category, etc. have a lot of missing values.

Most columns are of type object, and 5 are numerical (float64).
```python
#checking Missing Data in Detail
Aviation_df.isnull().sum().sort_values(ascending=False)
```
Droping Columns With Too Much Missing Data We’ll drop columns with more than 60% missing, since they are likely not usable:
```python
Aviation_df.drop(['Schedule', 'Air.carrier', 'FAR.Description', 'Aircraft.Category', 'Latitude', 'Longitude'], axis=1, inplace=True)
```

Confirm Remaining Columns Check again which columns still have missing data (but less severe):
```python
Aviation_df.isnull().sum().sort_values(ascending=False)
```

I still have moderate missing data in several columns, especially:

Airport.Code, Airport.Name (missing around 40k)

Injury data (Total.Serious.Injuries, Fatal, Minor)

Engine.Type, Purpose.of.flight, etc.

```Python

Aviation_df.drop(['Airport.Code', 'Airport.Name'], axis=1, inplace=True)
```
Am droppping Airport code and Airport name coz are of less importance in my analysis And fill Injury Columns ; fill missing with 0.0 since no data might mean no injuries:

```python
#is risky because it might be working on a copy of the data, not the real DataFrame.
#So in the future, it won’t work the way you expect.
Aviation_df['Total.Fatal.Injuries'] = Aviation_df['Total.Fatal.Injuries'].fillna(0.0)
Aviation_df['Total.Serious.Injuries'] = Aviation_df['Total.Serious.Injuries'].fillna(0.0)
Aviation_df['Total.Minor.Injuries'] = Aviation_df['Total.Minor.Injuries'].fillna(0.0)
Aviation_df['Total.Uninjured'] = Aviation_df['Total.Uninjured'].fillna(0.0)
```
Categorical Columns – fill with "Unknown":

```python

Aviation_df['Engine.Type'] = Aviation_df['Engine.Type'].fillna("Unknown")
Aviation_df['Purpose.of.flight'] = Aviation_df['Purpose.of.flight'].fillna("Unknown")
Aviation_df['Weather.Condition'] = Aviation_df['Weather.Condition'].fillna("Unknown")
Aviation_df['Aircraft.damage'] = Aviation_df['Aircraft.damage'].fillna("Unknown")
Aviation_df['Registration.Number'] = Aviation_df['Registration.Number'].fillna("Unknown")
Aviation_df['Injury.Severity'] = Aviation_df['Injury.Severity'].fillna("Unknown")
Aviation_df['Country'] = Aviation_df['Country'].fillna("Unknown")
Aviation_df['Amateur.Built'] = Aviation_df['Amateur.Built'].fillna("Unknown")
Aviation_df['Model'] = Aviation_df['Model'].fillna("Unknown")
Aviation_df['Make'] = Aviation_df['Make'].fillna("Unknown")
Aviation_df['Location'] = Aviation_df['Location'].fillna("Unknown")
Aviation_df['Report.Status'] = Aviation_df['Report.Status'].fillna("Unknown")
Aviation_df['Broad.phase.of.flight'] = Aviation_df['Broad.phase.of.flight'].fillna("Unknown")
```
Printed the first ten dates to see how it looks
```python
print(Aviation_df['Event.Date'].head(10))
```
Wanted to confirm the data type ,if its in the reall format of dates
```python
print(Aviation_df['Event.Date'].dtype)
```
Saw some had unfilled date represented with NaN
```python
print(Aviation_df['Event.Date'].sample(10))  # Random sample of 10 values
```
Replaced the missing values with 2000-01-01
```python
# Step 1: Convert to datetime, forcing invalid ones to NaT
Aviation_df['Event.Date'] = pd.to_datetime(Aviation_df['Event.Date'], errors='coerce')

# Step 2: Replace only the NaT values with placeholder
Aviation_df['Event.Date'] = Aviation_df['Event.Date'].fillna(pd.Timestamp('2000-01-01'))
```
Confirmed how many were replaced
```python
print("Replaced missing dates:", (Aviation_df['Event.Date'] == '2000-01-01').sum())
```
Convert Event.Date and Fill:

Final check of the data after cleaning

```Python
Aviation_df.isnull().sum().sort_values(ascending=False)
```
Want to drop Publication (date mainly used in reporting not safety ) and Unique identifiers (mainly for traceability but not analysis) and fill the number of enginees coz safety sometimes depend on it

```python
# Drop unnecessary columns
Aviation_df.drop(['Publication.Date', 'Event.Id', 'Accident.Number'], axis=1, inplace=True)

# Fill missing engine counts with 0 (or 'Unknown' if you want to treat them differently)
Aviation_df['Number.of.Engines'] = Aviation_df['Number.of.Engines'].fillna(0)
```
The final check of of my data
```python
Aviation_df.isnull().sum().sort_values(ascending=False)
```
Perfect now am good to go
```python
Aviation_df.info()
```
saving the cleaned data
```python
Aviation_df.to_csv("Cleaned_Aviation_Data.csv", index=False)
```
```python
# Step 3: Get unique values and their counts
purpose_counts = Aviation_df['Purpose.of.flight'].value_counts()
print(purpose_counts)
```

```python
purpose_counts = Aviation_df['Engine.Type'].value_counts()
print(purpose_counts)
```

Saving in my project folder the cleaned data to use in tablue

Want to do the viasualization with the cleaned data

```python
Cleaned_df=pd.read_csv("/content/Cleaned_Aviation_Data.csv", low_memory=False)
Aviation_df.head()
```
```python
Cleaned_df.info()
```
Correcting the data type for Make Column
```python
Cleaned_df["Make"].value_counts()
```
Replace None with appropriate text """ The two cases of inconsistent Make naming are:

"Cessna" and "CESSNA" – These should be combined as they refer to the same Make. "piper" and " PIPER" – The second entry has a leading space and should be cleaned to match the first.

"""
```python
# Your code here
# Strip leading/trailing spaces
Cleaned_df["Make"] = Cleaned_df["Make"].str.strip()
# Replace 'Marvel' with 'Marvel Comics'
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Cessna", "CESSNA")
# Replace any remaining 'DC Comics' variants with consistent name
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Piper", "PIPER")
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Bell", "BELL")
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Beech", "BEECH")
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Boeing", "BOEING")
```
Checking wether the changes were applied

```python
Cleaned_df["Make"].value_counts().head(20)
```
Saving the changes
```python

Cleaned_df.to_csv("Cleaned_Aviation_Data002.csv", index=False)
```

Creating a gragh of model&make against the Investigation.Type .Had filtered the top 20 records and conditioned with>=10 and >=5 for both model and make
```python

# Load the dataset
df = pd.read_csv("/content/Cleaned_Aviation_Data002.csv")

# Filter for accidents only
accidents = df[df['Investigation.Type'] == 'Accident']

# Get top 20 models by accident count
top_models = accidents['Model'].value_counts().head(20)

# Get the make for each of these top models
model_make = accidents.groupby('Model')['Make'].first()

# Create a combined dataframe
frequency = pd.DataFrame({
    'Count': top_models,
    'Make': model_make[top_models.index]
}).reset_index()

frequency.columns = ['Model', 'Count', 'Make']

# Combine Make and Model
frequency['Make_Model'] = frequency['Make'] + ' ' + frequency['Model']

# Sort by accident count (least to most)
frequency = frequency.sort_values('Count', ascending=True)

# Plot
plt.figure(figsize=(12, 10))
bars = plt.barh(
    y=frequency['Make_Model'],
    width=frequency['Count'],
    color='#1f77b4'
)

# Add labels to bars
for bar in bars:
    width = bar.get_width()
    plt.text(width + 5,
             bar.get_y() + bar.get_height() / 2,
             f'{int(width)}',
             va='center',
             fontsize=10)

# Customize plot appearance
plt.xlabel('Number of Accidents', fontsize=12)
plt.ylabel('Aircraft Make & Model', fontsize=12)
plt.title('Top 20 Aircraft Models by Accident Frequency (Sorted Least to Most)',
          fontsize=14, pad=20)
plt.grid(axis='x', linestyle='--', alpha=0.3)

# Remove top and right spines
for spine in ['top', 'right']:
    plt.gca().spines[spine].set_visible(False)

# Final layout and show plot
plt.tight_layout()
plt.show()
```

```python
Resposes explaining the gragh 
"""
For the Top 20  Model 
Top Risky Models:
Cessna 152: 94 incidents
Cessna 172: 178 incidents
Piper PA-28-140: 33 incidents

Which Concluded:
These popular training planes have high exposure  Not ideal for new operators.
Why? 
These models dominate incident reports; likely due to training use and high fleet numbers!
"""
```
Damage Severity graph the Finding 2

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load and filter data
df = pd.read_csv("/content/Cleaned_Aviation_Data002.csv")
accidents = df[df['Investigation.Type'] == 'Accident']
top_models = accidents['Model'].value_counts().head(20).index
filtered = accidents[accidents['Model'].isin(top_models)]

# Replace your current Risk Level assignment with:
filtered = filtered.copy()  # Explicitly create a copy first
filtered['Risk Level'] = ['High Risk' if (sev == 'Fatal') or (dam == 'Destroyed')
                         else 'Standard Risk'
                         for sev, dam in zip(filtered['Injury.Severity'], filtered['Aircraft.damage'])]
# Prepare data
risk_counts = filtered.groupby(['Model', 'Risk Level']).size().unstack().fillna(0)
risk_counts = risk_counts.sort_values('High Risk', ascending=False)

# Create plot
#plt.figure(figsize=(12, 7))
plt.figure(figsize=(24, 14))  # Larger dimensions
plt.tight_layout(pad=3)  # Add extra padding
risk_counts[['High Risk', 'Standard Risk']].plot(
    kind='bar',
    stacked=True,
    color=['red', 'blue'],  # Simple, clear colors
    width=0.8
)

# Basic formatting
plt.title('Top 20 Aircraft Models by Risk Level', size=14)
plt.xlabel('Model', size=12)
plt.ylabel('Count of Injury.Severity', size=12)
plt.xticks(rotation=45, ha='right')
plt.legend(title='Risk Level')
plt.grid(axis='y', alpha=0.2)

# Add total counts
for i, total in enumerate(risk_counts.sum(axis=1)):
    plt.text(i, total + 5, str(int(total)), ha='center', size=10)

plt.tight_layout()
plt.show()
```

```python
Response for the Damage Severity 
"""
Shows some models frequently result in total losses(35% for Cessana 152)
Ground Cessna 152/172 and Piper PA-28s from initial fleet plans.
Lease Cessna 206B for early operations to minimize risk.
Audit Boeing 737 data for commercial viability.
"""
"""
Cessna 152: 35% of accidents result in total loss.
Piper PA-28-140: 99.7% severe damage rate.
Cessna 206B: 92% minor damage—ideal for cost control
"""
```
Finding 3-Safety Score gragh

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load data
df = pd.read_csv("/content/Cleaned_Aviation_Data002.csv", parse_dates=['Event.Date'])

# Filter for accidents (2013-2022)
accidents = df[(df['Investigation.Type'] == 'Accident') &
               (df['Event.Date'].dt.year.between(2013, 2022))].copy()

# Get top 20 models by incident count
top_20_models = accidents['Model'].value_counts().nlargest(20).index
accidents_top20 = accidents[accidents['Model'].isin(top_20_models)]

# Calculate required metrics
safety_data = accidents_top20.groupby('Model').agg(
    Total_Incidents=('Investigation.Type', 'count'),
    Fatal_Incidents=('Total.Fatal.Injuries', 'sum'),
    High_Risk=('Injury.Severity', lambda x: ((x == 'Fatal') |
              (accidents_top20.loc[x.index, 'Aircraft.damage'] == 'Destroyed')).sum()),
    Standard_Risk=('Injury.Severity', lambda x: ((x != 'Fatal') &
                (accidents_top20.loc[x.index, 'Aircraft.damage'] != 'Destroyed')).sum())
)

# Calculate Safety Score (your exact formula)
max_total = safety_data['Total_Incidents'].max()
max_fatal = safety_data['Fatal_Incidents'].max()

def calculate_safety(row):
    if row['Total_Incidents'] == 0:
        return 100.0
    incident_part = (1 - row['Total_Incidents']/max_total) * 60
    fatal_part = (1 - row['Fatal_Incidents']/max_fatal) * 40
    return (incident_part + fatal_part) * 100

safety_data['Safety_Score'] = safety_data.apply(calculate_safety, axis=1)
safety_data = safety_data.sort_values('Safety_Score')

# Create horizontal bar chart
plt.figure(figsize=(12, 8))
bars = plt.barh(
    y=safety_data.index,
    width=safety_data['Safety_Score'],
    color='#4CAF50',  # Green color
    height=0.7,
    edgecolor='darkgreen'
)

# Add score labels
for bar in bars:
    width = bar.get_width()
    plt.text(
        width - 2 if width > 50 else width + 2,
        bar.get_y() + bar.get_height()/2,
        f'{width:.1f}',
        va='center',
        color='white' if width > 50 else 'black',
        fontweight='bold'
    )

# Formatting
plt.title('Top 20 Aircraft Models by Safety Score (2013-2022)', pad=15, fontsize=14)
plt.xlabel('Safety Score (0-100)', fontsize=12)
plt.ylabel('Aircraft Model', fontsize=12)
plt.xlim(0, 105)
plt.grid(axis='x', linestyle=':', alpha=0.3)

# Add reference lines
plt.axvline(80, color='green', linestyle='--', alpha=0.5, label='Excellent (80+)')
plt.axvline(50, color='orange', linestyle='--', alpha=0.5, label='Caution (<50)')
plt.legend(loc='lower right')

# Adjust layout
plt.tight_layout()
plt.show()

# Display data
print("Safety Data for Top 20 Models:")
display(safety_data[['Total_Incidents', 'Fatal_Incidents', 'High_Risk', 'Standard_Risk', 'Safety_Score']]
       .sort_values('Safety_Score', ascending=False))
```

```python
Responses
"""
Cessna 152: 9,990.77 score but 1,883 severe accidents (Sheet 2).
Boeing 737: 9,999.60 score + 1 incident (trustworthy).

"""
"""
Cessna 206B (low damage) and Boeing 737 (low incidents + high score).

"""
```

Reccommendation """ Immediate Buys Cessna 206 Priority acquisition-lowest damage rates Commercial option Boeing 737 Lease for passenger routes-validated safety

Conditional Use Cessna 172/152 Only with school paertnerships(risk transfer) """

Q&A """ Cessna 206B (low damage) and Boeing 737 (low incidents + high score). """

Conclusion """ While safety scores appear strong across all models, cross-referencing with damage data reveals: Two safe paths forward: Build fleet around Cessna 206B/Boeing 737 Negotiate risk-sharing for essential training aircraft """


#Kindly Find my Attached link to tableau dashboard


https://public.tableau.com/views/Project_Aviation_Phase001/AirSafetyDashboard?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

<img width="1302" height="537" alt="Air Safety Dashboard" src="https://github.com/user-attachments/assets/965162e2-a067-4e1c-9121-e212cd09f690" />




