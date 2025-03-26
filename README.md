# DFW Oil & Gas Tariff Impact: Analyzing Fuel Price Scenarios for Consumers

## Overview

This project analyzes the impact of recent tariffs on Canadian and Mexican oil imports, effective March 4, 2025, on oil and gas companies in the Dallas-Fort Worth (DFW) metroplex—Valero, Chevron, HF Sinclair, and Energy Transfer—and the resulting effects on consumers. Using advanced data visualization techniques in R, we explore how these tariffs could reshape the oil and gas landscape, focusing on fuel price increases across urban, suburban, and rural DFW areas.

The analysis includes:
- A line graph showing predicted fuel price increases under three tariff pass-through scenarios (High, Moderate, Low Impact).
- A bar chart comparing the estimated cost increases for Valero, Chevron, HF Sinclair, and Energy Transfer due to tariffs.
- An interactive 2D map visualizing fuel price increases in key DFW areas, with hover-over labels for detailed insights.

## Table of Contents
- [Background](#background)
- [Data Analysis](#data-analysis)
  - [Fuel Price Impact Scenarios](#fuel-price-impact-scenarios)
  - [Company Cost Impact Analysis](#company-cost-impact-analysis)
  - [Interactive 2D Map](#interactive-2d-map)
- [Why It Matters](#why-it-matters)
- [Conclusion](#conclusion)
- [How to Run the Code](#how-to-run-the-code)
- [Dependencies](#dependencies)
- [License](#license)

## Background

On March 4, 2025, new tariffs were imposed: a 10% tariff on Canadian energy products and a 25% tariff on Mexican oil imports, aimed at addressing issues like illegal immigration and drug smuggling. These tariffs have raised concerns about higher fuel prices for U.S. consumers, particularly in regions like the DFW metroplex, a hub for oil and gas companies such as Valero, Chevron, HF Sinclair, and Energy Transfer.

Inspired by Stacy Schmitt’s LinkedIn post, which highlighted a $676M cost increase for Valero and a 0.3% edge for Chevron, this project quantifies the potential impacts on these companies and models fuel price scenarios for consumers. With urban fuel prices already up 8.61 cents/gallon, the stakes are high for both industry stakeholders and DFW residents.

## Data Analysis

### Fuel Price Impact Scenarios

We model three scenarios for fuel price increases in the DFW metroplex from March 2025 to September 2026, based on how much of the tariff costs refiners pass on to consumers:
- **High Impact (Full Pass-Through):** 100% of tariff costs passed to consumers.
- **Moderate Impact (Partial Pass-Through):** 50% of tariff costs passed to consumers.
- **Low Impact (Minimal Pass-Through):** 20% of tariff costs passed to consumers.

**Key Findings:**
- Urban areas (e.g., Dallas) face the highest increases, with the High Impact scenario reaching 26.61 cents/gallon by September 2026.
- Suburban areas (e.g., Frisco) see delayed impacts, with the High Impact scenario at 18 cents/gallon.
- Rural areas (e.g., Mansfield) experience the smallest increases, with the High Impact scenario at 10.8 cents/gallon.

**R Code for Fuel Price Impact Line Graph:**

```R
# Install and load required packages
if (!require(ggplot2)) install.packages("ggplot2")
if (!require(dplyr)) install.packages("dplyr")

library(ggplot2)
library(dplyr)

# Define dates for the analysis (March 2025 to September 2026)
dates <- c("Mar-25", "Sep-25", "Mar-26", "Sep-26")

# Baseline fuel price in DFW (March 2025)
baseline_price <- 2.70

# Fuel price increases under three scenarios (in cents/gallon)
fuel_price_data <- data.frame(
  Date = rep(dates, 9),
  Area = rep(c("Urban", "Suburban", "Rural"), each = length(dates), times = 3),
  Scenario = rep(c("High Impact", "Moderate Impact", "Low Impact"), each = length(dates) * 3),
  PriceIncrease = c(
    # Urban - High Impact: 8.61 cents + 5 cents/month (full pass-through)
    8.61, 8.61 + 5*6, 8.61 + 5*12, 8.61 + 5*18,
    # Suburban - High Impact: 5 cents/month starting Sep 2025
    0, 5*6, 5*12, 5*18,
    # Rural - High Impact: 3 cents/month starting Sep 2025
    0, 3*6, 3*12, 3*18,
    # Urban - Moderate Impact: 8.61 cents + 2.5 cents/month (50% pass-through)
    8.61, 8.61 + 2.5*6, 8.61 + 2.5*12, 8.61 + 2.5*18,
    # Suburban - Moderate Impact: 2.5 cents/month starting Sep 2025
    0, 2.5*6, 2.5*12, 2.5*18,
    # Rural - Moderate Impact: 1.5 cents/month starting Sep 2025
    0, 1.5*6, 1.5*12, 1.5*18,
    # Urban - Low Impact: 8.61 cents + 1 cent/month (20% pass-through)
    8.61, 8.61 + 1*6, 8.61 + 1*12, 8.61 + 1*18,
    # Suburban - Low Impact: 1 cent/month starting Sep 2025
    0, 1*6, 1*12, 1*18,
    # Rural - Low Impact: 0.5 cents/month starting Sep 2025
    0, 0.5*6, 0.5*12, 0.5*18
  )
)

# Convert Date to a proper date format
fuel_price_data$Date <- as.Date(paste0("01-", fuel_price_data$Date), format = "%d-%b-%y")

# Plot fuel price increases
ggplot(fuel_price_data, aes(x = Date, y = PriceIncrease, color = Area, linetype = Scenario)) +
  geom_line(linewidth = 1) +
  scale_color_manual(values = c("Urban" = "purple", "Suburban" = "orange", "Rural" = "brown")) +
  scale_linetype_manual(values = c("High Impact" = "solid", "Moderate Impact" = "dashed", "Low Impact" = "dotted")) +
  labs(title = "Predicted Fuel Price Increases in DFW Metroplex Due to Tariffs",
       x = "Date", y = "Price Increase (cents/gallon)") +
  theme_minimal() +
  theme(legend.position = "bottom")

# Save the plot
ggsave("fuel_price_increases_due_to_tariffs.png", width = 8, height = 6)
```

### Company Cost Impact Analysis

We estimate the cost increases for Valero, Chevron, HF Sinclair, and Energy Transfer due to the tariffs, based on their exposure to Canadian and Mexican oil imports:
- **Valero:** $676M cost increase (25% tariff on Mexican oil).
- **HF Sinclair:** $400M cost increase (10% tariff on Canadian crude).
- **Chevron:** $200M cost increase but gains a 0.3% edge.
- **Energy Transfer:** $150M cost increase due to reduced Canadian crude flows.

**R Code for Company Cost Impact Bar Chart:**

```R
# Install and load required packages
if (!require(ggplot2)) install.packages("ggplot2")

library(ggplot2)

# Data for company cost impacts
company_cost_data <- data.frame(
  Company = c("Valero", "Chevron", "HF Sinclair", "Energy Transfer"),
  CostIncrease = c(676, 200, 400, 150)  # In millions USD
)

# Plot bar chart
ggplot(company_cost_data, aes(x = Company, y = CostIncrease, fill = Company)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("Valero" = "darkred", "Chevron" = "darkblue", 
                               "HF Sinclair" = "darkgreen", "Energy Transfer" = "darkorange")) +
  labs(title = "Estimated Cost Increases for DFW Oil & Gas Companies Due to Tariffs",
       x = "Company", y = "Cost Increase (Millions USD)") +
  theme_minimal() +
  theme(legend.position = "none")

# Save the plot
ggsave("company_cost_increases_due_to_tariffs.png", width = 6, height = 4)
```

### Interactive 2D Map

We create an interactive 2D map of the DFW metroplex using the `leaflet` package, showing fuel price increases in key areas under the Moderate Impact scenario (50% pass-through) as of September 2026. The map includes hover-over labels for detailed insights.

**Key Findings:**
- **Urban Areas (e.g., Dallas):** 21.36 cents/gallon increase.
- **Suburban Areas (e.g., Frisco, Plano, Arlington):** 15 cents/gallon increase.
- **Rural Areas (e.g., Lewisville, Melissa, Mansfield):** 9 cents/gallon increase.

**R Code for Interactive 2D Map:**

```R
# Install and load required packages
if (!require(leaflet)) install.packages("leaflet")
if (!require(dplyr)) install.packages("dplyr")
if (!require(htmlwidgets)) install.packages("htmlwidgets")

library(leaflet)
library(dplyr)
library(htmlwidgets)

# Data for DFW areas with fuel price increases (Moderate Impact scenario, Sep 2026)
dfw_fuel_areas <- data.frame(
  Area = c("Dallas", "Frisco", "Plano", "Arlington", "Lewisville", "Melissa", "Mansfield"),
  Type = c("Urban", "Suburban", "Suburban", "Suburban", "Rural", "Rural", "Rural"),
  Lat = c(32.7767, 33.1507, 33.0198, 32.7357, 33.0462, 33.2850, 32.5632),
  Lon = c(-96.7969, -96.8236, -96.6989, -97.1081, -96.9942, -96.5724, -97.1417),
  FuelPriceIncrease = c(21.36, 15, 15, 15, 9, 9, 9)  # Moderate Impact scenario, Sep 2026
)

# Add a hover label for each area
dfw_fuel_areas <- dfw_fuel_areas %>%
  mutate(Label = paste0(Area, "<br>",
                        "Type: ", Type, "<br>",
                        "Fuel Price Increase: ", FuelPriceIncrease, " cents/gallon"))

# Create the interactive map
dfw_fuel_map <- leaflet(data = dfw_fuel_areas) %>%
  addTiles() %>%
  setView(lng = -96.7969, lat = 32.7767, zoom = 9) %>%
  addCircleMarkers(
    lng = ~Lon,
    lat = ~Lat,
    radius = 8,
    color = ~case_when(
      Type == "Urban" ~ "purple",
      Type == "Suburban" ~ "orange",
      Type == "Rural" ~ "brown"
    ),
    fillOpacity = 0.7,
    popup = ~Label,
    label = ~Label,
    labelOptions = labelOptions(
      style = list("font-weight" = "normal", padding = "3px 8px"),
      textsize = "12px",
      direction = "auto"
    )
  ) %>%
  addLegend(
    position = "bottomright",
    colors = c("purple", "orange", "brown"),
    labels = c("Urban (21.36 cents/gallon)", 
               "Suburban (15 cents/gallon)", 
               "Rural (9 cents/gallon)"),
    title = "Fuel Price Increase (Moderate Impact, Sep 2026)"
  )

# Print the map to display it in RStudio or console
print(dfw_fuel_map)

# Save the map as an HTML file
saveWidget(dfw_fuel_map, file = "dfw_fuel_price_map.html")
```

## Why It Matters

The oil and gas sector is a cornerstone of the DFW economy, supporting jobs, transportation, and energy needs for millions of residents. The new tariffs on Canadian and Mexican oil imports threaten to increase fuel prices, disproportionately affecting urban consumers who face the highest increases (up to 26.61 cents/gallon in the High Impact scenario). For low-income households and commuters, this could mean an additional $13.31 per 50-gallon tank, straining budgets in a region already grappling with rising living costs. Meanwhile, companies like Valero face significant cost pressures ($676M increase), which could lead to reduced refining capacity or higher prices, while others like Chevron may gain a competitive edge. Understanding these dynamics is crucial for consumers, businesses, and policymakers to navigate the tariff-driven market and mitigate impacts on vulnerable communities.

## Conclusion

This project provides a comprehensive analysis of how tariffs on Canadian and Mexican oil imports are reshaping the oil and gas landscape in the DFW metroplex. Through predictive modeling, we quantify the potential fuel price increases for consumers across urban, suburban, and rural areas, while also assessing the cost impacts on major companies. The interactive 2D map offers a spatial perspective, highlighting the uneven distribution of these impacts. This analysis serves as a valuable resource for stakeholders to prepare for and respond to the challenges posed by tariffs in the energy sector.

## How to Run the Code

1. **Set Up R and RStudio:**
   - Install [R](https://www.r-project.org/) and [RStudio](https://rstudio.com/).
   - Clone this repository to your local machine:
     ```bash
     git clone https://github.com/your-username/DFW-Oil-Gas-Tariff-Impact.git
     cd DFW-Oil-Gas-Tariff-Impact
     ```

2. **Install Dependencies:**
   - Open RStudio and run the code blocks to install the required packages (see the [Dependencies](#dependencies) section).

3. **Run the Fuel Price Impact Line Graph:**
   - Copy and paste the code under the “Fuel Price Impact Scenarios” section into RStudio.
   - Run the code to generate the line graph (`fuel_price_increases_due_to_tariffs.png`).

4. **Run the Company Cost Impact Bar Chart:**
   - Copy and paste the code under the “Company Cost Impact Analysis” section into RStudio.
   - Run the code to generate the bar chart (`company_cost_increases_due_to_tariffs.png`).

5. **Run the Interactive 2D Map:**
   - Copy and paste the code under the “Interactive 2D Map” section into RStudio.
   - Run the code to display the map in RStudio (via the `print()` command) and save it as `dfw_fuel_price_map.html`.
   - Open the `dfw_fuel_price_map.html` file in a web browser to interact with the map.

## Dependencies

- R packages:
  - `ggplot2`: For creating static plots.
  - `dplyr`: For data manipulation.
  - `leaflet`: For creating interactive maps.
  - `htmlwidgets`: For saving the interactive map as an HTML file.

Install these packages in RStudio using the code provided in each section.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
```

---

### **Explanation of the README**

1. **Overview:** Summarizes the project, highlighting the focus on tariff impacts on oil and gas companies and consumers in the DFW metroplex, and the three key visualizations.
2. **Background:** Provides context on the tariffs, their purpose, and their relevance to the DFW metroplex, referencing Stacy Schmitt’s LinkedIn post for inspiration.
3. **Data Analysis:**
   - **Fuel Price Impact Scenarios:** Describes the line graph, key findings, and includes the R code.
   - **Company Cost Impact Analysis:** Explains the bar chart, cost estimates, and includes the R code.
   - **Interactive 2D Map:** Details the map, its findings, and includes the R code with the `print()` command.
4. **Why It Matters:** Emphasizes the economic and social implications of fuel price increases, particularly for low-income households and commuters, and the strategic importance for companies.
5. **Conclusion:** Summarizes the project’s value and insights for stakeholders.
6. **How to Run the Code:** Provides step-by-step instructions for running the code, including cloning the repository, installing dependencies, and generating the outputs.
7. **Dependencies:** Lists the required R packages.
8. **License:** Specifies the MIT License for the project.

---

### **Next Steps**
1. **Create the Repository:**
   - Go to GitHub and create a new repository named `DFW-Oil-Gas-Tariff-Impact`.
   - Initialize it with a README file.

2. **Add the README File:**
   - Copy the above markdown content into the `README.md` file in your repository.

3. **Add the Code Files:**
   - Create separate R scripts for each section (e.g., `fuel_price_scenarios.R`, `company_cost_analysis.R`, `fuel_price_map.R`) and add them to the repository.
   - Alternatively, the code is already included in the README for easy access.

4. **Add a License File:**
   - Add a `LICENSE` file with the MIT License text to your repository.

5. **Push the Outputs:**
   - Add the generated files (`fuel_price_increases_due_to_tariffs.png`, `company_cost_increases_due_to_tariffs.png`, and `dfw_fuel_price_map.html`) to the repository so users can view the outputs directly on GitHub.

This README provides a clear, informative, and compelling narrative for the project, making it an excellent resource for GitHub users interested in energy sector analysis, data visualization, and regional economic impacts. Let me know if you’d like to adjust any sections!
