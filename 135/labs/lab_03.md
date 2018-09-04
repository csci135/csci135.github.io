
<title>CSCI 136 - Lab 3</title>

# Lab 3. File I/O and Data Processing


![\[Ashokan Reservoir Illustration\]](https://i.imgur.com/u51pFWF.jpg){: .centered}

## Ashokan Reservoir

Located in Ulster County, about 13 miles west of Kingston and 73 miles north of New York City. 
Formed by the damming of the Esopus Creek, which eventually flows northeast and drains into the Hudson River. 
Consisting of **two basins** separated by a concrete dividing weir and roadway, it **holds 122.9 billion gallons** 
at full capacity and was placed into service in 1915.

[The Ashokan](http://www.nyc.gov/html/dep/html/watershed_protection/ashokan.shtml) is one of two reservoirs in the City's Catskill Water Supply System. 
The other is the Schoharie, located 27 miles to the north, whose water flows into the Ashokan via the Shandaken Tunnel and the Esopus Creek. 
Including the water it receives from the Schoharie Reservoir, the Ashokan supplies about **40% of New York City’s daily drinking water** needs in non-drought periods.

## Data on Ashokan water levels
In this lab, we will be studying the Ashokan water levels for the year 2016. It is available from *NYC Open Data*. 
Please follow these instructions to download the dataset:

1. Follow the link NYC Open Data [Current Reservoir Levels](https://data.cityofnewyork.us/Environment/Current-Reservoir-Levels/zkky-n5j3).
1. Choose the **View Data** menu option (it will reload the page).
  ![\[Illustration\]](https://i.imgur.com/bj2uhhb.png){: .centered}
1. Disable unnecessary columns. To do that:
    - Click the brown button **Manage** 
    - In the submenu **Show & Hide Columns**, unselect all columns except the first five
    - Click **Apply**
  ![\[Illustration\]](https://i.imgur.com/gSqFRIY.png){: .centered}
1. Filter data including only the year 2016. To do that:
    - Click the blue button **Filter**
    - In its submenu **Filter**, click **Add a New Filter Condition**
    - Choose **Date is between** `January 1, 2016` and `December 31, 2016`
  ![\[Illustration\]](https://i.imgur.com/i68wEOm.png){: .centered}
1. Sort entries *by Date* in the *Ascending* order.
1. Export the data:
    - Click the light blue button **Export**
    - Choose **TSV for Excel** (it will produce a plain text data file in *tab-separated values* format).
    - Save obtained file `Current_Reservoir_Levels.tsv` on your hard drive.

## Data format

**Don't open the datafile in Excel (that can mess up its formatting)**. Instead, you can open it with your text editor (gedit).

The datafile is a plain text file whose first line is a header followed by rows of data.
The entries in each row are separated by the *tab* symbol, hence the name of the file format: *TSV* (tab-separated-values).
It is the most convenient format for reading by our C++ program.

Each row has five fields: *Date*, *Storage* (in billions of gallons) and *Elevation* (in feet) for the East basin and for the West basin of the reservoir:

<pre style="font-size:80%;">
Date             EastStorage    EastElevation    WestStorage    WestElevation
01/01/2016          71.99           581.8           39.87           582.48
01/02/2016          71.97           581.79          39.82           582.44
01/03/2016          71.93           581.76          39.76           582.36
01/04/2016          71.93           581.76          39.67           582.27
...
</pre>

To read the datafile, we have to open an **input file stream** (represented by an object of type `ifstream`, here we called it `fin`):

```c++
ifstream fin("Current_Reservoir_Levels.tsv");
if (fin.fail()) {
    cerr << "File cannot be opened for reading." << endl;
    exit(1); // exit if failed to open the file
}
```

Remember that the first line in the file is a header line. We have to skip it before we get to process the actual data. 
We can do that by reading that line into a temporary variable that we can call `junk`:
```c++
string junk;        // new string variable
getline(fin, junk); // read one line from the file 
```
After that, the file can be read line by line. The most idiomatic C++ way to read such well-formatted file until the end would be the following:

```c++
while(fin >> date >> eastSt >> eastEl >> westSt >> westEl) { 
    // this loop reads the file line-by-line
    // extracting 5 values on each iteration 

    // for example, to print the date and East basin storage:
    cout << date << " " << eastSt << endl;
}
```
Here, variable `date` can be of type `string`, and the others are numeric variables of type `double`
extracting the storage and elevation in East and West basins.

After you are done reading the file, close the stream:
```c++
fin.close();
```
### Need to include additional header files
The above code is using a new function `exit` and a stream class `ifstream`. To make them work,
we have to include two new **headers** at the beginning of the program:

```c++
#include <cstdlib>
#include <fstream>
```

## Task A

Write a program `east-storage.cpp` that asks the user to input a **string** representing the date (in MM/DD/YYYY format), 
and prints out the **East basin storage** on that day.

### Example
```
$ ./east-storage
Enter date: 05/20/2016
East basin storage: 79.88 billion gallons
```

## Task B. Minimum and maximum storage in 2016

Write a program `minmax.cpp` that finds the minimum and maximum storage in East basin in 2016.

### Example (using made up numbers):
```
$ ./minmax
minimum storage in East basin: 61.13 billion gallons
MAXimum storage in East basin: 69.22 billion gallons
```

### Hint: 
The program should read the file line by line, while keeping track of what is the highest and the lowest storage level in the basin so far.
In the end, after reading the entire file, the found values will be the minimum and the maximum storage levels for the entire year.

## Task C. Comparing elevations

Write a program `compare.cpp` that asks the user to input two dates (the beginning and the end of the interval).
The program should check each day in the interval and report **which basin had higher elevation** on that day
by printing "**East**" or "**West**", or print "**Equal**" if both basins are at the same level.

### Example:
```
$ ./compare
Enter starting date: 09/13/2016
Enter ending date: 09/17/2016

09/13/2016 East
09/14/2016 East
09/15/2016 Equal
09/16/2016 West
09/17/2016 West
```

### Explanation:

<style type="text/css">
table{border-collapse: collapse;}
th{ padding-left: 2em; padding-right:2em; border-bottom:1px solid #888; }
td{ padding-left: 2em; padding-right:2em }
</style>

Date       |  East (ft) | West (ft)  |
-----------|------------|------------|------------
09/13/2016 | **576.28** |   575.93   |  East is higher
09/14/2016 | **576.06** |   575.79   |  East is higher
09/15/2016 | **575.75** | **575.75** |  Equal elevation
09/16/2016 |   575.51	  | **575.61** |  West is higher
09/17/2016 |   575.32   | **575.4**  |  West is higher

## Task D. Reverse chronological order

![\[Turn Back Time Illustration\]](https://i.imgur.com/EBCioqG.jpg){: .centered}

Write a program `reverse-order.cpp` which asks the user to input two dates (earlier date then later date).
The program should report the **West basin elevation** for all days in the interval in the reverse chronological order (from the later date to the earlier).

### Example:
```
$ ./reverse-order
Enter earlier date: 05/29/2016
Enter later date: 06/02/2016

06/02/2016  587.66 ft
06/01/2016  587.81 ft
05/31/2016  587.93 ft
05/30/2016  588.02 ft
05/29/2016  588.17 ft
```

**Hint:** If for the previous tasks you did not use arrays, here you really have to read the data into arrays first, and only then report them in the 
required order.

<br />

