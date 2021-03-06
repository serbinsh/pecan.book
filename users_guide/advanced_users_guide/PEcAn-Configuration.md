The PEcAn system is configured using a xml file, often called settings.xml. The configuration file can be split in X seperate pieces:

1. [PEcAn Folders](#pecan_folders)
2. [Database Access](#database_access)
3. [BETY Configuration](#bety_database)
4. [Brown Dog](#brown_dog)
5. [PFT Selection](#pft_selection)
6. [Meta Analysis](#meta_analysis)
7. [Ensemble Runs](#ensemble_runs)
8. [Sensitivity Runs](#sensitivity_runs)
9. [Model Setup](#model_setup)
10. [Run Setup](#run_setup)
11. [State Data Assimilation](#sda_setup)

<a name="pecan_folders" />

## PEcAn folders

The following are the tags that can be used to configure the folders used by PEcAn. All of these are optional.

```{xml}
<outdir>/home/carya/testrun.pecan</outdir>
```

* **outdir** : [optional] specifies where PEcAn will write all outputs and create folders. If this is not specified the folder pecan in the current folder will be used.

<a name="database_access" />
## Database Access

The connection to the BETY database is configured using this section. In this section you will specify what driver to use to connect to the database (PostgreSQL by default) and the connection parameters to connect to the database. This section is very picky and will only accept parameters that are passed into the connection function for each database driver, any other entries will result in an error by the database driver.

### functions: 

`db.open()`, and thus by `db.query()` and `db.check()`.

New format (starting with PEcAn 1.3.6)
```
<database>
	<bety>
		<dbname>bety</dbname>
		<username>bety</username>
		<password>bety</password>
		<host>localhost</host>
		<write>true</write>
	</bety>
	<fia>
		<dbname>fia5data</dbname>
		<username>bety</username>
		<password>bety</password>
		<host>localhost</host>
	</fia>
</database>
```

The connection information under bety will be used by most of the PEcAn system, only the extraction of pss/css/site information from the FIA database will use the connection information in the fia section.

* **driver** : [optional] the driver to use to connect to the database. Default value is PostgreSQL  
* **dbname** : [optional] the name of the database (was name), default value is the username of the current user logged in.  
* **user** : [optional] the username to connect to the database (was userid), default value is the username of the current user logged in (PostgreSQL uses user for this field).
* **password** : [optional] the password to connect to the database (was passwd), if not specified no password is used.  
* **host** : [optional] the name of the host to connect to, default value is localhost.  
* **write** : [optional] should any results be written back to the database, setting this to TRUE (the default) will write any results back into the database. (since PEcAn 1.3.6)

For other database drivers these parameters will change. See the driver documentation in R for the right parameters.

<a name="bety_database" />
## BETY Database Configuration

This section describes how to connect to the BETY Database. This section is used for versions of PEcAn prior to version 1.3.6, starting at 1.3.6 this parameter is part of &lt;database&gt;&lt;bety&gt; and defaults to TRUE.

### functions: 

`db.check()`, `write.configs()`, `run.models()`

```
<bety>
	<write>TRUE</write>
</bety>
```

* **write** : [optional] this can be TRUE/FALSE (the default is TRUE). If set to TRUE, runs, ensembles and workflows are written to the database.

<a name="brown_dog" />
## Brown Dog Configuration

This section describes how to connect to [Brown Dog](http://browndog.ncsa.illinois.edu). This will allow for conversions of data (currently only met data).

### functions: 

`met.process()`

```
<browndog>
	<url>...</url>
	<username>...</username>
	<password>...</password>
</browndog>
```

* **url** : [required] endpoint for Brown Dog to be used.
* **username** : [optional] username to be used with the endpoint for Brown Dog.
* **password** : [optional] password to be used with the endpoint for Brown Dog.

<a name="pft_selection" />
## PFT Selection

The PEcAn system requires at least 1 PFT (Plant Functional Type) to be specified inside the `<pfts>` section. 

### functions:  

`get.traits()`


### PFT tags


```
<pfts>
	<pft>
		<name>sipnet.temperate.coniferous</name>
		<outdir>/home/carya/testrun.pecan/pft/1/</outdir>
		<constants>
			<num>1</num>
		</constants>
	</pft>
</pfts>
```

* **name** : [required] the pft as is found inside the BETY database, this needs to be an exact match.  
* **outdir**: [optional] path in which pft-specific output will be placed during meta-analysis and sensitivity analysis. If not specified it will be written into `<outdir>/<pftname>`.  
* **contants**: [optional] this section contains information that will be written directly into the model specific configuration files. PEcAn does not look at the information in this section.  

<a name="meta_analysis" />
## Meta Analysis

### meta analysis functions:

`run.meta.analysis()`

### meta analysis tags

```
<meta.analysis>
	<iter>1000</iter>
	<random.effects>FALSE</random.effects>
	<update>FALSE</update>
	<threshold>1.2</threshold>
</meta.analysis>
```

The section meta.analysis needs to exists for a meta.analysis to be executed, even though all tags inside are optional.

* **iter** : [optional] [MCMC](http:/en.wikipedia.org/wiki/Markov_chain_Monte_Carlo) (Markov Chain Monte Carlo) chain length, i.e. the total number of posterior samples in the meta-analysis, default is 3000. Smaller numbers will run faster but produce larger errors.  
* **random.effects** : [optional] Whether to include random effects (site, treatment) in meta-analysis model. Can be set to FALSE to work around convergence problems caused by an over parameterized model (e.g. too many sites, not enough data). The default value is TRUE.
* **update** : [optional] Should previous results of meta.analysis and get.traits be re-used. If set to TRUE the meta-analysis and get.trait.data will always be executed. Setting this to FALSE will try and reuse existing results. Future versions will allow for AUTO as well which will try and reuse if the PFT/traits have not changed. The default value is FALSE.
* **threshold** threshold for Gelman-Rubin convergence diagnostic (MGPRF); default is 1.2
<a name="ensemble_runs" />
## Ensemble Runs

Only if this section is defined an ensemble analysis is done.

### ensemble functions: 

`write.configs()`, `run.ensemble.analysis()`

### ensemble tags

```
<ensemble>
	<size>5</size>
  <variable>GPP</variable>
  <variable>NPP</variable>
</ensemble>
```

* **size** : [required] the number of runs in the ensemble.
* **variable**: [optional] name of one (or more) variables the analysis should be run for. If not specified, sensitivity.analysis variable is used, otherwise default is GPP.

Note: if the ensemble size is set to 1, PEcAn will select the **posterior median** parameter values rather than taking a single random draw from the posterior

<a name="sensitivity_runs" />
## Sensitivity Runs

Only if this section is defined a sensitivity analysis is done. This section will have `<quantile>` or `<sigma>` nodes. If neither are given, the default is to use the median +/- [1 2 3] x sigma (e.g. the 0.00135 0.0228 0.159 0.5 0.841 0.977 0.999 quantiles); If the 0.5 (median) quantile is omitted, it will be added in the code.

### functions: 

`write.configs()`, `run.sensitivity.analysis()`

### sensitivity analysis tags

```
<sensitivity.analysis>
	<quantiles>
		<quantile></quantile>
		<sigma>-3</sigma>
		<sigma>-2</sigma>
		<sigma>-1</sigma>
		<sigma>1</sigma>
		<sigma>2</sigma>
		<sigma>3</sigma>
	</quantiles>
  <variable>GPP</variable>
	<start.year>2004</start.year>
	<end.year>2006</end.year>
</sensitivity.analysis>
```

* **quantiles** : [optional] Quantiles of parameter distributions at which the model should be evaluated when running sensitivity analysis. Values greater than 0 and less than 1 can be used.  
* **sigma** : [optional] Any real number can be used to indicate the quantiles to be used in units of normal probability.  
* **quantile** : [optional] Which quantile should be used.  
* **start.date** : [required?] start date of the sensitivity analysis (in YYYY/MM/DD format) 
* **end.date** : [required?] end date of the sensitivity analysis (in YYYY/MM/DD format)
* **_NOTE:_** start.date and end.date are distinct from values set in the run tag because this analysis can be done over a subset of the run.
* ** variable** : [optional] name of one (or more) variables the analysis should be run for. If not specified, sensitivity.analysis variable is used, otherwise default is GPP.

<a name="model_setup" />
## Model Setup

This section is required and tells PEcAn what model to run. This section should either specify `<id>` or both `<name>` and `<binary>`  of the model. If both id and name and/or binary are specified the id is used to check the specified name and/or binary.

As of version 1.3.7 the `<name>` tag has been renamed `<type>`. The `<type>` tag refers to the "type" of model and is used for a) identifying appropriate pfts and b) identifying whether appropriate inputs (i.e. met data) are available for the given site and model (or if they can be created). 

To ensure compatability, the code will automatically convert from `<name>` to `<type>`.

### functions: 

`write.configs()`, `run.models()`

### model tags

```
<model>
	<id>7</id>
	<type>ED2</type>
	<binary>/usr/local/bin/ed2.r82</binary>
	<job.sh>module load hdf5</job.sh>
	<config.header>
		<!--...xml code passed directly to config file...-->
	</config.header>
</model>
```

* **id** : [optional/required] id in the models database table, see above.  
* **name** : **OBSOLETE** name of the model, use type from version 1.3.7
* **type** : [optional/required] type of model, see above.
* **binary** : [optional/required] path to the model executable, see above.  
* **job.sh** : [optional] additional options to add to the job.sh at the top  
* **config.headers** : [optional] XML that will appear at the start of generated config files.

### ED2 specific tags

Following variables are ED specific and are used in the [ED2 Configuration](ED2-Configuration).

Starting at 1.3.7 the tags for inputs have moved to `<run><inputs>`. This includes, veg, soil, psscss, inputs.

```
	<edin>/home/carya/runs/PEcAn_4/ED2IN.template</edin>
	<config.header>
		<radiation>
			<lai_min>0.01</lai_min>
		</radiation>
		<ed_misc>
			<output_month>12</output_month>      
		</ed_misc> 
	</config.header>
	<phenol.scheme>0</phenol.scheme>
```

  
* **edin** : [required] template used to write ED2IN file
* **veg** : **OBSOLETE** [required] location of VEG database, now part of `<run><inputs>
* **soil** : **OBSOLETE** [required] location of soild database, now part of `<run><inputs>
* **psscss** : **OBSOLETE** [required] location of site inforation, now part of `<run><inputs>`. Should be specified as `<pss>`, `<css>` and `<site>`.
* **inputs** : **OBSOLETE** [required] location of additional input files (e.g. data assimilation data), now part of `<run><inputs>`. Should be specified as `<lu>' and `<thsums>`.

<a name="run_setup" />
## Run Setup

### tags

```
<run>
	<jobtemplate>/home/carya/path/to/template</jobtemplate>
	<start.date>2002-01-01 00:00:00</start.date>
	<end.date>2005-12-31 00:00:00</end.date>
	</dbfiles>/home/carya/.pecan/dbfiles</dbfiles>
	<site>
		<id>772</id>
		<name>Niwot Ridge Forest/LTER NWT1 (US-NR1)</name>
		<lat>40.032900</lat>
		<lon>-105.546000</lon>
	</site>
	<inputs>
       <met>
             <id>10000000001</id>
             <path>/fs/data1/pecan.data/input/</path>
             <source>Ameriflux</source>
        </met>
	<inputs>
	<host>
		<name>localhost</name>
		<rundir>/home/carya/testrun.pecan/run/</rundir>
		<outdir>/home/carya/testrun.pecan/out/</outdir>
		<scratchdir>/tmp/carya</scratchdir>
		<clearscratch>TRUE</clearscratch>
		<qsub>qsub -N @NAME@ -o @STDOUT@ -e @STDERR@ -S /bin/bash</qsub>
		<qsub.jobid>Your job ([0-9]+) .*</qsub.jobid>
		<qstat>qstat -j @JOBID@ &> /dev/null || echo DONE</qstat>
		<job.sh>module load udunits R/R-3.0.0_gnu-4.4.6</job.sh>
	</host>
</run>
```

* **jobtemplate** : [optional] the template used when creating a job.sh file which is used to launch the actual model. Each model has it's own [template](https://github.com/PecanProject/pecan/blob/master/models/ed/inst/template.job) in the inst folder of the module. The following variables can e used: @SITE_LAT@, @SITE_LON@, @SITE_MET@, @START_DATE@, @END_DATE@, @OUTDIR@, @RUNDIR@ which all come variables in the pecan.xml file. The following two command can be used to copy and clean the results from a scratch folder (specified as scratch in the run section below, for example local disk vs network disk) : @SCRATCH_COPY@, @SCRATCH_CLEAR@ .
* **start.date** : [required] the first day of the simulation  
* **end.date** : [required] the last day of the simulation
* **dbfiles** : [optional] location where pecan should write files that will be stored in the database. The default is store them in ${HOME}/.pecan/dbfiles

Site specific information is specified in the `<site>` subsection. Either `<id>` or `<name>`, `<lat>` and `<lon>` should be specified. If id and any of the others are specified the values will be compared with those from BETYdb.
 
* **id** : [optional/required] id of the site in the BETY database, see above.  
* **name** : [optional/required] site name, see above.  
* **lat** : [optional/required] site latitude, see above.  
* **lon** : [optional/required] site longitude, see above.  

Inputs specific information for each model type is specified in the `<inputs>` subsection. 
Each input will have three tags:
* **id** :  [optional/required] An id that points to the input if already in the database.
* **path** : [optional/required] The path to the dataset if already downloaded.
* **source** : [optional/required] The input data type. This tag name needs to match the names in the conversion functions. 
In general, the path should be filled in by PEcAn and not by the user.
If PEcAn is processing all the input, id is not required. Alternatively, if you're using data processed by hand, source is not required. 


One common input will be the weather data, often specified in `<met>`.

Other model types will have different model specific inputs.  PEcAn will find the location of the file on the host you run PEcAn on.

* **met** : [model type specific] most models will have a met tag to specify location of the weather data.
* **pss** : [ED2, required] location of patch file
* **css** : [ED2, required] location of cohort file
* **site** : [ED2, optional] location of site file
* **lu** : [ED2, required] location of land use file
* **thsums** : [ED2, required] location of thermal sums file
* **veg** : [ED2, required] location of vegetation data
* **soil** : [ED2, required] location of soil data

Host on which the simulation will run is specified in the `<host>` subsection. If this section is not specified it is assumed the simulation will run on localhost. If qsub is specified (can be empty in which case the defaults will be used) the models will be executed using qsub.

* **name** : [optional] name of host server where model is located and executed, if not specified localhost is assumed.  
* **rundir** : [optional/required] location where all the configuration files are written. For localhost this is optional (`<outdir>/run` is the default), for any other host this is required.  
* **outdir** : [optional/required] location where all the outputs of the model are written. For localhost this is optional (`<outdir>/out` is the default), for any other host this is required.
* **scratchdir** : [optional] location where output is written. If specified the output from the model is written to this folder and copied to the outdir when the model is finished, this could significantly speed up the model execution (by using local or ram disk).
* **clearscratch** : [optional] if set to TRUE the scratchfolder is cleaned up after copying the results to the outdir, otherwise the folder will be left. The default is to clean up after copying.
* **qsub** : [optional] the command to submit a job to the queuing system. There are 3 parameters you can use when specifying the qsub command, you can add additional values for your specific setup (for example -l walltime to specify the walltime, etc). You can specify @NAME@ the pretty name, @STDOUT@ where to write stdout and @STDERR@, where to write stderr. You can specify an empty element (<qsub/>) in which case it will use the default value is "qsub -V -N @NAME@ -o @STDOUT@ -e @STDERR@ -s /bin/bash".
* **qsub.jobid** : [optional] the regular expression used to find the jobid returned from qsub. If not specified (and qsub is) it will use the default value is "Your job ([0-9]+) .*"
* **qstat** : [optional] the command to execute to check if a job is finished, this should return DONE if the job is finished. There is one parameter this command should take @JOBID@ which is the id of the job as returned by qsub.jobid. If not specified (and qsub is) it will use the default value is "qstat -j @JOBID@ || echo DONE"
* **job.sh** : [optional] additional options to add to the job.sh at the top  

<a name="sda_setup" />

### State Data Assimilation Tags

The following tags can be used for state data assimilation. More detailed information can be found here: [State Data Assimilation Documentation](sda.documentation.md)

```
<state.data.assimilation>
	<process.variance>FALSE</process.variance>
  <sample.parameters>FALSE</sample.parameters>
  <state.variables>
   <variable>AGB.pft</variable>
   <variable>TotSoilCarb</variable>
  </state.variables>
  <spin.up>
  	<start.date>2004/01/01</start.date>
	  <end.date>2006/12/31</end.date>
  </spin.up>
  <forecast.time.step>1</forecast.time.step>
	<start.date>2004/01/01</start.date>
	<end.date>2006/12/31</end.date>
</state.data.assimilation>
```

* **process.variance** : [optional] TRUE/FLASE flag for if process variance should be estimated (TRUE) or not (FALSE). If TRUE, a generalized ensemble filter will be used. If FALSE, an ensemble Kalman filter will be used. Default is FALSE.
* **sample.parameters** : [optional] TRUE/FLASE flag for if parameters should be sampled for each ensemble member or not. This allows for more spread in the intial conditions of the forecast.
* **_NOTE:_** If TRUE, you must also assign a vector of trait names to pick.trait.params within the sda.enkf function.
* **state.variable** : [required] State variable that is to be assimilated (in PEcAn standard format). Default is "AGB" - Above Ground Biomass.
* **spin.up** : [required] start.date and end.date for model spin up.
* **_NOTE:_** start.date and end.date are distinct from values set in the run tag because spin up can be done over a subset of the run.
* **forecast.time.step** : [optional] start.date and end.date for model spin up.
* **start.date** : [required?] start date of the state data assimilation (in YYYY/MM/DD format) 
* **end.date** : [required?] end date of the state data assimilation (in YYYY/MM/DD format)
* **_NOTE:_** start.date and end.date are distinct from values set in the run tag because this analysis can be done over a subset of the run.

