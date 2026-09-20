# Investigation of the relationship between Arctic fox reproduction, estimated from den activity, and the lemming population

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

