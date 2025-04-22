# Building an ETL Pipeline and Dashboard for Detroit’s 311 Dataset

As part of my work with [DETROITography](https://detroitography.com/)’s [Detroit Data](https://detroitdata.org/) open data portal project, I was asked to set up a simple dashboard to showcase publicly available data for our summer open data ambassadors program. The Improve Detroit / 311 reports were selected as a dataset that was not only recognizable, but allowed others to interact with it in ways that other datasets wouldn’t immediately offer itself to. It seemed simple enough to connect the data directly into a dashboard, but as I continued to work I learned quickly that I had to meet the size of the data set and scale my solution in a way that allowed immediate access with little loading downtime. 

First, the data. I was looking at the city’s [ArcGIS REST Services Directory](https://services2.arcgis.com/qvkbeam7Wirps6zC/arcgis/rest/services/Improve_Detroit_Issues_Test/FeatureServer/0) for the source. It is constantly updated with new reports and I thought it would be perfect to be able to connect directly and get the data for the dashboard in full. Initially, I tried to pull the data into a Google Sheet through Google Scripts, but quickly ran into two major problems:
1. I could only grab so much of the reports in a single pull which led me to pull in 1,000 rows every five minutes to avoid rate limiting and timeouts. This slowed our data pull immensely as we needed to grab all 600,000+ rows by the end of it. 
2. Once the script ran out of records to pull, it would restart the process…wiping out the Google Sheet and starting over. This was the only way I knew would ensure we could get any new reports coming in. 

While this was not what I wanted, it gave me the chance to build up a dashboard and start to think about how else I could solve these problems to get something that would work seamlessly and automatically. While I was working on this, I was also developing a side project to automatically document and record sidewalk report data into a SQLite database using Github Actions. This sparked an idea for me to do something very similar for the Improve Detroit dataset. However, with the Improve Detroit dataset being massively larger than the sidewalk report dataset, and there not being an obvious connector into my Looker Studio dashboard, I landed on using Google’s BigQuery as my database. 

What I knew was that I needed to develop what is considered an ETL Pipeline or Extract, Transform, Load Pipeline. I had to **Extract** the Improve Detroit data from the Services Directory, **Transform** it for my dashboard uses and potential future uses, and **Load** it into the Google Big Query so that it is housed there. I also needed to set this all up so that I could get the latest data on a regular basis in an automated way. 

My primary script was written in Python, designed to handle the heavy lifting of taking the data along the pipeline and into the database. As I was developing it, I encountered several challenges that ultimately strengthened the final solution:

- **Data volume and rate limiting:** I found early on that the services directory wouldn’t let me pull all of the data all at once and so to prevent it from automatically returning a server error to my request, I broke it down into small batches of reports and looped over the request. I further added functions to allow additional attempts in the event that I do experience rate limiting. 

```
def safe_request(url, params, retries=3, delay=2):
    for attempt in range(retries):
        try:
            response = requests.get(url, params=params)
            if response.status_code == 429:  # Rate limit
                print(f"Rate limit hit, retrying in {delay * (2 ** attempt)} seconds...")
                time.sleep(delay * (2 ** attempt))  # Exponential backoff
                continue
            response.raise_for_status()  # Raise an error for bad status codes
            return response
        except RequestException as e:
            print(f"Request failed: {e}. Retrying... ({attempt + 1}/{retries})")
            time.sleep(delay)  # Wait before retrying
    return None
```

- **Schema and metadata alignment:** To ensure that BigQuery would take the data, I had to ensure that the data pulled aligned with a schema table that BigQuery can understand. Thankfully the city had available metadata for me to reference. 

```
schema = [
    bigquery.SchemaField("ID", "INTEGER"),
    bigquery.SchemaField("Status", "STRING"),
    bigquery.SchemaField("Request_Type_Title", "STRING"),
    bigquery.SchemaField("Report_Method", "STRING"),
    bigquery.SchemaField("Created_At", "TIMESTAMP"),
    bigquery.SchemaField("Acknowledged_At", "TIMESTAMP"),
    bigquery.SchemaField("Closed_At", "TIMESTAMP"),
    bigquery.SchemaField("Reopened_At", "TIMESTAMP"),
    bigquery.SchemaField("Updated_At", "TIMESTAMP"),
    bigquery.SchemaField("Days_to_Close", "FLOAT"),
    bigquery.SchemaField("Address", "STRING"),
    bigquery.SchemaField("Neighborhood", "STRING"),
    bigquery.SchemaField("Council_District", "STRING"),
    bigquery.SchemaField("Latitude", "BIGNUMERIC"),
    bigquery.SchemaField("Longitude", "BIGNUMERIC"),
    bigquery.SchemaField("Zip_Code", "STRING")
]
```

- **Data type inconsistencies:** The Latitude and Longitude fields fluctuated in length, causing errors with attributing them to the NUMERIC type. I standardized these fields to BIGNUMERIC in BigQuery to handle variations safely. Additionally, the Days_to_Close field was a string/text type which I converted into FLOAT so I could work with it in my dashboard and keep it accurate to the decimal. 
- **Resource management and BigQuery integration:** Transitioning from Google Sheets to BigQuery required that I set the correct project environments, define access scopes, and connect a Google Cloud Service Account to automate secure data transfers.
- **Automation and scalability:** Using GitHub Actions, I automated regular data retrieval and loading into BigQuery. Secure key management and YAML configuration files allowed me to connect files and access keys without exposing sensitive credentials.

```
       env:
          GCP_SERVICE_ACCOUNT_JSON: ${{ secrets.GCP_SERVICE_ACCOUNT_JSON }}
          PROJECT_ID: ${{ secrets.PROJECT_ID }}  # Add PROJECT_ID
          DATASET_ID: ${{ secrets.DATASET_ID }}  # Add DATASET_ID
          TABLE_ID: ${{ secrets.TABLE_ID }}      # Add TABLE_ID
          GIS_REST_URL: ${{ secrets.GIS_REST_URL }}
        run: python gis_to_bigquery.py
```

This experience not only deepened my understanding of cloud database management and pipeline automation, but also taught me how to build systems that are scalable, maintainable, and production-ready. It also allowed me to write more advanced code in a way that just isn’t possible in simple notebooks like I had been using for a long time.

At the end of my development, I had three documents working together to build what I needed and left me room to do more with it if I wanted. Connecting to Looker studio was fairly simple with the exception of the latitude and longitude fields needing to be combined in the dashboard to allow for the heatmap visualization. 

Now, when I got everything set up and connected into Looker Studio, I found three things that are in need of further investigation:
- There are 40,000+ reports with redacted addresses. [~6% of all reports] 
- Not all reports are properly assigned to the correct council district.
- Over 1,000 reports do not have a council district assigned at all.

Each of these puts into question whether decisions being made off this data are taking into account the inconsistencies found. I’ll be publishing more results on this in a coming post as I get the opportunity to dig in further. 

You can view [my repository on Github](https://github.com/TedTansley/improve-detroit-issues) which has a link in the README file to the Looker Studio report. 
