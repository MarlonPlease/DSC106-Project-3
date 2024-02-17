# About

The dataset we selected for our interactive visualization graph comprises the energy per capita consumption data provided by Our World in Data, containing information on the years 1900 to 2022. Upon examining the data, our focus turned to identifying the top 5 primary energy consumers per capita from 1965 to 2022. Our objective was to create an interactive bar graph that answers the question: 'What were the top consumers of energy per capita in a given year?

The first interactive component of our bar graph allows users to select a specific year and observe the top 5 primary energy-consuming countries. In this setup, the x-axis represents countries, while the y-axis represents primary energy consumption (kWh/person). We implemented a function using a click event, handled by the function called handleYearChange(), which updates the graph depending on the selected year. This function is triggered by clicking HTML buttons containing the years of the data. This setup the bar graph to update dynamically as the user switches the year. To make use of screen space, we incorporated a horizontal scrollbar to contain the HTML buttons, which allows navigation of the years of the dataset. Another interactive feature of our graph enables users to hover over the bars and view the precise kilowatt-hour of energy consumption for a particular country. Visually, the bars change color upon hovering, transitioning from light orange to red, allowing for differentiation among the top 5 countries using the “hover” feature, along with additional information displayed to the user based on what bar the mouse was hovering over. Initially, we considered creating a choropleth graph to highlight the top 5 countries, however, due to the complexity of implementation in d3, we opted for a bar graph. In the future, we aim to experiment with creating a choropleth graph using d3.

Amongst our 3 group members, Marlon, Derrick, and Sai, Derrick found the dataset and converted the CSV from the website into a JSON file for JavaScript readability and console outputs for testing our functions. We all had Zoom meetings to review the data and decide on the question we wanted our graph to answer. Derrick assisted with setting up the Svelte components and initial settings. When coding the graph in d3, Sai implemented the scroll bar for selecting the year, while Marlon created the text titles and handled the HTML formatting. Additionally, we all collaborated on creating the hovering effects for the bars. The aspect that took the most time was creating the scroll bar for selecting a year, as the graph was not updating based on the year chosen, requiring us to research and reimplement our code to allow for the bars to update. Another challenging task was making the bars dynamic using the "hover" function. Our application took approximately 12-ish hours to implement.

## Setup

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building static site

Just push to github. 
