To upload model driver data [or any other model input data or data used for model calibration/validation]...

From your BETY interface:

* Create a DBFILES entry for the path to the file
 * From the menu click RUNS then FILES
 * Click “New File”
 * Select the machine your file is located at
 * Fill in the File Path where your file is located (aka folder or directory) NOT including the name of the file itself
 * Fill in the File Name with the name of the file itself. Note that some types of input records will refer to be ALL the files in a directory and thus File Name can be blank
 * Click Update
* Create an INPUT entry for your data
 * From the menu click RUNS then INPUTS
 * Click “New Input”
 * Select the SITE that this data is associated with the input data set
 * Other required fields are a unique name for the input, the start and end dates of the data set, and the format of the data. If the data is not in a currently known format you will need to create a NEW FORMAT and possibly a [[new Input Converter| Adding-an-Input-Converter]]
 * Parent ID is an optional variable to indicated that one dataset was derived from another.
 * Click “Create”
* Associate the DBFILE with the INPUT
 * In the RUNS -> INPUTS table, search and find the input record you just created
 * Click on the EDIT icon
 * Select “View related Files”
 * In the Search window, search for the DBFILE you just created
 * Once you have found the DBFILE, click on the “+” icon to add the file
 * Click on “Update” at the bottom when you are done.
