# Does my study site already exist in PEcAn?

1. **Find the site on the map** The simplest way of determining if a site exists in PEcAn is through the Google Map interface of the web-based workflow. You'll want to make sure that the Host is set to All Sites and the Model is set to All Models. 

2. **Find the site in BETY** If the site is not on the map there's a chance that it still in PEcAn but just lacking geographic information. To begin you'll want to login to your local version of the BETY database. If you're on the PEcAn VM this will be at localhost:3280/bety or localhost:6480/bety depending on whether you downloaded the 32 or 64 bit version.  Within BETY navigate to Data > Sites and use the Search window on the page to try and locate your site. If you **DO** find your site you will want to click Edit and add geographic information so that the site will show up on the map. It is also worth noting that the site ID number shows up in the URL for the Show or Edit pages. This ID is frequently useful to know, for example if you have to set up a PEcAn settings file by hand. If you did not find you site you will want to follow the instructions below for adding a site

# Adding a new site

1. To add a new site to PEcAn you currently have to begin by logging in to BETY

2. Navigate to Data > Citations. Before you add a Site you will need to establish a Citation to associate with the site. If you went to Data > Sites and just clicked on **Add Site** it will take you to an error page reminding you of this.

3. Before adding a new citation, you should Search the system to make sure the Citation you are about to add doesn't already exist. If it does you should be able to just click on the check-box icon to select that citation and then skip the next step

4. To create a new citation click on the **New Citation** button, fill in the fields, and then click Create. The field URL should contain the web address that takes you to this publication on the publisher's website. The Pdf field should similarly be the web address for the pdf for this citation.  If there are no publications associated with the site you are interested in you can leave most of the fields blank, but you could add a descriptive title, such as "EBI Farm Field Data", and a relevant contact person as the Author so that future users can associate any site-specific data with this citation.

5. Once the Citation is created or selected this should automatically take you to the Sites page and list any Sites already associated with this citation. To create a new site click the **New Site** button.

6. When creating a new cite the most critical information is the **Site name** and the Lat/Lon. The Lat/Lon can be entered by hand or by clicking on the site location on the Google Map interface. It is also helpful to fill in the other location information (Cite, State, Country) and the Notes to make it easier for other users to search for the site. The topographic, climate, and soils information is optional.

7. When you are done click **Create**. At this point if you refresh the PEcAn site-level run webpage the site should automatically show up.

# Troubleshooting

### My site shows up when I don't have any model selected, but disappears once I select the model I want to run

Selecting a model will cause PEcAn to filter the available sites based on whether they possess the required Inputs for a given model (e.g. meteorology). To check what Inputs are missing for a site point your browser to the pecan/checksite.php webpage (e.g. localhost:3280/pecan/checksite.php). This page looks virtually identical to the site selection page, except that it has a *Check* button instead of *Prev* and *Next*. If you select a Machine, Model, and Site and then click *Check* the page should return a list of what Inputs are missing (listing both the name and the Format ID number). Don't forget that its possible for PEcAn to have required Inputs in its database, but just not have them for the Machine where you want to run.

To see more about what Inputs a given model can accept, and which of those are required, take a look at the MODEL_TYPE table entry in the database (e.g. go to localhost:3280/bety; Select Runs > Model Type; and then click on the model you want to run).

For information about loading missing Inputs into the database visit [How to insert new Input data](/../../How-to-insert-new-Input-data.md), and also read the rest of the pages under this section, which will provide important information about the specific classes of Inputs (e.g. meteorology, vegetation, etc).

Finally, we are in the process of developing workflows and standards for processing Input data in a model-agnostic way, starting with meteorology. So hopefully much of the work of building model driver files and uploading them into PEcAn will become automatic soon.