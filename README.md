# Sqlite3-Data-Accumulation
Detailing DataFile class created for zygo data manipulation

General Flow:
First we initialize our database, details under initDatabase.
The main function is designed to parse (using os.walk) a directory path.
Then for each file in the path it checks to see if the file should be in the database (endswith('.xyz')
then checks the database for the filename.
If the filename is found it moves to the next item, if the filename is not found an instance of 
the DataFile class is created, this gathers all data needed and the database is updated with 
the new value.
After all files have been checked we query the Database and print it out to a csv file.

Global Variables:
db_path - sqlite database location.
main_dir - What folder to parse files in.
csvPath - Location of output file.
csvHeader - Column headers used in output file.

Functions:

initDatabase:
No parameters passed in.
Requires db_path
The database consists of one primary table and four ancilary tables
Verifies .sqlite file exists or writes the .sqlite file with schema.

isValid:
Parameter to pass in: file and root variables as returned from os.walk()
If the file extension is ".xyz" and the folder is not named "New folder"
returns True

checkDatabase:
Parameter to pass in: absolute file path as string.
Requires db_path and existing database at that path.
This function checks the database for the passed in entry.
Returns True if the entry is found, otherwise returns False.

writeCsvfromDb:
No parameters passed in.
Requires csvHeader, db_path, an existing database at that path.
Includes an sql query to gather all of the data in the database joining the correct tables together.
writes the csvHeader then all rows from the sql query into a csv file, named in csvPath global variable.
Also written to the csv file is a contained file count, total file count in the directory and % complete.
This will write out a new file with all data from zygoPull.sqlite

getFileTotal:
No parameters passed in.
Requires main_dir
Using os.walk this function goes through all of the files and folders in the given directory and counts
the ones that end in .xyz
Returns Total .xyz files in the given directory as int()

Class methods include:

getEnumStepSource:
Parameter to pass in: filename, ending with ".xyz" and not in the database.
Return values: EpiNumber, Step, SourceName. To be assigned to the associated class variables.
Using a combination of regex patterns and default values this method will parse the filename, pulling out data to build our database.

getFolderProductEwo:
Parameter to pass in: root, returned from os.walk()
Return values: Folder, Product, EWO. To be assigned to the associated class variables.
Using a combination of regex patterns and default values this method will parse the foldername, pulling out data to build our database.

getOsPathData:
Parameter to pass in: file, root. as returned by os.walk
Return value: N/A, this method sets class variables.
Using getEnumStepSource and getFolderProductEwo we assign six our our class variables.
Using zygo code we set another 4 internal variables intended to later write to the database

updateDatabase:
Parameter passed in: self.
Requires self.db_path and getOsPathData to have been run.
Creates a single database entry with the gathered data

Main:
The main loop of our program initializes the database then loops through all files and folders in the main_dir.
For each file we validate the filename and check if it exists in the database, if the filename is valid but doesn't exist 
in the database it initialize the DataFile class, collects the ospath/zygo data, updates the database.
When the database has been fully updated it writes a fresh csv file from the entire contents of the database.
