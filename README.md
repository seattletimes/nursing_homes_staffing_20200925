![The Seattle Times](https://github.com/seattletimes/nursing_homes_staffing_20200925/tree/master/assets/ST_Logo.png)

This repository contains the data reporting behind [story name](), a Seattle Times story by [Mary Hudetz](https://twitter.com/marymhudetz), [Asia Fields](https://twitter.com/asiakmfields) and [Manuel Villa](https://twitter.com/mvilla1859).


# Summary

As the coronavirus known as SARS-CoV-2 started spreading over the United States at the beginning of 2020, it became clear that some of the most vulnerable places to the virus were long-term care facilities. These are establishments whose role is to take care of elderly people who are not capable of fully looking after themselves. Washington state became the early epicenter of the pandemic in the United States, and long-term care facilities across the state, particularly nursing homes, witnessed a sudden spike in COVID-19 cases and deaths. 

Why were those facilities been so vulnerable to the virus? 

To find the answer, [The Seattle Times](https://www.seattletimes.com/) engaged in data-intensive investigation. Our aim was to understand what the situation was in nursing homes before the pandemic. We figured that would be reflected in inspection and enforcement reports issued by authorities that regulate those facilities.

Nursing homes are governed by state and federal regulators. At the federal level is the Centers for Medicare & Medicaid Services (CMS), the agency responsible for ensuring approximately 15,500 nursing homes nationwide meet federal quality standards to participate in the Medicare and Medicaid programs. In the state of Washington, they are under the jurisdiction of the Department of Social and Health Services (DSHS).

In retrospect, those regulations did not prevent hundreds of senior people from dying of COVID-19. Were those regulations inadequate for the extraordinary situation that became the pandemic? Were they not properly enforced? Or was it something else?

It soon became clear that not all the data we needed to explore those questions existed, at least not in a structured form. While federal data about deficiencies reported by inspectors at nursing homes was readily available, data about the penalties imposed on those facilities by Washington state was contained across thousands of pages of PDF documents. 

The sheer number of reports made it infeasible to download them manually, so we created a programming script that located and downloaded them automatically. Then we wrote another script that actually “read” them, confirmed if they contained any penalties and, if they did, extracted them. This way, we created a database that did not exist before, allowing us to analyze the data to understand what has been Washington’s response to the issues facing nursing homes.

The detailed process of how we set up to find the sources of the data is as follows:


# Obtaining the data

## Deficiency data

The state of Washington doesn't keep a database of the deficiencies recorded by ispectors in nursing homes. But CMS, the federal agency, does, and it publisehs it in two ways:

- Their [Health Deficiencies Socrata portal](https://data.medicare.gov/Nursing-Home-Compare/Health-Deficiencies/r5ix-sfxw/)
- A monthly recurring series of compressed CSV files. 

The data contained in both sources are consistent with each other, but they have slightly different information. In both of them, each record represents a particular deficiency committed by a particular facility at a particular date. But while the CSV files include a detailed description of each deficiency, the Socrata data does not, so we ended up using the CSV files instead of the Socrata portal.

The compressed CSV files are published monthly and each one contains a history of deficiencies that goes back about three years. Each time, the data from the most recent month is added and the data from the oldest month is dropped, so there is a large overlap across each CSV file.

Each of the CSV packages can be downloaded by writing the following generic URL in a browser (each package is roughly 500MB and will start downloading immediately after it is typed in the browser):

http://downloads.cms.gov/files/Full-Statement-of-Deficiencies-Month-Year.zip

For example, to obtain the package for August 2020, one would use this URL:

[http://downloads.cms.gov/files/Full-Statement-of-Deficiencies-August-2020.zip](http://downloads.cms.gov/files/Full-Statement-of-Deficiencies-August-2020.zip)

## Enforcement data

Once we obtained the history of deficiencies for each nursing home, we wanted to know what, if any, consequences those deficiencies generated.

Just as with deficiencies, CMS has a [Socrata portal for penalty data](https://data.medicare.gov/Nursing-Home-Compare/Penalties/g6vv-u9sr). However, there were two limitations with these data:
- They only correspond to penalties imposed by CMS, not by the state.
- They do not report what type of deficiency generated the penalty. 

If we wished to know what enforcement actions the state took, we would need to find that information locally. Unfortunately, the state DSHS does not provide that data in an organized and structured way such as CMS does. That information was contained across thousands of pages of PDF reports. 

Those reports are called “enforcement letters” and thousands of pages of them are regularly published online by the Aging and Long-Term Support Administration (ALTSA), a subdivision of the state DSHS. ALTSA's [Assisted Living Facilities Locator](https://fortress.wa.gov/dshs/adsaapps/lookup/bhpublookup.aspx) generates a CSV file containing the location of the enforcement letters issued to nursing homes during the previous three years. There have been over 1,000 of such letters issued since 2017 and we downloaded them all. 

Fortunately, not only were the PDF in "native" format, meaning they could be readily scraped, but their structure was highly regular. They mostly had the same sections and the same language. It seemed obvious they were generated by some standardized input system, which makes them the ideal PDF to scrape.

As a corollary, it is worth mentioning that scraping PDFs should never—emphasis on NEVER—be a data journalist’s first approach. In the ideal world, this information would have also been neatly available in a structured format, such as an API. The decision to dive into a PDF ocean should only be taken after it has been confirmed beyond doubt that is the only way to obtain the information contained in them, either because there is no structured database version of that information or, if there is, it is not possible to access it. 

Alas, in this case, the PDFs were our only choice, so we counted ourselves fortunate that they had the features that made them scrapable.

## Big Local News

Hoping to help other news organizations report on similar stories, The Seattle Times is collaborating with [Big Local News](https://biglocalnews.org/#/about), a program of Stanford University’s Journalism and Democracy Initiative that collects local data to discover the regional or national patterns that will yield stories with impact. From the Big Local News platform, journalists can obtain full-text deficiency reports for nursing homes nationwide going back ten years. The data is provided state by state CSVs, or as one national file (4.5GB).
 
To access the data, journalists can [login for free to BLN using a Google account](https://biglocalnews.org). Once registered, search the Open Projects for CMS-Nursing-Home-Full-Deficiencies.



# The repository

## Structure

The repository is organized as follows:
- **A_source_data**: As the name implies, the official source data we work on.
- **B_scripts**: All our wacky [Python](https://www.python.org/) code can be found here, organized in a series of [Jupyter](https://jupyter.org/) notebooks. A description of each script notebook can be seen in the next section of this README, as well as inside the notebooks themselves.
- **C_output_data**: Data created by the scripts, used to arrive to findings.
- **D_documents**: Explanatory and reference documents obtained from official sources.


## Scripts


**A_Enf_Letters_Step1_Download_PDFs.ipynb** - We download all the available PDF enforcement letters issued to nursing homes by the DSHS. The letters available for download are updated each month, so we ran this process a few times since March 2020.

**A_Enf_Letters_Step2_Download_WAC_Codes.ipynb** - The data we scraped from the enforcement letters include the [Washington Administrative Codes (WAC)](https://apps.leg.wa.gov/wac/default.aspx?cite=388-97) that the surveyed nursing homes were found to be out of compliance with. In this script, we add the details of each of those codes.

**A_Enf_Letters_Step3_Scraping_PDFs.ipynb** - We dive into a scraping odyssey, using [Jeremy Singer-Vine](https://github.com/jsvine)'s awe-inspiring Python package, [PDF Plumber](https://github.com/jsvine/pdfplumber).

**B_Deficiencies.ipynb** - Now that we have created a dataset of all the penalties issued by the state, we create another dataset of all deficiencies committed by WA-based nursing homes from 2017, using the federal CMS data prepared in collaboration with BLN.

**C_Standardizing_the_Datasets.ipynb** — Now that we have created deficiencies and penalties datasets, we clean them and make them compatible with each other.

**D_Analysis.ipynb** — Finally, we dive into the analysis of the datasets we created.


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


# Findings

Here is a visualization of our findings by the talented [Emily Eng](https://twitter.com/Emily_M_Eng)



![Deficiencies and Penalties](https://github.com/seattletimes/nursing_homes_staffing_20200925/tree/master/assets/nursing-home-penalties-P-c.png)




# Questions, Comments? 

We always appreciate feedback. If you learned somthing from our work or have suggestions on how to improve it, please contact us.

mvilla@seattletimes.com
[@mvilla1859](https://twitter.com/mvilla1859)


