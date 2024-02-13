<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3'; // Import all d3 modules

  // Constants for chart dimensions
  const margin = { top: 40, right: 80, bottom: 70, left: 80 }; // Increased top margin
  const width = 850 - margin.left - margin.right;
  const height = 550 - margin.top - margin.bottom;

  // Input data received from the parent component
  export let jsonData;

  // Function to process data
  function processData(data) {
    // Extract unique years from the data
    const uniqueYears = Array.from(new Set(data.map(entry => entry.Year)));
    // Sort years in ascending order
    uniqueYears.sort((a, b) => a - b);

    // Format data for the chart
    return uniqueYears.map(year => ({
      label: year,
      value: year,
      top5Countries: getTop5CountriesForYear(data, year)
    }));
  }

  // Function to filter top 5 countries for a given year
  function getTop5CountriesForYear(data, year) {
    // Filter data for the specified year
    const yearData = data.filter(entry => entry.Year === year);

    // Sort filtered data by energy consumption per capita
    yearData.sort((a, b) => b["Primary energy consumption per capita (kWh/person)"] - a["Primary energy consumption per capita (kWh/person)"]);

    // Take the top 5 countries
    const top5Countries = yearData.slice(0, 5);

    // Format data for the chart
    return top5Countries.map(entry => ({
      "Entity": entry.Entity,
      "Code": entry.Code ? entry.Code : "N/A",
      "Year": entry.Year ? entry.Year : "N/A",
      "Primary energy consumption per capita (kWh/person)": entry["Primary energy consumption per capita (kWh/person)"] ? entry["Primary energy consumption per capita (kWh/person)"] : "N/A"
    }));
  }

  // State to hold the selected year
  let selectedYear;

  // State to hold the filtered data based on the selected year
  let filteredData = [];

  // Function to handle user's year selection
  function handleYearChange(yearValue) {
    selectedYear = yearValue;
    // Filter top 5 countries for the selected year
    filteredData = yearsData.find(item => item.value === selectedYear).top5Countries;
    // Render the chart with the filtered data
    renderChart();
  }

  let yearsData = [];

  onMount(() => {
    yearsData = processData(jsonData);

    // Set default selected year to the first available year
    selectedYear = 2005;

    // Filter top 5 countries for the default selected year
    filteredData = yearsData.find(item => item.value === selectedYear).top5Countries;

    // Render the chart with the filtered data
    renderChart();
  });

  let tooltipVisible = false;
  let tooltipData = {};

  // Function to show the tooltip
  function showTooltip(data, x, y) {
    tooltipData = data;
    tooltipVisible = true;
    // Update tooltip position and content
    const tooltip = document.getElementById('tooltip');
    tooltip.style.left = `${x}px`;
    tooltip.style.top = `${y}px`;
    tooltip.innerHTML = `${data.Entity}: ${data["Primary energy consumption per capita (kWh/person)"]} kWh/person`;
  }

  // Function to hide the tooltip
  function hideTooltip() {
    tooltipVisible = false;
    // Clear tooltip content
    document.getElementById('tooltip').innerHTML = '';
  }

  // Function to handle mouseenter event for the bars
  function handleMouseEnter(event, d) {
    const originalColor = d3.select(event.target).attr("fill");

    // Change the fill color of the bar to blue
    d3.select(event.target)
    .transition()
    .duration(200)
    .attr("fill", d3.interpolateOranges(d3.randomUniform(0.25, 1)()));
    // Show tooltip
    const [x, y] = d3.pointer(event);
    showTooltip(d, x, y);

    d3.select(event.target)
    .on("mouseleave", function() {
      d3.select(this)
        .transition()
        .duration(200)
        .attr("fill", originalColor);
      hideTooltip();
    });
  }

  // Function to handle mouseleave event for the bars
  function handleMouseLeave(event, d) {
    // Change the fill color of the bar back to its original color
    d3.select(event.target)
      .attr("fill", "lightblue");
    // Hide tooltip
    hideTooltip();
  }

  // Function to render the chart
  function renderChart() {
    // Remove existing SVG elements
    d3.select("#chart svg").remove();

    // Create the SVG element
    const svg = d3.select("#chart")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);

    // Calculate the maximum value of energy consumption per capita across all years
    const maxEnergyConsumption = d3.max(yearsData.flatMap(year => year.top5Countries), d => d["Primary energy consumption per capita (kWh/person)"]);

    // Create scales
    const x = d3.scaleBand().range([0, width]).padding(0.1);
    const y = d3.scaleLinear().range([height, 0]);

    x.domain(filteredData.map(d => d.Entity));
    y.domain([0, maxEnergyConsumption]); // Set the y-axis domain dynamically

    // Add bars
    svg.selectAll(".bar")
        .data(filteredData)
        .enter().append("rect")
        .attr("class", "bar")
        .attr("x", d => x(d.Entity))
        .attr("width", x.bandwidth())
        .attr("y", d => y(d["Primary energy consumption per capita (kWh/person)"]))
        .attr("height", d => height - y(d["Primary energy consumption per capita (kWh/person)"]))
        .attr("fill", "lightblue") // Set initial fill color
        .on("mouseenter", handleMouseEnter)
        .on("mouseleave", handleMouseLeave);

    // Add axes
    svg.append("g")
        .attr("transform", `translate(0,${height})`)
        .call(d3.axisBottom(x));

    svg.append("g")
        .call(d3.axisLeft(y));

    // Add y-axis label
    svg.append("text")
        .attr("transform", "rotate(-90)")
        .attr("y", 0 - margin.left)
        .attr("x", 0 - (height / 2))
        .attr("dy", "1em")
        .style("text-anchor", "middle")
        .text("Primary energy consumption (kWh/person)");

    // Add text element to display the selected year above the chart
    svg.append("text")
        .attr("x", width / 2)
        .attr("y", -margin.top / 2)
        .attr("text-anchor", "middle")
        .style("font-size", "20px") // Larger font size
        .style("padding-top", "10px") // Padding above the text
        .style("padding-bottom", "10px") // Padding below the text
        .text(`Top 5 Primary Energy Consumers (Per Capita) in ${selectedYear}`);

    const logStatement = filteredData.map(d => `${d.Entity}: ${d["Primary energy consumption per capita (kWh/person)"]}`).join(', ');
    console.log(`Year ${selectedYear}: ${logStatement}`);
  }
</script>

<div id="year-container">
  {#each yearsData as year}
    <button class="year-button" on:click={() => handleYearChange(year.value)}>{year.label}</button>
  {/each}
</div>

<div id="chart"></div>

<div id="tooltip" class="{tooltipVisible ? 'visible' : ''}"></div>

<style>
  body {
    display: flex;
    justify-content: center;
  }

  #year-container {
    display: flex;
    overflow-x: auto;
    white-space: nowrap;
    width: 700px; /* Adjust this value as needed */
    margin: 0 auto; /* Center the container horizontally */
    padding-right: 0px; /* Add padding to the right to accommodate the scrollbar */
    padding-bottom: 20px;
  }

  .year-button {
    flex: 0 0 auto;
    margin-right: 10px;
    /* Add additional styles for the year buttons as needed */
  }

  /* Bar styles */
  .bar {
    fill: lightblue;
    transition: fill 0.3s; /* Smooth transition for color change */
  }

  /* Tooltip styles */
  #tooltip {
    position: absolute;
    background-color: rgba(0, 0, 0, 0.7);
    color: white;
    padding: 5px;
    border-radius: 5px;
    pointer-events: none;
    display: none;
  }

  #tooltip.visible {
    display: block;
  }
</style>
