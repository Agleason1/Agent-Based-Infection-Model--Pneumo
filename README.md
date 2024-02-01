# *S. pneumoniae* and Hib agent-based infection models

## Model scenarios:

1. ABM_code_incrementing_vaccinations_to_current_(last 12 years).ipynb <br>
Model of the last 12 years in which vaccination coverage was increased to current levels.

2. ABM_code_no_vaccinations_(last 12 years).ipynb <br>
Model scenario for the last 12 years if vaccination was not implemented (counterfactual).

3. ABM_code_incrementing_vaccinations_to_90%_coverage_(next 12 years).ipynb <br>
Model scenario for the next 12 years in which vaccination coverage is uniformly increased.


## *S. pneumoniae* and Hib data used to tune the models:

1. antibiotics_childhood_vaccination.csv <br>
Vaccine efficacy against antibiotic use for *S. pneumoniae*, Hib
2. district_populations_extrapolated.csv <br>
Extrapolated population per district, based on NFHS4 and NFHS5 population data)
3. estimated_current_prevalence.pkl<br>
Represents current *S. pneumoniae* prevalence, derived from model scenario 1
4. S. pneumoniae_incidence_estimates.csv<br>
2010 incidence of *S. pneumoniae* by state
5. vaccination_data_condensed.csv<br>
Vaccination coverage by state and wealth quintile for *S. pneumoniae*, Hib
6. wealthq_data_condensed.csv<br>
Proportion of state inhabitants per wealth quintile<br>

Other data used to tune the model is found in Supplemental Materials and is input directly in model scenario code.

## Dependencies:
Julia version: 1.8.3 <br>
<br>
Julia Packages:<br>
Agents v5.6.2<br>
CSV v0.10.7<br>
DataStructures v0.18.13<br>
Distributions v0.25.79<br>
DrWatson v2.12.0<br>
Pickle v0.3.2<br>
Plots v1.36.4<br>
StatsBase v0.33.21<br>
IJulia v1.23.3 (for running Julia kernels in Jupyter Notebook)<br>
<br>
The Jupyter notebooks can be run following the installation of Anaconda. Julia's IJulia package allows loading Julia environments into Jupyter notebooks.

## Operating system used for simulations:
Windows 10 PC with 32 logical processors and 64 GB RAM; also tested on a standard desktop laptop (Lenovo T14) running Ubuntu 20.04 LTS. Each simulation takes approximately 1 hour to run on a standard laptop.

## Instructions for use:
This repository should be cloned to a local location. From there, model scenario notebooks can be run to replicate the results depicted in the study. The dependencies listed above should be installed. Models cannot be run with the current set parameters on a standard desktop computer due to memory constraints. We advise either reducing the number of agents in simulation (change the value of 'total_agents=150000' in the 'create_params' function in each notebook to a lower value) or using a computing resource with at least 64GB of memory.

The model outputs include estimated number of cases, deaths, and antibiotic use by welath quintile, state, and year given a rate of vaccination for pneumonia illnesses. Vaccination rates can be changes to the user's specifications if they choose to run additiona simulations.