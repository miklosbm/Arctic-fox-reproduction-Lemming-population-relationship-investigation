# Arctic-fox-reproduction-Lemming-population-relationship-investigation
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

Since neither variable is normally distributed, Spearman's correlation was used as the main measure. Pearson is also shown, mainly as a cross-check rather than a separate result.

Combining Arctic fox natal den counts with estimated lemming population density (1995–2019, n = 25 years) shows a clear positive correlation:

| Comparison | Pearson r | Pearson p | Spearman ρ | Spearman p |
|:---:|:---:|:---:|:---:|:---:|
| Same-year (lemming(t) vs. dens(t)) | 0.663 | 0.0003 | 0.752 | <0.0001 |
| Lagged (lemming(t-1) vs. dens(t)) | 0.109 | 0.6109 | 0.053 | 0.8066 |

Years with more lemmings tend to have more active fox dens, and this same-year link is fairly strong. **One thing worth noting about the p-values above:** both variables are measured year by year, and lemming populations are known to rise and fall in cycles of about 3–5 years. This means one year isn't fully independent from the next — but the standard statistical tests used here assume it is. In practice, this means the data behaves more like a smaller number of truly independent years than the raw count of 25 suggests — so these p-values are probably a bit more confident than they should be. The relationship itself still lines up with what's known about Arctic predator-prey cycles — it's really just the precision of the p-values that comes with this caveat, not the overall finding.

A 1-year lag test (checking whether last year's lemming numbers predict this year's den activity) found no meaningful link, suggesting fox reproduction reacts to the current year's lemming numbers rather than lagging behind. That said, this null result doesn't rule out longer lags — especially given the multi-year cycle mentioned above.

Of course, there are other factors that could matter too — weather, climate change, disease, and other predators. It would be an oversimplification to say fox reproduction depends on lemmings alone. Still, this project's specific goal was to check the relationship between just these two variables. Future work could look into:

1. **A closer look at the time trends** — a 1-year lag didn't show anything, but since lemming cycles often run 3–5 years, it's worth testing longer lags, building a full autocorrelation/cross-correlation (CCF) plot with a properly adjusted significance range, or fitting a real time-series model. This would also help address the independence issue mentioned above.
2. **Adding climate/weather data** — lemming population crashes in the Arctic are closely tied to snow cover and how harsh the winter is. Bringing in public climate data for Bylot Island could help explain the population swings.
3. **Predictive modeling** — moving from "is there a link" to "can we actually predict den activity from lemming trends."
