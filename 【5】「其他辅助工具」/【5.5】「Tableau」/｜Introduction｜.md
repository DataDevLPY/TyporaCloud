##### 5.2.1 Tableau Public

One of the best Data Analysis tools according to KDnuggets is:

No.1 [Tableau Public](https://www.tableau.com/public): “Tableau democratizes visualization in an elegantly simple and intuitive tool.”
[Top 10 Data Analysis Tools for Business](https://www.kdnuggets.com/2014/06/top-10-data-analysis-tools-business.html)

The ‘Public’ in the name refers to the promotion of online sharing of visualisations (in fact the only way you can save them, see: https://public.tableau.com/s/gallery).

Which may be why there’s a free version of the software available, download and install: https://public.tableau.com/s/download

(Tableau Desktop is available for educational users if anyone is interested, and you can save your visulisations locally with it).

##### 5.2.2 A. Worksheets and Dashboards

###### Step 1. Get data ready

Download and examine the data “[Cultural-Exchange-International-Program-LA-Dept-of-Cultural-Affairs.xlsx](https://lms.monash.edu/pluginfile.php/12151406/mod_book/chapter/869365/Cultural-Exchange-International-Program-LA-Dept-of-Cultural-Affairs1.xlsx?time=1583621781904)” here:

![截屏2021-03-05 下午9.29.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004956.png?token=AWS37JL43C4VIBSO66SRK3TBTJXEI)

The data is already in “**row-oriented tables”** format. Which means, in this data file:

- Each row is an entity (or an object, or a record)
- Each column is a property of this entity (or this object, or this record)

Usually, this is the preferred format for most tools and data processing languages (like R).

However, you cannot expect all data are in this perfect format. If it is not the case, do Data wrangling!

More information about file format can be found at: [Tableau Help: Tips for Working with Your Data.](https://help.tableau.com/current/pro/desktop/en-us/data_tips.htm)

###### Step 2. Load data into Tableau

Open Tableau Public (TP), there are two ways to load the data:

- at the left top click **“Excel”** and navigate to the file you just downloaded.
- Just drag your file and drop it into Tableau Public window.

![截屏2021-03-05 下午9.31.16](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004318.png?token=AWS37JPND2TH35ABEQO4MWLBTJXEK)



###### Step 3. Data Source View

TP will try to determine your **data types** automatically, the common types are:

- \# => numbers
- Abc => text (strings)
- An earth symbol => geographic/locations

Usually this automatic process works fine, but please remember to check that the data type inferred by TP is exactly the data type you want, sometime TP will make mistakes.

The most common mistake is that TP will sometimes treat longitude and latitude numbers as #, but we usually want them be treated as geographic type.

To change the type, just click the **symbol (#, Abc, Earth symbol and etc.).**

![截屏2021-03-05 下午9.32.12](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004698.png?token=AWS37JOYO7VPFTNZVKJ2FQ3BTJXEW)

###### Step 4. Create worksheet

Go to worksheet by clicking on Sheet 1 (at bottom of display, as shown below)![截屏2021-03-05 下午9.32.29](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004154.png?token=AWS37JMNFJ23JW7LBHUXB4LBTJXFE)

to see the TP interface:

![截屏2021-03-05 下午9.32.54](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004903.png?token=AWS37JMPTTEMURW62R2BKJLBTJXE4)

Shown on the left, data has been split into

- **Dimensions;** by default, Tableau treats any field containing qualitative, categorical information as a dimension;
- **Measures;** and any field containing numeric (quantitative) information as a measure.

This modular treatment of information – that is to say, the treatment of individual data fields as independent components instead of an interdependent table – enables us to pick and choose what specific pieces of data we want to visualise against one another.

Shown in the middle, top down, are **‘Pages’ ‘Filters’** and **‘Marks’** and then ‘**Columns’** & ‘**Rowsm**’ then a ‘**Drop field here**’ table (you can drop fields on all of these middle controls):

![截屏2021-03-05 下午9.33.27](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004182.png?token=AWS37JI3CJSDBNIDIXCBWYLBTJXFM)

###### Step 5. Create Visualisations

For our first visualisation, we will create a simple horizontal bar graph measuring City/Country against the Total Award Amounts granted to the artists from said locations.

- Drag ‘City,Country’ from **Dimensions** and drop it to **Rows**
- Drag ‘Total Award Amount’ from **Measures** and drop it to **Columns**
- Maybe also try to switch Rows and Columns

![截屏2021-03-05 下午9.34.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004477.png?token=AWS37JIASX4OS7RSAROTK7TBTJXFS)

###### Step 6

Try sorting by using the control attached to ‘‘Total Award Amount’ on the axis.

![截屏2021-03-05 下午9.35.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004814.png?token=AWS37JM7A3AKSXNNXQWU5HDBTJXF2)

###### Step 7

Imagine we wanted to see what individual grant amounts compose the Total Award Amount for each Country/Region. To achieve this, we would want to differentiate between “Grantees”.

The “Marks” functions will allow you to insert further detail into your visualization. Click and drag your “Grantee” dimension into the “Marks” table beneath the icons (as below). Your visualization should now feature individual segments, which you can click for details about all the dimensions and measures you have worked into your visualization (you can see for example that Cuba has two segments).



**![截屏2021-03-05 下午9.35.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004384.png?token=AWS37JPSMIZC26NPA4XE3SDBTJXGK)**



**Who were the two recipients in Havana, Cuba and how much?**

**(Notice the SUM(Total Award Amount) above, how about other measures, try max, min, total, mean..?)**

###### Step 8. One more dimension

We can add another dimension, e.g. ‘Discipline’ but let’s distinguish it somehow (colour!). Drag ‘Discipline’ onto ‘Colour’:

![截屏2021-03-05 下午9.36.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004712.png?token=AWS37JMIOMLWBDVZ2JFFYTTBTJXGS)

**Which Discipline was granted the most, the least?**
**Which countries got funding for film?**

###### Step 9. Change colors

Don’t like those colours? By clicking the “Color” in the “Marks” window, you can customize the color palette, adjusting to the distribution of information present in your visualization.

![截屏2021-03-05 下午9.36.46](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005763.png?token=AWS37JJ7WUDHUR4DP55VGYTBTJXGW)

Then ‘Edit colors’

![截屏2021-03-05 下午9.37.13](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005888.png?token=AWS37JNC4UIGZXVVJTKSGP3BTJXHG)

###### Step 10. Filtering

Filter by year. Drag ‘Year’ onto ‘Filters’ to see:

![截屏2021-03-05 下午9.37.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005053.png?token=AWS37JMACRF7BGN4ZRNHS6DBTJXHO)
And ‘OK’ to select all (2009 to 2013). Now all this data is wrapped up and ready to go, let’s try a geographical view.

###### Step 11. Map

Start a new sheet, the icon to the right of Sheet1 below

![截屏2021-03-05 下午10.20.33](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220006717.png?token=AWS37JN3BRZ73HQICMIS3VLBTJXK4)

Drag and drop ‘Country’ onto the table below ‘Rows’ (use the larger of the ‘Drop field here’ cells):

![截屏2021-03-05 下午10.20.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005956.png?token=AWS37JOCFTKFVISFBZMR3PLBTJXIM)

=>

![截屏2021-03-05 下午10.21.03](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005827.png?token=AWS37JM37OTRWUZRDTBKY5DBTJXI2)
This launches a map, change it from a symbol map to a **filled map** using the ‘Show me’ dialog at right, drag ‘Country’ onto ‘Label’ also:![截屏2021-03-05 下午10.23.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005680.png?token=AWS37JNQOQFDJPRNTOPF3QTBTJXJM)

Now look for the measures labeled ‘Latitude’ and ‘Longitude’
**What does ‘generated’ mean?**

###### Step 12. Color the map

Drag ‘Total Award Amount’ onto ‘Color’ (the default colour range is gradient green, change it if you hate green!):

![截屏2021-03-05 下午10.24.06](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220005138.png?token=AWS37JL24P6VG2JEK6XGGLLBTJXJ4)

###### Step 13. Combine work sheets into a dashborad

Combining sheets in a ‘New Dashboard’ (icon below)

![截屏2021-03-05 下午10.24.51](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220006785.png?token=AWS37JM23CPZSJGZPBQXPB3BTJXKQ)

Drag from top left to ‘Drop sheets here’

![截屏2021-03-05 下午10.38.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220006350.png?token=AWS37JKLDISRXYXN4MVT25DBTJXLS)

Optional: Share (if you have a tableau account). (based on[ http://dh101.humanities.ucla.edu/wp-content/uploads/2014/09/Tableau_Public_Tutorial.pdf)](http://dh101.humanities.ucla.edu/wp-content/uploads/2014/09/Tableau_Public_Tutorial.pdf)

##### 5.2.3 B. Fuse tables

Sometimes (actually most of the time), the data is not stored in a single file.

For example, one file stores the ***state name\*** and ***state population\*** in Australia; another file stores the ***state name\*** and ***state population density\*** in Australia.

You want to explore the relationship between ***population\*** and ***population density\*** across different ***states\*** in Australia.

For this very simple example, you could simply open Excel and copy and paste.

However, for real, large data, it takes ages to do so, as you have to link them correctly record (row) by record (row).

TP provides the functionality to fuse multiple data sets by linking records (rows) through the “linking property” you specify for each data set.

Now we are going to introduce the procedures.

Download the example data sets, they are ‘[Shape of US Congressional District Boundaries, 110th Congress‘](https://lms.monash.edu/pluginfile.php/12151406/mod_book/chapter/869365/Shape-of-US-Congressional-District-Boundaries-110th-Congress.csv) and [‘Household heating by Congressional District – 2008‘.](https://lms.monash.edu/pluginfile.php/12151406/mod_book/chapter/869365/Household-heating-by-Congressional-District-2008.csv)

###### Step 1. Load the first data set (**updated**)

Open TP first.

Drag the first data set “Shape-of-US-Congressional-District-Boundaries-110th-Congress.csv” and drop it into the TP windows.

![img]()

 

###### Click on the triangle button on the data source, then Open.

![img]()

###### Step 2. Load the second data set (**updated**)

Drag the second data set “Household-heating-by-Congressional-District-2008.csv” and drop it into the previous TP windows.

![截屏2021-03-05 下午10.54.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220007566.png?token=AWS37JKXJXR2JPO4JLB5BRDBTJXQY)

###### Step 3. Configure the fuse

You need to specify the way you want to fuse the data sets.

- left data: first data set, Shape-of-US-Congressional-District-Boundaries-110th-Congress.csv and
- right data: second data set, Household-heating-by-Congressional-District-2008.csv

The 4 different ways to fuse are (TP shows them with visulisations – Venn diagram!):

- Inner, only fuse the rows (with the specified property) where the same value exists in both the left data and right data, drop the rows where the value only exists in one side.
- Left, fuse the rows (with the specified property) that exist in the left data, and drop the row if it is in the right data but not in the left data.
- Right, fuse the rows (with the specified property) that exist in the right data, and drop the rows if it is in the left data but not in the right data.
- Full order, fuse the rows (with the specified property) that exist in either side.





You also need to specify the linking properties for each side. In this case, let’s choose

- **“\*id\*”** for the left
- **“\*Two-Digital District\*”** for the right

And “inner” model to fuse.

![截屏2021-03-05 下午10.55.21](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220007326.png?token=AWS37JLHNDRFY7DGZSODBCTBTJXQO)

The fused table is shown in the “data view”.

Close the above window.

![截屏2021-03-05 下午10.55.39](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220007330.png?token=AWS37JNMPCO6H3JF7SNB72TBTJXQG)

And now you can create your “sheet” and use properties from both table to implement your visulisations!

![截屏2021-03-05 下午10.56.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220007624.png?token=AWS37JMJ4GELICGXIQYZGJTBTJXPY)

![截屏2021-03-05 下午10.56.24](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220007959.png?token=AWS37JKXSKGLINZJCOEDJWDBTJXOK)

**Try different fuse models, and how many rows you can get in each way?**

##### 5.2.4 C. Choropleth Map

Choropleth map is a map with regions filled by different colors representing different properties.

One use case is using different color to present different type.

Another is using gradient color to present quantitative data.

You will learn more details in Module 3 of FIT5147.

###### Step 1. Data always go first

Download the data from [Household-heating-by-State-2008.csv](https://lms.monash.edu/pluginfile.php/12151406/mod_book/chapter/869365/Household-heating-by-State-2008.csv) and “drag & drop”.

Make sure “State” column is marked as “geographic/locations”.

###### Step 2. Create the map

Create a new “Sheet”, and drag “States” to the center.

![截屏2021-03-05 下午11.02.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220006388.png?token=AWS37JNXQCRFQRQAR2QNSOLBTJXOE)

If you can see your map os US, congratulations! And you might choose to go to **Step 3** directly.

If your map cannot be generated properly, or you are looking at Australia, do not panic. This is not your fault, but TP has done something wrong.

Let’s fix it. Click on the top toolbar: **Map => Edit Locations**

![截屏2021-03-05 下午11.03.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220006696.png?token=AWS37JL454VRZG3KKAXEVCTBTJXOC)

You should look at the following window:

![截屏2021-03-05 下午11.03.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220008988.png?token=AWS37JP24H2DF2NIZM6SKMTBTJXR6)

TP locate all your data into Australia……Maybe it is because of the self-locate service on your machine.

Anyway, change it to “United States” and press “OK”.





###### Step 3. Color the map

Drag “% Housing Units That Are Mobile Homes” to “Color” in “Marks” section.

Rename it if you think this name is so long…

![截屏2021-03-05 下午11.33.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220008286.png?token=AWS37JIP5LRCOP7XS4RHUH3BTJXSO)

###### Step 4. More map options

Click the top toolbar: **Map => Map Layers**

![截屏2021-03-05 下午11.34.08](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220008349.png?token=AWS37JKJZ7EIDBW7SCQIJV3BTJXSU)

![截屏2021-03-05 下午11.34.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220008836.png?token=AWS37JMEU3SP52I4AKXMS2DBTJXTC)

Try different options, what do they mean?

You can even add a colored data layer! You may want to remove your previous color first.

Where does the data come from?





Remember the “generated”? Investigate what can/cannot be generated?