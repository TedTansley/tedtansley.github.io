# A Look Into the City of Detroit’s Sidewalk Reporter System
## Sidewalks marked for inspection within 60 days have waited years for attention.
Detroit residents can report broken sidewalks through a dedicated online web app. However, this tool operates separately from the widely used ImproveDetroit app that connects resident reported issues and makes them visible to all relevant public officials. This separation raises concerns about whether reported sidewalk issues are being addressed effectively.

The [City of Detroit Sidewalk Damage Reporter](https://detroitmi.gov/webapp/sidewalk-map) states : “If you are reporting damaged sidewalks, the sidewalks will be inspected within 60 days. If you are reporting damages to your property made by the sidewalk contractor, we will notify the contractor, and someone will get back to you within 2 weeks.” By glancing at the data viewable in the web app, it made me wonder whether Detroit was actively working on inspecting and contracting out repairs. Especially considering that every record I could see in the app indicates that it is in the “Inspection Pending” phase of the process.

Because the web application and City Data Portal do not provide a direct method to access report data, I developed a [Python script](https://colab.research.google.com/drive/1xBYDy5QrychKWoJNr50Zsqqoq__XO3pC?usp=sharing) to export and analyze the sidewalk reporting data. I was able to make an HTTP request to the ArcGIS REST API to get the data from the application and be able to view it in a more manageable (and user friendly) way. This allowed me to examine when reports were created and whether they received an inspection date. Here is my [export of the data](https://docs.google.com/spreadsheets/d/1P34wkX8-CWf07P8d3b9Rdux5BqegkuTQXKqkvMFLis8/edit?usp=sharing) (note, 2025 data is included still). 

The immediate finding was that out of 10,454 records (dating to before current year 2025), not a single one had an inspection date. Now, the city typically spends $4.5 million per year into their sidewalk replacement program and had [recently increased that investment](https://www.fox2detroit.com/news/city-of-detroit-unveils-plan-for-sidewalks-repairs-and-replacements) to add an additional $20.5 million in 2023. 

This must mean that all the reports are new and are simply waiting the two weeks for inspection, right? Well, when we look at the creation date found in the data, we get the following:
![](/images/bar-chart-of-sidewalk-reports-per-year.png "This bar chart shows the number of reports that remain unaddressed by creation year")

Hundreds and even thousands of reported sidewalks from as far back as 2018 without a resolution. This could lead to many dissatisfied Detroiters with the perception that the city isn’t doing anything with the reports and is only fixing more advantaged areas. To know with more certainty about where the city is and isn’t working, we would need more open data about these sidewalk requests so a more complete analysis can be performed.

The data does reveal a troubling trend: an exponential rise in reports going unaddressed.
![](/images/cumulative-line-chart-of-reports-by-year.png "This line chart shows a rising total of unaddressed sidewalk repair requests being made.")

What is this telling us? That reports do not have an address included? Only 6 of the reported 10K+ do not have an address listed when I originally looked. By the time of this posting, they had all seemingly been addressed. 

![](/images/nan-counts.PNG "Python output of missing data")

The city claims on their [sidewalk program page](https://detroitmi.gov/departments/department-public-works/sidewalk-program) that they “[have] funding to replace 70,000 broken sidewalk slabs in 2023.  This represents approximately 20% of all damaged sidewalk citywide.”. Why did the number of unaddressed reports rise by more than a thousand in the year 2024? Where is the data on completed sidewalk repair if not in their own sidewalk reporting web app? 

If this issue stems from reporting errors, it underscores the urgent need for the city to improve data accuracy and transparency. Clearer and more accessible data is essential to demonstrate progress in repairing Detroit’s sidewalk network—particularly for residents disproportionately impacted by broken sidewalks and those who take the time to report these issues through the web app.

As [Outlier Media had reported in 2024](https://outliermedia.org/detroit-sidewalks-missing-broken-replacement-accessible-public-works/), “DPW said it plans to replace 40,000 more pieces of damaged sidewalks throughout neighborhoods this coming year and is prioritizing repairing sidewalk problems reported by residents”. This gives me hope that by year’s end we will see these reports addressed. 
