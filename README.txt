-------
SUMMARY
-------
The program is a demonstration of decision tree mining for classification.
The implementation is a multi-class classification.(Varies the log base based on no.of target classes in entropy calculation)
The additional part also uses the generated tree to predict class and test the implementation.

---------------
BUILD & EXECUTE
---------------
The program has been developed in C.
The code development linux environment running CentOS Linux release 7.2.1511.
gcc version 4.8.5 was used to compile.
To compile the source code, run the make command, which will produce the executable Digger using the targets mentioned in the makefile.
After compiling the application can be executed by runnning the below, 
$./Digger

Note: Noise indicator is predefined in differ.h file. Defualt is ?, change it if required.

--------------
INPUT & OUTPUT
--------------
Interactive command line inputs are required.
The program takes three mandatory inputs and one optional input,
	File Name -	Name of the file containing the data for mining. 
			The input can also be prefixed by path if the file is in a different location than that of execution.
			The columns are space separated.
			First line should be column header.
			Empty columns should be indicated by the predifined value set as Macro in the code. Default value is ?
	Target -	The attribute or column to be classified.
			The list of attributes is displayed in a numbered list, out of which one has to be chosen by entering the number.
	Test Option -	Checks if user wishes to test the classifer by applying prediction.
			Input of 1 is considered as acceptance to predict, anything else is decline.
	Test File -	Name of the file containing the test data set.
			The input can also be prefixed by path if the file is in a different location than that of execution.
			The columns are space separated.
			First line should be column header.
			The data format should be same as that of the training data set.

Training & testing output is printed in the screen along with the performance statistics.
The outputs are also stored in files name Rules and Test respectively.

Train Sample input:
Enter the input file name:data1
Please choose an attribute (by number):
        1. outlook
        2. temperature
        3. Humidity
        4. Windy
        5. PlayTennis
Target:5

Testing Sample input:
Enter 1 to test or any other key to exit:1
PREDICTION
----------
***Input file should follow the same structure of training data and should not have new values in dataset***
Enter the input file name:test_data1

-----------
PERFORMANCE
-----------
The execution time for the application is measured accross three different tasks loading input, decision tree mining and prediction.
The decision tree mining includes time to print the output and noise rectification.
The prediction includes time to read and write the output.
Given below is the statistics for the 3 sample inputs using same file as training and testing dataset.

Time provided below is in milli seconds.
	|Target		|Loading	|Training	|Testing
-------------------------------------------------------------------
data1	|PlayTennis	|0.394		|2.644		|4.430
data3	|irradiat	|0.769		|4.256		|7.133
adult2	|wage		|0.700		|4.061		|7.544

The statistics shows that execution time is fairly faster with a all tasks completing in lot less than 10 milli second.
Combining noise rectification and print has reduced the amount loops reuired and efficiently increasing the performace.
Even in in this, considerable time has been consumed in IO & memory allocate operation, which can be reduced further by developing ways to perform those in bulk.

-----------
ASSUMPTIONS
-----------
Field should not have space in between.
Input file should not have empty columns, instead indicated by the predifined value set as Macro in the code. Default value is ?
Test dataset should follow the same structure as that of training dataset.

--------------
DATA STRUCTURE
--------------
Tree style data structure is used to build the data.
The tree has root and can have maximum levels equal to that of the number of columns in the input.
Each node indicating a attribute to be applied for decision. 
There can be multiple branches from a node. Maximum branches equal to that of number of uniques values in attribute.
Each branch indicates a value attribute.
A node and dynamicaly set number of branches is created a structure.
The tree is built dynamicaly using recursion on structure, which forms the only fundamental building block of the tree.
The recursive use of data structure helps developing a single traversal algorithm and apply it to navigate along the whole tree.

---------------
MODULES & TASKS
---------------
The code contain the below files,
	digger.h - Structure, Global variable and Function declaration.
	digger.c - main() program.
	load.c - Reads data from input and forms the training dataset.
	plant.c - Form decision tree. Also print the tree and rectify noise.
	predict.c - Reads data from input and forms the testing dataset.
	navigate.c - Tree traversal.

Given below the flow of control between modules,

DIGGER:main()
  |
  |
LOAD-->load()			#file name & target input
  |      |
  |	 |
  |    load_headers()		#read first line from file. gathers info such as number of columns and column names.
  |      |
  |	 |
  |    load_records()		#read all records. gather info such as number records.
  |
  |
PLANT-->seed()			#Setup values to be passed to form the root, record set and available columns to decide.
  |	  |
  |	  |
  |	grow()<-		#rectify noise. identify unique values/items. calculate item  & columns total gain. 
  |	  |    |		#find column with max gain. set the column as node and its items as branch.
  |	  |    ^		#recurse by calling same function on every branch to build tree further.
  |	  ->---|		#handle uncertaininty in end leaf.
  |
  |
TEST-->predict()		#read test dataset. call navigation from root for every record. print predicted output.
  |	  |			#calculate accuracy.
  |	  |
  |	  |
  |    navigate()<-		#based on the record value for the node's attribute, validate the branch to move forward.
  |	  |	  |		#recursive call, to navigate from identified branch.
  |	  |	  ^		#on reaching certain end leaf node, set it as prediction.
  |	  ->------|		#on reaching uncertain end leaf node return back to parent and max target of parent is set as prediction
  |
  |	
RELEASE-->free_mem()		#free all the dynamic allocated memory for the tree and dataset storing.

