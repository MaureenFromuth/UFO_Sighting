# UFO Sighting Challenge

## Overview of Project

Our primary customer for this project is Dana, a data analyst from McMinnville, Oregon, a place known for its UFO excitement.  Dana is also a UFO enthusiast and would like to explore this topic in greater depth and host a website that provides users a way to sort through sightings.   As part of our previous work with her, we have created a website that hosts sighting in a large table and allowed for the user to filter the table by date with a click of a button.  For our current task, Dana is asking for us to add more fields to the filter function and have the table filter immediately after update rather than waiting for the push of a button.  

Using a combination of JavaScript, HTML/CSS, and bootstrapping, Dana asked us to update our previous code that analyzed a number of [sightings](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/js/data.js) from across the country held within a JavaScript file.  This data is hosted in an array under a variable called ‘data’ and has multiple key value pairs as seen below.  Her task to us is to expand the number of key’s we are able to filter the table from just the date to also include city, state, country, and shape of the sighted object.

```
var data = [
  {
    datetime: "1/1/2010",
    city: "benton",
    state: "ar",
    country: "us",
    shape: "circle",
    durationMinutes: "5 mins.",
    comments: "4 bright green circles high in the sky going in circles then one bright green light at my front door."
  },  
```

For our project we started with code we had already built - a [JavaScript](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/js/app.js) file, an [HTML](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/index.html) file, and then the data file referenced above.  Starting with the JavaScript file, our code referenced the sighting data, and then used the powerful D3 library in JavaScript to begin the visualization of this array into a table by referencing ‘tbody’ in the HTML file we created.  We then looped through the data, using a ‘forEach’ function, appending the data into a row and then adding the HTML ’td’ tag to each row.  This provided the baseline from which we added additional code for the filtering, which we will highlight in the *Results* section.

```
// from data.js
const tableData = data;

// get table references
var tbody = d3.select("tbody");

function buildTable(data) {
  // First, clear out any existing data
  tbody.html("");

  // Next, loop through each object in the data
  // and append a row and cells for each value in the row
  data.forEach((dataRow) => {
    // Append a row to the table body
    let row = tbody.append("tr");

    // Loop through each field in the dataRow and add
    // each value as a table cell (td)
    Object.values(dataRow).forEach((val) => {
      let cell = row.append("td");
      cell.text(val);
    });
  });
}
```
For the HTML file, we utilized a story board for ‘inspiration’ and a CSS bootstrapping component to build out our website.  The HTML implemented a wrapper to allow for smoother transition between screen sizes, and built out a navigation bar to reset the site after filtering, a large box for the title *The Truth is Out there* (aka Jumbotron), and then two fluid containers: one for a brief description and title of the sight, and the other for the *Filter Search* input the table.  For the box containing the table, the HTML also names each of the headers.  This provided the baseline from which we added additional code in the input box for the *Filter Search*, which we will highlight in the *Results* section.

## Results

### Code Review

To build out the additional functionality, we needed to update the HTML file as well as the JavaScript code file.  

For the HTML file, we removed the button click function within the second fluid container, and then added additional lists for additional filter input.  

```
              <div class="col-md-3">
                <form class="bg-dark">
                    <p>Filter Search</p>
                </form>
                <ul class="list-group bg-dark">
                    <li class="list-group-item bg-dark">
                        <label for="date">Enter Date</label>
                        <input type="text" placeholder="1/10/2010" id="datetime"/>
                    </li>
                    <li class="list-group-item bg-dark">
                        <label for="date">Enter a City</label>
                        <input type="text" placeholder="roswell" id="city"/>
                    </li>
                    <li class="list-group-item bg-dark">
                        <label for="date">Enter a State</label>
                        <input type="text" placeholder="ca" id="state"/>
                    </li>
                    <li class="list-group-item bg-dark">
                        <label for="date">Enter a Country</label>
                        <input type="text" placeholder="us" id="country"/>
                    </li>
                    <li class="list-group-item bg-dark">
                        <label for="date">Enter a Shape</label>
                        <input type="text" placeholder="circle" id="shape"/>
                    </li>
                </ul>
              </div>

```

For the JavaScript application file, we had to create two new functions and update our listening function.  More specifically, we had to create a new function that extracted and held all of the new filter inputs.  We did this by first creating a new variable, *filters*, that would hold all of the key value pairs for the filter inputs from the website.  We then created the function, *updateFilters*, that looked for and extracted using D3 the new value or the *changedElement*, and saved that input as a value or the *elementValue* and the key or the ID as *filterId*.  The function then uses an if/else statement to input those key value pairs into the original *filters* variable.  Finally, it calls the next function, which filters the table.

```
// Create a variable to keep track of all the filters as an object.
let filters = {};

// Use this function to update the filters. 
function updateFilters() {

    // Save the element that was changed as a variable.
    let changedElement = d3.select(this);

    // Save the value that was changed as a variable.
    let elementValue = changedElement.property("value");
    console.log(elementValue);

    // Save the id of the filter that was changed as a variable.
    let filterId = changedElement.attr("id");
    console.log(filterId);
  
    // If a filter value was entered then add that filterId and value
    // to the filters list. Otherwise, clear that filter from the filters object.
    if (elementValue) {
      filters[filterId] = elementValue;
    }
    else {
      delete filters[filterId];
    }
    
    // Call function to apply all filters and rebuild the table
    filterTable();
  }
```

The next function, *filterTable*, takes the inputs from the *filters* array and filters the table.  To do this we created a new variable called *filteredData* and set it equal to the *tableData* variable in order to give the variable a set of inputs to start with.  We then use another *forEach* function to loop through each of the key value pairs in the *filter* array and filter the table down further and further when the key for that row equals the value in the *filters* variable.  Finally, we call our original function, *buildTable*, to rebuild the table in the website.


```
// Use this function to filter the table when data is entered.
function filterTable() {
  
  // Set the filtered data to the tableData.
  let filteredData = tableData;

  //Loop through all of the filters and keep any data that
  //matches the filter values
  Object.entries(filters).forEach(([key, value]) => {
  filteredData = filteredData.filter(row => row[key] === value)
  });

  // Finally, rebuild the table using the filtered data
  buildTable(filteredData);
};
```

Finally, to ensure these functions are actually executed, we had to update our original listening event from a button click to an update using the following code.

```
// 2. Attach an event to listen for changes to each filter
d3.selectAll("input").on("change", updateFilters);
```

### Site Functionality

As part of our deliverable to Dana, we also wanted to provide a brief tutorial on how the website works with each of the filters.  Below is a graphic of the overall site as it first appears upon load.  As you an see in the graphic, there is a navigation bar at the very top called *UFO Sightings* that allows you to reset the page, the large Jumbotron title *The Truth is Out There*, a brief title and overview of the site, and then the filters and table.  Of note, you will see in the filters that there is a ‘filler’ or ‘place holder’ value put into each of the filters.  These are light grey and are not actual inputs for the table.  Instead, however, they provide a template for users to look at to see what format they should put into the boxes for filtering.  

>**UFO Sighting Site: Load**

![UFO Sighting Site: Load](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/Overview.png)


To filter the table itself, a user goes into each one of the boxes for the appropriate filter criteria and adds an input.  The input, however, is not registered until the user either leaves that box via a tab or click elsewhere on the page.  This change then initiates the event listener we updated above, and that kicks off the *updateFilters*, *filterTable*, and *rebuildTable* functions we also listed above.  You can see in the graphic below the input value for a *Date* and in the second graphic what happens as a result of these events and functions once we hit *Tab*.    

>**UFO Sighting Site: Date Input**

![UFO Sighting Site: Date Input](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/Date%20Filter-Input.png)

>**UFO Sighting Site: Date Input & Tab**

![UFO Sighting Site: Date Input & Tab](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/Date%20Filter-Tab.png)


This functionality can be applied to each of the filter inputs in the *Filter Search* section, and as they are input by the user, the table will continue to update with the added filter.  Below provides an example of both the date above as well as inputing a city within the *City* filter area.  The graphic below depicts both the input and then the tab out of the input box.  

>**UFO Sighting Site: City Input & Tab**

![UFO Sighting Site: City Input & Tab](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/City%20Filter.png)


Finally, when a user wants to reset the table, they can go to the top of the page and click on the navigation bar, as identified previously.  This bar will reload the original HTML file and thus the original data without any filters.  

>**UFO Sighting Site: Reset Table Button**

![UFO Sighting Site: Reset Table Button](https://github.com/MaureenFromuth/UFO_Sighting/blob/master/Reset%20Button.png)


## Summary

Overall, the code and website provides very basic functionality for filtering the table for analytics of UFO sightings.  While it will help other enthusiasts sort through the data, there are a few drawbacks and areas for improvement on the site.  

**Drawback: Exact Spelling & Case Notation and No Range/Multiple Input Selection**
Although the *Filter Search* box and inputs have placeholders that show the format of different inputs, users need to input the exact case and spelling for each of the inputs to get the table filtered correctly.  If a user, for example, misspells the town of Roswell, they will not get the correct returns/filter results.  If they spell the name with a capital ‘R’ and not a lowercase ‘r’ as it appears in the table, they will also not get any results.  This could be problematic for users and would force them to look through the table to find the results they are looking for in order to ensure the correct spelling and case.  Additionally, users are only able to put in one input per each key.  Therefore, they are not able to look at trends or groupings of inputs together.  For example, if a user wanted to look at the number of sights from 1 Jan 2010 to 5 Jan 2015 in California and Oregon, they would have to conduct a filter for each day and for each state, i.e. 10 different filters.  

**Area for Improvement 1: Add Drop Down Menus for Selection**
One way to improve and remedy the drawback above on case and spelling would be to have a drop down instead of a free-form text input within the *Filter Search*.  The drop down fields would be populated based off of the available values within the remaining key value pairs of the filteredTable.  To do this, you would first need to create a new variable, ‘dropDown’ that holds all of the existing values for each key.  This variable would be populated using a forEach function and an if/else function, whereby the code loops through the dataTable and if the value is already in the ‘dropDown’ it is passed over but if not it is appended to the array.  The intent would be to create an array within an array for each field.  Using this variable, you can then populate the drop down menu in the HTLM and switch the listening function BACK to update on a click.  

**Area for Improvement 2: Provide Ranges or Multiple Selection**
Another area of improvement that addresses the second drawback above would be to allow for the selection of multiple inputs for a given key.  In doing this, the user would then be able to conduct trend analysis and would not require them to execute individual filters if they wanted to do so.  
