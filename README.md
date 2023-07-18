
# Joinery: Political Data Merging and Aggregation

Joinery is a Python class based on its [R-based equavalent](https://github.com/mattwloftis/joinery#readme), designed to ease the process of merging and aggregating text-based political data used in research within the field of political science. 

The library provides a class called `Jnry` that facilitates the integration of data from the handling data from sources like the [Comparative Agendas Project (CAP)](https://www.comparativeagendas.net/) and/or the [Manifesto Project (CMP)](https://manifesto-project.wzb.eu/). 


# Overview of Jnry functionality
The `Jnry` class takes the following parameters during initialization:

`country`: The country for which the political data will be processed.
`path`: The path to the CSV file containing the target political data. If df is not provided, the data will be read from this file.
year (optional): The specific year to use for data processing. If not provided, the current year will be used.
`df`(optional): A dataframe containing the target political data. If path is provided, this parameter is ignored.

# Functions and Methods
The Jnry class provides the following functions and methods:

`get_jnry_year(yr)`: This method parses the provided yr and returns the year as an integer. If yr is not provided, it returns the current year.

`get_jnry_country()`: This method converts the provided country to the ISO3 country code format using the `country_converter`` library. If the conversion fails, it returns the original country name.

`get_unique_country_year_combinations()`: This method returns a dictionary of the unique combinations of years and political parties found in the target DataFrame. These combinations will be matched with the PartyFacts data.

`get_party_facts()`: This method downloads the PartyFacts data using the provided URL and filters it based on the jnry_country.

`get_party_facts_ids()`: This method performs the matching between the target DataFrame and PartyFacts data. It returns a list of dictionaries, each containing information about the matched political party, including the year, jnry_year, politicalparty, partyfactors_id, and wikipedia.

`merge_party_facts_with_target()`: This method merges the [PartyFacts](https://partyfacts.herokuapp.com/) data with the target DataFrame based on the year and politicalparty columns. The resulting DataFrame contains the original data from CMP or CAP, along with additional columns containing PartyFacts information for each unique combination of political parties and years.

## Installation
First, make sure you have Python installed on your system. To install Joinery and its dependencies, run the following command:

```python
pip install pandas datetime country_converter dateutil fuzzywuzzy
```

## Using the Manifesto API
Joinery allows you to interact with the Manifesto API to retrieve political data for various countries and timeframes. To use this functionality, request an API key at [https://manifesto-project.wzb.eu](https://manifesto-project.wzb.eu/) and place it in `src/config.py`

### 1a. Get an Overview of Available Countries

```python
VERSION = "MPDS2021a"
a = Manifesto_data(VERSION, manifesto_key)
meta_data = a.get_meta_data()
meta_data.head()
```

### 1b. Get Available Years for a Particular Country
```python
a.get_country_specific_df('Norway')
```

### 1c. Get Annotated Dataset for Selected Timeframe + Country

```python
# Please note that if you leave `timeframe` empty, you will get all available data for the specified country.
norway = a.get_annotations_per_country("Norway", timeframe=[2009, 2013, 2017])
```

# Core functionalities Joinery

## Merging with PartyFace Data. 

The key functionalities of the Joinery class are as follows:

### 2a. Create an Instance of the Jnry Class

```python
j = Jnry(country='Norway', df=norway, source='CMP')
```

### 2b. Create Merged DataFrame

The `merge_party_facts_with_target()` method merges the data from the PartyFacts data with the target DataFrame, based on the `year` and `politicalparty` columns. The result of this merge operation is stored in the variable `df`.

```python
df = j.merge_party_facts_with_target()
df.head()
```

### 2b. Same, Using CMP Data File from Disk

You can also use a CSV file containing the CMP data from disk instead of a dataframe retrieved via the API.

```
j = Jnry(country='Spain', path_to_file="spain.csv", source='CMP')
df_spain = j.merge_party_facts_with_target()
df_spain.head()
```


### 2c. Same, using CAP data.

```
path_to_file = 'https://comparativeagendas.s3.amazonaws.com/datasetfiles/Party_Manifestos_CAP_Web_csv.csv'
j = Jnry(country='Spain', path_to_file=path_to_file, source='CAP')
CAP_spain = j.merge_party_facts_with_target()
CAP_spain.head()
```

The examples provided above assume that the required data files and keys are available and accessible. Make sure to replace the placeholders with links retrieved from CAP and requested API access to execute the code successfully.