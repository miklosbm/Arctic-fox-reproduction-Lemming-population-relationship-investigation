# Arctic-fox-reproduction-Lemming-population-correlation
This study compares Arctic fox reproductive success — estimated from den activity — with the estimated size of the lemming population, examining the correlation between the two in the Qarlikturvik Valley (Bylot Island, Nunavut).
## Citation & Data Sources:
1. Moisan, Louis; Bideault, Azenor; Gauthier, Gilles et al. (2025). Long-term abundance time-series of the High Arctic terrestrial vertebrate community of Bylot Island, Nunavut [Dataset]. Dryad. https://doi.org/10.5061/dryad.44j0zpcnt
2. Berteaux, B. 2020. Monitoring of arctic and red fox reproduction on Bylot Island, Nunavut, Canada, v. 1.1. Nordicana D49, doi:10.5885/45594CE-A69880E653314887.

## Repository Structure

```
main/
├── data/
│   ├── saved/
│   │   ├── .gitkeep                                              # Placeholder to keep folder in git
│   │   ├── habitat_areas.json                                    # Calculated habitat area data
│   │   ├── lemming_summary.csv                                   # Summarized lemming population data
│   │   └── natal_dens_per_year.csv                                # Natal den counts per year
│   ├── BYLOT-community_composition.csv                            # Community composition data (Bylot Island)
│   ├── BYLOT-species_density_monitoring.csv                       # Species density monitoring data (Bylot Island)
│   ├── MetadataS1.pdf                                              # Supplementary metadata documentation
│   ├── habitat_type.png                                           # Habitat type reference image/map
│   ├── nordicanad_arctic_and_red_fox_den_monitoring_data_1993-2019_berteaux.txt        # Arctic and red fox den monitoring data (1993–2019)
│   ├── nordicanad_arctic_and_red_fox_den_monitoring_data_1993-2019_berteaux_ReadMe.txt # Documentation for the above dataset
│   └── valley_orange_polygon_mask.png                              # Valley polygon mask image
├── den_monitoring_status.ipynb                                      # Notebook: den monitoring status analysis
├── habitat_area_extraction.ipynb                                     # Notebook: habitat area extraction
├── lemming_population_plot.ipynb                                     # Notebook: lemming population visualization
├── pipeline.ipynb                                                     # Main analysis pipeline notebook
├── pyproject.toml                                                     # Python project configuration
└── uv.lock                                                             # Dependency lock file (uv package manager)
LICENSE                                                                # Project license
README.md                                                              # Project overview and documentation
```
### Inputs/outputs
- valley_orange_polygon_mask.png and habitat_type.png — both loaded in pipeline.ipynb for the habitat area extraction step
- BYLOT-species_density_monitoring.csv — loaded in pipeline.ipynb for lemming density data
- nordicanad_arctic_and_red_fox_den_monitoring_data_1993-2019_berteaux.txt — loaded in den_monitoring_status.ipynb
- data/saved/*.csv and .json files — these are generated outputs from one notebook that get read back in by another (habitat_areas.json ← from habitat_area_extraction.ipynb;
- lemming_summary.csv, natal_dens_per_year.csv ← read into pipeline.ipynb and lemming_population_plot.ipynb)

## How to use/ short description

This project is a data science / data analysis project. The idea behind creating it was that I am really passionate about life sciences and working with data, so I wanted to build something interesting and close to my personality.

The main folder contains every file; directly in it there are four jupyter notebook files, namely:

- _pipeline.ipynb_ — main notebook: computes habitat areas, estimates lemming population, correlates it with fox den activity, runs stats.
- _den_monitoring_status.ipynb_ — visualizes fox den status over time and natal den counts per year.
- _habitat_area_extraction.ipynb_ — helper functions to compute habitat area (km²) from the map image. Not run standalone — imported into pipeline.ipynb via import_ipynb.
- _lemming_population_plot.ipynb_ — plots lemming population trend over time, log-scaled.

Computing order: 1. den_monitoring_status.ipynb, 2. pipeline.ipynb, 3. lemming_population_plot.ipynb

## License

This project is for personal/educational use. The underlying datasets remain subject to their original licenses — see the citations above.

## Environment
  - Python 3.12+
  - Managed with [uv](https://docs.astral.sh/uv/) - installation: https://docs.astral.sh/uv/getting-started/installation/ 
  - Find the pyproject.toml and uv.lock files in the main folder
### Setup
  ```bash
  uv sync
  uv run jupyter lab
  ```

## Results

Before interpreting the correlation results, normality of both variables was tested using the Shapiro-Wilk test:

| Variable | W | p-value | Normal distribution? |
|:---|:---:|:---:|:---:|
| Lemming population | 0.812 | 0.0004 | No |
| Arctic fox natal dens | 0.769 | 0.0001 | No |

Merging Arctic fox natal den counts with estimated lemming population density (1995–2019) shows a significant positive correlation:

| Comparison | Pearson r | Pearson p | Spearman ρ | Spearman p |
|:---:|:---:|:---:|:---:|:---:|
| Same-year (lemming(t) vs. dens(t)) | 0.663 | 0.0003 | 0.752 | <0.0001 |
| Lagged (lemming(t-1) vs. dens(t)) | 0.109 | 0.6109 | 0.053 | 0.8066 |

Given the non-normality, Spearman was treated as the primary measure here — and it confirms a statistically strong relationship: years with more lemmings tend to have more active fox natal dens.

A 1-year lag test (checking whether last year's lemming population predicts this year's den activity) showed no meaningful relationship, suggesting fox reproduction responds to the current year's lemming abundance rather than a delayed effect. However, the null lagged result doesn't rule out longer-lag effects.

Of course, there are many more environmental variables that could be taken into consideration — weather, global warming, disease, and other predators. In some ways, it would be an oversimplification to say that fox reproduction depends heavily on the lemming population alone; however, this project's aim was specifically to verify the relationship between the two. Further work could address these additional factors, including:

1. **Deeper time-series analysis** — a 1-year lag isn't predictive, but lemming cycles are often 3–5 years long, so it's worth testing longer lags, adding a full autocorrelation/cross-correlation (CCF) plot, or fitting a proper time-series model.
2. **Incorporating climate/weather data** — Arctic lemming crashes are strongly tied to snow cover and winter severity. Pulling in a public climate dataset for Bylot Island could help explain the population swings.
3. **Predictive modeling** — moving from "is there a correlation" to "can we predict den activity from lemming trends."

## About
This project is a data science / data analysis project. The idea behind creating it was that I am really passionate about life sciences and working with data, so I wanted to build something interesting and close to my personality.

Reproductive activity of animals is usually difficult to census directly, so researchers tend to use different types of approaches instead. One such approach is den monitoring, which is used to indirectly track the reproductive success of arctic foxes — a species whose breeding is closely tied to the population cycles of their main prey, lemmings. Lemming populations follow a boom-bust cycle, typically peaking and crashing every 3–4 years, and based on the literature predators respond strongly to this: in low-lemming years they often skip breeding altogether, while in peak years litter sizes can be much larger. This project investigates that predator-prey relationship quantitatively, using two independent long-term datasets collected in the Qarlikturvik Valley and surrounding sites on Bylot Island. The datasets mentioned below provided the following insights:

  **1. Fox den activity (1993–2019)** — annual counts of natal dens, distinguishing arctic fox and red fox use, based on twice-yearly den surveys _(Berteaux, 2020, Nordicana D49)_
  **2. Lemming population estimates** — density measurements (individuals/km²) for brown and collared lemmings across mesic and wetland habitats -_(Moisan et al., 2025, Ecology)_

## Steps
  1. Image-based habitat classification — preprocessing step to define the exact area of the valley, and specifically the wetland and mesic habitat areas, using a pixel-ratio and color-based (hue) classification approach.
  2. Using the lemming density data from Moisan et al. (2025, Ecology) together with the mesic and wetland areas calculated for the valley, the density values for each year were combined to obtain the estimated mesic and wetland lemming counts for both brown and collared lemmings.
  
<img width="3192" height="790" alt="kép" src="https://github.com/user-attachments/assets/b57578c5-aa72-4adb-affe-8db900cf8a58" />

  3. Based on Berteaux (2020, Nordicana D49), den activity was recorded for each year, which made it possible to count the natal dens per year for both arctic fox and red fox.
  
<img width="2787" height="1989" alt="kép" src="https://github.com/user-attachments/assets/69900704-f3ff-4e30-ab72-cb28dbe48229" />

<img width="3590" height="790" alt="kép" src="https://github.com/user-attachments/assets/7d891ab7-b624-49d5-bd37-9e5961cdff74" />

  4. The number of arctic fox natal dens and the total estimated lemming population were then compared, examining the correlation between the two time series.

  ## Normality Testing
  
  Before interpreting the correlation results, normality of both variables was 
  tested using the Shapiro-Wilk test:
  
  | Variable | W | p-value | Normal distribution? |
  |:---|:---:|:---:|:---:|
  | Lemming population | 0.812 | 0.0004 | No |
  | Arctic fox natal dens | 0.769 | 0.0001 | No |
  
  Both variables significantly deviate from a normal distribution (p < 0.05). 
  This is expected given the count-based, zero-inflated nature of den activity 
  data and the boom-bust dynamics of lemming populations. As a result, the 
  **Spearman correlation is considered the primary, more reliable measure** in 
  this analysis, while the Pearson correlation is reported alongside it for 
  comparison.
  
  ## Correlation Results

| Comparison | Pearson r | Pearson p | Spearman ρ | Spearman p |
|:---:|:---:|:---:|:---:|:---:|
| Same-year (lemming(t) vs. dens(t)) | 0.663 | 0.0003 | 0.752 | <0.0001 |
| Lagged (lemming(t-1) vs. dens(t)) | 0.109 | 0.6109 | 0.053 | 0.8066 |

Arctic fox natal den activity correlates strongly with lemming population size within the **same year**, but shows no significant relationship with the **previous year's** lemming population. This suggests foxes respond to prey availability within the current breeding season rather than with a one-year delay.

