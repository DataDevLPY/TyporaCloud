From the above example, you will see that each data record looks like a [Python dictionary](https://docs.python.org/2/tutorial/datastructures.html#dictionaries). A JSON file usually contains a list of dictionaries, which is defined by '[' and ']'. In each of those dictionaries, there is a key-value pair for each row and the key and value are separated by a colon. Different key-value pairs are separated by commas. Note that a value can also be a dictionary, see "address" in the example. The basic types are object, array, value, string and number. If you would like to know more about JSON, please refer to

- [Introducing to JSON](http://www.json.org/): the JSON org website gives a very good diagrammatic explanation of JSON 📖.
- [Introduction to JSON](https://www.youtube.com/watch?v=WWa0cg_xMC8): a 15-minutes Youtube video on JSON, recommended for visual learners.

(Of course, you can also go and find your own materials on JSON by searching the Internet.)

In the rest of this section, we will start from an simple example, walking through steps of acquiring JSON Data from Google Maps Elevation API and normalizing those data into a flat table. Then, we revisit the dataset mentioned in the previous section (except that it is now in JSON format), parsing the data and store them in a Pandas DataFrame object. Before we start, it might be good for you to view one of the following tutorials on parsing JSON files:

- [Working with JSON data](http://wwwlyndacom.ezproxy.lib.monash.edu.au/Python-tutorials/Working-JSON-data/122467/142575-4.html): A Lynda tutorial on parsing JSON data. You need a Monash account to access this website. [here](http://resources.lib.monash.edu.au/eresources/lynda-guide.pdf) is the lynda settup guide.
- A [Youtube video](https://www.youtube.com/watch?v=9Xt2e9x4xwQ) on extracting data from JSON files (**optional**).





