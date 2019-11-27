<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <link rel='icon' href="/Image/icon.png"/>
        <title>Interactive Design</title>
        <script src="https://d3js.org/d3.v3.min.js" charset="utf-8"></script>
        <style>
        body {
            font: 10px sans-serif;
        }
        select {
            display: block;
        }
        .bar {
            fill: purple;
            opacity: 0.8;
        }

        .axis path,
        .axis line {    
            fill: none;
            stroke: #000;
            shape-rendering: crispEdges;
        }
        .position {
            float:right;
            padding-right: 20px;
        }
        .left {
            padding-left: 20px;
            padding-top: 20px;
            padding-right: 20px;
        }
        p {
            font: 12px sans-serif;
        }
        ul {
            font: 12px sans-serif;
        }
        </style>
    </head>
    <body class="left">
        <div>
            <span class="position" >
                <img src="/Image/Logo.png" width="150" height="100">
            </span>
            <span>
                <h1 style="color:tomato"> Interactive Design : Olympics </h1>
            <h2 style="color:#000080">Count of Medals collected by Countries in last 120 Years</h2>
        </span>
        <div id='vis-container'></div>
        
        </div>
        <div>
            <p><b>Q1: How did you choose your particular visual encodings and interaction techniques?</b> <br>
                    Ans: The dataset for our Visualization was <a href="https://www.kaggle.com/heesoo37/120-years-of-olympic-history-athletes-and-results">"120 Years of Olympics History" </a>. Our initial choice of design was to display count of medals for every country and we wanted to make it interactive. When we explored our data set we realised that we could display the variations in a country's performance for a specific year effectively using bar charts. The interactive part about our visualization is the ability of the user to be able to select the country's data they desire to view using the DROPDOWN MENU. <br>
                <br>
                <b>Q2: What alternatives did you consider and how did you arrive at your ultimate choices?</b><br>
                Ans: The alternatives considered were animated bar chart that would transition every 5 seconds to change the year's data. Since our dataset is very humungous, consisting of 137 unique Countries spanning over 120 years. We wanted to effectively communicate our visualization's idea
                without omission. And hence, we came to the conclusion that an interactive bar chart would effectively exhibit our scheme without omiiting any data. <br>
                <br>
                <b>Q3: Development Process.</b>
                <ul>  
                    <li> Data Wrangling : Yesha Shah </li>
                    <li> Function definitions and UI : Mukkul Jayhne </li>
                    <li> D3 Adaption Functions : Combined Efforts </li>
                    <li> Debugging : Koushik Sai Venkataramanan </li>
                </ul></p>
                <p>
                <b>Q4: Commentary of the Development Process. </b><br>
                Ans: <br><u><b>Data wrangling:</b> </u>This was the most tedious task as the raw data was pretty random and had a lot of NULL/NA values in important fields like "MEDALS". To tackle this, we performed a two step process. The first was to count the number of medals for every country and then apply one-hot encoding for every year to wrangle the data for every country. After this, our dataset had more NULL values than before, due to which, we had to manually rearrange the structure of the dataset to solve the issue of absent values.<br>
                    <u> <b>Function definitions and UI:</b></u> We initially created pseudocodes for the required functions that was supposed to be implemented and designed a skeleton of our code. Post this, we created a raw sketch of the visualization.<br>
                     <u><b>D3 adaptation of functions:</b> </u>Since we had the pseudocode, it was easy to adapt it to D3 environment. It took us few hours to comprehend the implementation of concepts.<br>
                     <u><b>Debugging:</b></u> Pre-existing D3 functions were still not working due to syntactical differences from other coding languages. Due to unprecedented circumstances, there was an addition of an empty row with all zeroes. We had to remove the empty row for proper visualization.<br>
                     <br>
                <b>Q5: Time spent developing the application.</b><br>
                Ans: It took us approximately 17-20 hours to implement the project in its entirity.<br>
                <br>
                </p>
        </div>
        <footer><center><p>Design Implemeted By: Koushik Sai Venkataramanan, Mukkul Jayhne, Yesha Shah</p></center></footer>
        <script type="text/javascript">
            // Load and munge data, then make the visualization.
             var fileName = "/data/athlete_events.csv";
            var years = ["1896","1900","1904","1906","1908","1912","1920","1924","1928","1932","1936","1948","1952","1956","1960","1964","1968","1972","1976","1980","1984","1988","1992","1994","1996","1998","1998","2000","2002","2004","2006","2008","2010","2012","2014","2016"];

            // d3.csv(fileName, function(error,data){
            //     console.log(data);
            // });

            d3.csv(fileName, function(error, data) {
                var countryMap = {};
                var country;
                data.forEach(function(d) {
                    country = d.Country;
                    //console.log(country);
                    countryMap[country] = [];
                    //console.log(countryMap[country]);
                    // { countryName: [ bar1Val, bar2Val, ... ] }
                    years.forEach(function(field) {
                        countryMap[country].push( +d[field] );
                    });

                //console.log(countryMap);       
                });
                //var count = Object.keys(countryMap[country]).length;
                //console.log(countryMap);
                delete countryMap['']
                makeVis(countryMap);

            });

            var makeVis = function(countryMap) {
                // Define dimensions of vis
                var margin = { top: 30, right: 50, bottom: 30, left: 50 },
                    width  = 1500 - margin.left - margin.right,
                    height = 500 - margin.top  - margin.bottom;

                // Make x scale
                var xScale = d3.scale.ordinal()
                    .domain(years)
                    .rangeRoundBands([0, width], 0.1);

                // Make y scale, the domain will be defined on bar update
                var yScale = d3.scale.linear()
                    .range([height, 0]);

                // Create canvas
                var canvas = d3.select("#vis-container")
                  .append("svg")
                    .attr("width",  width  + margin.left + margin.right)
                    .attr("height", height + margin.top  + margin.bottom)
                  .append("g")
                    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

                // Make x-axis and add to canvas
                var xAxis = d3.svg.axis()
                    .scale(xScale)
                    .orient("bottom");

                canvas.append("g")
                    .attr("class", "x axis")
                    .attr("transform", "translate(0," + height + ")")
                    .call(xAxis);

                // Make y-axis and add to canvas
                var yAxis = d3.svg.axis()
                    .scale(yScale)
                    .orient("left");

                var yAxisHandleForUpdate = canvas.append("g")
                    .attr("class", "y axis")
                    .call(yAxis);

                yAxisHandleForUpdate.append("text")
                    .attr("transform", "rotate(-90)")
                    .attr("y", 6)
                    .attr("dy", ".71em")
                    .style("text-anchor", "end")
                    .text("Count of Medals");

                var updateBars = function(data) {
                    // First update the y-axis domain to match data
                    yScale.domain( d3.extent(data) );
                    yAxisHandleForUpdate.call(yAxis);

                    var bars = canvas.selectAll(".bar").data(data);

                    // Add bars for new data
                    bars.enter()
                      .append("rect")
                        .attr("class", "bar")
                        .attr("x", function(d,i) { return xScale( years[i] ); })
                        .attr("width", xScale.rangeBand())
                        .attr("y", function(d,i) { return yScale(d); })
                        .attr("height", function(d,i) { return height - yScale(d); });

                    // Update old ones, already have x / width from before
                    bars
                        .transition().duration(250)
                        .attr("y", function(d,i) { return yScale(d); })
                        .attr("height", function(d,i) { return height - yScale(d); });

                    // Remove old ones
                    bars.exit().remove();
                };

                // Handler for dropdown value change
                var dropdownChange = function() {
                    var newcountry = d3.select(this).property('value'),
                        newData   = countryMap[newcountry];

                    updateBars(newData);
                };

                // Get names of countrys, for dropdown
                var countrys = Object.keys(countryMap).sort();

                var dropdown = d3.select("#vis-container")
                    .insert("select", "svg")
                    .on("change", dropdownChange);

                dropdown.selectAll("option")
                    .data(countrys)
                  .enter().append("option")
                    .attr("value", function (d) { return d; })
                    .text(function (d) {
                        return d[0].toUpperCase() + d.slice(1,d.length); // capitalize 1st letter
                    });

                var initialData = countryMap[ countrys[0] ];
                updateBars(initialData);
            };
        </script>
    </body>
</html>