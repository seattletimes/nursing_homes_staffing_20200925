![The Seattle Times](https://github.com/seattletimes/nursing_homes_staffing_20200925/tree/master/assets/ST_Logo.png)

# Summary

As the coronavirus known as SARS-CoV-2 started spreading over the United States at the beginning of 2020, it became clear that some of the most vulnerable places to the virus were long-term care facilities. These are establishments whose role is to take care of elderly people who are not capable of fully looking after themselves. Washington state became the early epicenter of the pandemic in the United States, and long-term care facilities across the state witnessed a sudden spike in COVID-19 cases and deaths. 

Why have residents from those facilities been so severely affected by the virus? 

Nursing homes are governed by state and federal regulations, the role of which is to guarantee safety and a minimum quality of life for their residents. They regulate aspects such as quality of care, resident rights, infection control and physical environment. In retrospect, those regulations did not prevent hundreds of senior people from dying of COVID-19. Were those regulations inadequate for the extraordinary situation that became the pandemic? Were they not properly enforced? Or was it something else?

To find the answer, [The Seattle Times](https://www.seattletimes.com/) engaged in data-intensive investigation. Our methodology consisted of the following three general steps:

1. Understand the state and federal regulatory framework that governs nursing homes. 
1. Obtain the history of compliance with those regulations from each nursing home in Washington. For each facility, we wanted to obtain a historical record of any time it was found to be out of compliance with regulations, potentially affecting the safety and quality of care it provided its residents. 
1. Finally, find out what consequences, if any, such compliance deficiencies had for the nursing homes. In other words, did those deficiencies result in penalties imposed by state or federal authorities? If so, what kind of penalties were imposed and how severe were they?

In this repository, we present the process we followed to search for, obtain and analyze those data.

# The regulatory system

Nursing homes across the country are regulated at two levels: state and federal. In the state of Washington, they are under the jurisdiction of the Department of Health (DOH). 

At the federal level is the Centers for Medicare & Medicaid Services (CMS), an agency within the Department of Health and Human Services (HHS), is responsible for ensuring approximately 15,500 nursing homes nationwide meet federal quality standards to participate in the Medicare and Medicaid programs.

All nursing homes are subject to surveys to confirm that they are in compliance with both regulation systems. The federal authorities don’t dispatch their own surveyors, but enter into agreements with agencies in each state government—known as state survey agencies—who in each of their visits check for compliance with both state and federal regulations.

After they have conducted a survey, state DOH officials do three things:
1. Decide if a nursing home presented deficiencies in compliance with both types of regulations. 
1. If there were deficiencies regarding state regulations, they decide what type of enforcement to apply, if any. The most common is financial fines. 
1. If there were deficiencies regarding federal regulations, they make suggestions to the federal authorities regarding possible enforcements. It is up to the federal government to decide whether to implement them or not. 

# Obtaining the data

It soon became clear that not all the data we needed existed, at least not in a structured form. While part of it was readily available from APIs, a large part of the information was contained across thousands of pages of PDF documents.


## Deficiency data

State authorities did not have them readily available, so the the data regarding nursing homes’ deficiencies was obtained from the [CMS Nursing Home Compare database](https://www.cms.gov/Research-Statistics-Data-and-Systems/Research-Statistics-Data-and-Systems), which makes it available in a structured format. Fortunately, a crucial aspect of the system is that local authorities report all their findings to the CMS.

There are several datasets published in the CMS Nursing Home Compare portal, such as lists of providers, ownership, staffing, penalties, etc. We were interested in health deficiencies, and we found two ways they publish those data:

- Their [Health Deficiencies Socrata portal](https://data.medicare.gov/Nursing-Home-Compare/Health-Deficiencies/r5ix-sfxw/)
- A monthly recurring series of compressed CSV files. 

We ended up using the second one. The data contained in both sources are consistent with each other, but they have slightly different information. In both of them, each record represents a particular deficiency committed by a particular facility at a particular date. But while the CSV files include a detailed description of each deficiency, the Socrata data does not. 

The compressed CSV files are published monthly and each one contains a history of deficiencies that goes back about three years. In theory, each monthly package overlaps to a point with several files published before it. Each of the CSV packages can be downloaded by writing the following generic URL in a browser (each package is roughly 500MB and will start downloading immediately after it is typed in the browser):

http://downloads.cms.gov/files/Full-Statement-of-Deficiencies-Month-Year.zip

For example, to obtain the package for June 2020, one would use this URL:

http://downloads.cms.gov/files/Full-Statement-of-Deficiencies-June-2020.zip

## Enforcement data

Once we obtained the history of deficiencies for each nursing home, we wanted to know what, if any, consequences those deficiencies generated.

Just as with deficiencies, CMS has a [Socrata portal for penalty data](https://data.medicare.gov/Nursing-Home-Compare/Penalties/g6vv-u9sr). However, there were two  limitations with these data: They only correspond to penalties imposed by the CMS, not by the state. They do not report what type of deficiency generated the penalty. 

If we wished to know what enforcement actions the state took, we would need to find that information locally. Unfortunately, the state DOH does not provide that data in such an organized and structured way as CMS does; that information was contained across thousands of pages of PDF reports. 

Those reports are called “enforcement letters,” and thousands of pages of them are regularly published online by the Aging and Long-Term Support Administration (ALTSA), a subdivision of the state DOH. The ALTSA's [Assisted Living Facilities Locator](https://fortress.wa.gov/dshs/adsaapps/lookup/bhpublookup.aspx) generates a CSV file containing the location of the enforcement letters issued to nursing homes during the previous three years. There were almost 1,000 of such letters since 2017 and we downloaded them all. 

Unfortunately, we found out that at any given time, not all letters are actually in that portal. For example, if a nursing home closed down for business or changed ownership during the previous three years, its enforcement letters were automatically purged from the website. So besides the bulk downloading, we had to issue several public records requests to the DOH. TK: DID WE MANAGE TO OBTAIN ALL OF THEM IN THE END, OR WERE SOME LEFT OUT? 

Fortunately, not only were the PDF letters in text-based format, but their structure was highly regular. They mostly had the same sections and the same language. It seemed obvious they were generated by some standardized digital entry form—think of a Google form. This made them good candidates for scraping, which is what we did. 

As a corollary, it is worth mentioning that scraping PDFs should never—emphasis on NEVER—be a data journalist’s first choice. In the ideal world, this information would have also been neatly available in a structured format, such as CMS’s Socrata API. The decision to dive into a PDF ocean should only be taken after it has been confirmed beyond doubt that is the only way to obtain the data—whether because it only exists in such form, or, if a structured version does exist, it is not possible to access it. 

Alas, in this case, the PDFs were our only choice, so we counted ourselves fortunate that they had the features that made them scrapable.

## Other supporting data

The previous were the two main data dishes at the feast, but they would not have possessed the proper nutrition without a few other necessary ingredients, such as the list and classification of all state and federal regulation codes, for example. 

Links to all sources can be found in the section conveniently named “Sources”.


## Big Local News

Wishing that our work could potentially help other organizations, The Seattle Times is collaborating with [Big Local News](https://biglocalnews.org/), a program of Stanford University’s Journalism and Democracy Initiative that collects local data to discover the regional or national patterns that will yield stories with impact. 

All the data we obtained in this project can be obtained from their portal. **TK: BLN PORTAL LINK**


# The repository



## Structure

The repository is organized as follows:
- **A_source_data**: As the name implies, the official source data we work on.
- **B_scripts**: All our wacky [Python](https://www.python.org/) code can be found here, organized in a series of [Jupyter](https://jupyter.org/) notebooks. A description of each script notebook can be seen in the next section of this README, as well as inside the notebooks themselves.
- **C_output_data**: Data created by the scripts, used to arrive to findings.
- **D_documents**: Explanatory and reference documents obtained from official sources.


## Scripts

### Section A (Enforcement data)
The scripts of this section process *local* data, mostly obtained from the enforcement letters issued to nursing homes by the state's Department of Social and Health Services (DSHS). From them, we obtained the penalizations imposed to nursing homes who incurred in regulation deficiencies.

**A_Enf_Letters_Step1_Download_PDFs** - We download all the available PDF enforcement letters issued to nursing homes by the Washington State Department of Social and Health Services (DSHS).
The letters available for download are updated each month, so we ran this process a few times since March 2020.

**A_Enf_Letters_Step2_Comparing_Dowload_Batches** - We run some tests to compare the sets of letters we obtained in each of the downloads we did during the last few months, to make sure we have no duplicates or that we are missing letters.

**A_Enf_Letters_Step3_Split_Early_Letter_PDFs** - We process the earliest enforcement letters, corresponding to January-February of 2017, which we obtained as a single large PDF that could not be processed in the same way as the other letters.

**A_Enf_Letters_Step4_Scraping_PDFs** - Now that we have identified the set of unique enforcement letters that will be out target, we dive into a scraping odyssey, using [Jeremy Singer-Vine](https://github.com/jsvine)'s mind-blowing Python package, [PDF Plumber](https://github.com/jsvine/pdfplumber).

**A_Enf_Letters_Step5_Download_WAC_Codes** - The data we scraped from the enforcement letters include the [Washington Administrative Codes (WAC)](https://apps.leg.wa.gov/wac/default.aspx?cite=388-97) that the surveyed nursing homes were found to be out of compliance with. In this script, we add the details of each of those codes.

### Section B (Deficiencies data)
This section processes *federal* data, obtained from the [Centers for Medicare and Medicaid Services](https://www.cms.gov/). The CMS publishes the regulation deficiencies found during surveys of nursing homes.

**B_CMS_Deficiencies** - We create a dataset of all deficiencies committed by WA-based nursing homes from 2017.

### Section C (Standardizing both datasets)
Now that we have created an enforcement dataset  and a deficiencies dataset, we need to make them compatible with each other. We do that in the only script in this section:

**C_Standardizing_Both_DFs** — We clean the data scraped from the enforcement letters, making it consistent with the originally-structured CMS deficiency data.

### Section D (Analysis)
Now that we have produced a series of structured datasets, we put them together and query the sanity out of them to see what we can find.

**D_Analysis_1_General** — General, forest-view analysis.
**D_Analysis_2_Infection_Control** — Analysis focused on infection control deficiencies and penalizations.
**D_Analysis_3_Staffing** — Analysis focused on staffing deficiencies and penalizations.

# Sources

## Federal data
Nursing Home Compare datasets
- [CMS - Nursing home health deficiencies (Socrata)](https://data.medicare.gov/Nursing-Home-Compare/Health-Deficiencies/r5ix-sfxw/)
- [CMS - Nursing home health deficiencies with full statements - Updated in August 2020 (Zipped XLSXs)](http://downloads.cms.gov/files/Full-Statement-of-Deficiencies-August-2020.zip)
- [CMS - F-Tag Crosswalk (XLSX)](https://www.cms.gov/Medicare/Provider-Enrollment-and-Certification/GuidanceforLawsAndRegulations/Downloads/F-Tag-Crosswalk.xlsx)


## Federal documentation
- [CMS - Guidance to nursing homes laws and regulations](https://www.cms.gov/Medicare/Provider-Enrollment-and-Certification/GuidanceforLawsAndRegulations/Nursing-Homes)
- [CMS - List of revised F-tags (PDF)](https://www.cms.gov/Medicare/Provider-Enrollment-and-Certification/GuidanceforLawsAndRegulations/Downloads/List-of-Revised-FTags.pdf)
- [CMS - Design for Nursing Home Compare Five-Star Quality Rating System: Technical Users’ Guide (PDF)](https://www.cms.gov/Medicare/Provider-Enrollment-and-Certification/CertificationandComplianc/downloads/usersguide.pdf)

## State data
There are literally thousands of PDF reports available at the [DSHS Nursing Home Facilities Locator](https://fortress.wa.gov/dshs/adsaapps/lookup/NHPubLookup.aspx). The tool generates a CSV file that contains metadata about the reports, including the URLs where they can be downloaded from. This is how we located and downloaded more than 1,000 enforcement letter reports.
