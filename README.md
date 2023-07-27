# Companion repository for the manuscript entitled _Microbial habitability of early Mars lacustrine environments sustained by iron redox cycling_

The purpose of this repository is to allow for the reproduction of our results utilizing the community genome-scale models.

## Requirements
* [Python](https://www.python.org/downloads/) (version 3.10 was used in the manuscript + the [PyCharm 2021.2.3 IDE](https://www.jetbrains.com/pycharm/))
* [COBRApy](https://github.com/opencobra/cobrapy) (version 0.25.0 was used in the manuscript)
* [Gurobi Optimizer](http://www.gurobi.com/registration/download-reg) for any simulations
* The model(s)


## General reading and simulating a model
The models provided in the manuscript both end in `.xml` and are in Systems Biology Markup Language ([SBML](https://sbml.org/)) format. 

**To read in the model and run a basic simulation:**
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model.xml")
solution = model.optimize()

# Will print only the solution to the objective function
print(solution)

# Will print more of a summary including the solution
print(model.summary(solution = solution))
```

## Table 2 from the manuscript
Table 2. Specific growth rates of the genome-based models under the specified conditions and constrained to either the low or high bounds of medium metabolite flux.
|Electron donor/acceptors | Input carbon source |Light?  | Model | Specific growth Rate (h-1) |
| ------------- | ------------- |------------- | ------------- |------------- |
| H2 / Fe | CO2  | NA  | _G. sulfurreducens_  | 0.001–0.005  |
| Fe / CO2 |  CO2  | - | _R. palustris_ | 0.00–0.00 |
| Fe / CO2  |  CO2  | +  | _R. palustris_  | 0.001–0.032  |
| H2, Fe / Fe, CO2  |  CO2  | +  | Community  | 0.001–1.031  |
| H2, Fe / Fe, CO2 |  CO2 | - | Community | 0.00–1.012 |

### To generate the community model growth results in Table 2:
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_1.xml")

# Set media to upper bounds of Gale Lake with light
mediumupper = {
    'EX_cpd29674_e0': 45,
    'EX_cpd00063_e0': 45,
    'EX_cpd00099_e0': 250,
    'EX_cpd00149_e0': 0.0059,
    'EX_cpd00011_e0': 41,
    'EX_cpd00058_e0': 0.0001,
    'EX_cpd10515_e0': 5.8,
    'EX_cpd00067_e0': 0.001,
    'EX_cpd11640_e0': 12,
    'EX_cpd00001_e0': 1000,
    'EX_cpd00205_e0': 4.4,
    'EX_cpd00254_e0': 60,
    'EX_cpd00030_e0': 0.01,
    'EX_cpd00528_e0': 13,
    'EX_cpd00971_e0': 120,
    'EX_cpd00009_e0': 1.8,
    'EX_cpd00048_e0': 72,
    'EX_cpd00034_e0': 0.0001,
    'EX_photon_e': 100.0
}


model.medium = mediumupper
sol = model.optimize()
print(sol)
# prints <Solution 1.031 at 0x2106b888f10>
# 1.031 h^-1

# Set media to lower bounds of Gale Lake with light
mediumlower = {
    'EX_cpd29674_e0': 24,
    'EX_cpd00063_e0': 24,
    'EX_cpd00099_e0': 110,
    'EX_cpd00149_e0': 0.001,
    'EX_cpd00011_e0': 2.3,
    'EX_cpd00058_e0': 0.0001,
    'EX_cpd10515_e0': 0.12,
    'EX_cpd00067_e0': 0.001,
    'EX_cpd11640_e0': 12,
    'EX_cpd00001_e0': 1000,
    'EX_cpd00205_e0': 1.4,
    'EX_cpd00254_e0': 35,
    'EX_cpd00030_e0': 0.00053,
    'EX_cpd00528_e0': 13,
    'EX_cpd00971_e0': 94,
    'EX_cpd00009_e0': 0.00052,
    'EX_cpd00048_e0': 44,
    'EX_cpd00034_e0': 0.0001,
    'EX_photon_e': 100.0
}
model.medium = mediumlower
sol = model.optimize()
print(sol)
# prints <Solution 0.001 at 0x17fd603ce80>
# 0.001 h^-1


# Now simulate without light
#First, with upper bounds

mediumupper["EX_photon_e"] = 0.0
model.medium = mediumupper
sol = model.optimize()
print(sol)

# Then, with lower bounds
mediumlower["EX_photon_e"] = 0.0
model.medium = mediumlower
sol = model.optimize()
print(sol)

# prints 
#<Solution 1.012 at 0x1da9a099780>
#<Solution 0.000 at 0x1da99d90ca0>
```
## Table 3 from the manuscript
Table 3. Modeled interactions between community members. 
|Metabolite| _R. palustris_ (reaction ID) |_G. sulfurreducens_ (reaction ID) | 
| ------------- | ------------- |------------- | 
| Acetate | -6.83 / -144.4 (rxn05488)  | 6.83 / 144.4 (rxn05488)  | 
| Fe2+ |  -6.99/ -0.052 (rxn37614_2)  | 6.99 / 0.052 (FERCYT) | 
| Fe3+  |  6.99 / 0.052 (rxn37614_2)  | 6.99 / -0.052 (FERCYT) |
| Citrate  |  -2.81 / 63.88 (rxn05211)  |  2.81 / -63.88 (rxn05211)  |
| CO2 |  -3.21 / -95.74 (rxn05467) |  0.91 / 55.21 (rxn05467)  |
* Fluxes are shown in mmol gDW-1 h-1 for low / high bounds of media inputs with light. Negative values indicate metabolite consumption, whereas positive values indicate production.


### To reproduce the modeled interactions between community members in Table 3:
```
# Follow the same instructions above to read in model and media bounds

model.medium = mediumupper #setting media to upper bounds
sol = model.optimize()

#print(sol)
#print(model.summary(solution = sol))
print("Acetate upper")
#print(model.metabolites.get_by_id("cpd00029_e0").summary(solution = sol))
print("Fe2+  upper")
print(model.metabolites.get_by_id("cpd10515_e0").summary(solution = sol))
print("Fe3+  upper")
print(model.metabolites.get_by_id("cpd10516_e0").summary(solution = sol))
print("Citrate  upper")
print(model.metabolites.get_by_id("cpd00137_e0").summary(solution = sol))
print("CO2  upper")
print(model.metabolites.get_by_id("cpd00011_e0").summary(solution = sol))

model.medium = mediumlower #setting media to lower bounds
sol = model.optimize()

#print(sol)
#print(model.summary(solution = sol))
print("Acetate lower")
#print(model.metabolites.get_by_id("cpd00029_e0").summary(solution = sol))
print("Fe2+ lower")
print(model.metabolites.get_by_id("cpd10515_e0").summary(solution = sol))
print("Fe3+ lower")
print(model.metabolites.get_by_id("cpd10516_e0").summary(solution = sol))
print("Citrate lower")
print(model.metabolites.get_by_id("cpd00137_e0").summary(solution = sol))
print("CO2 lower")
print(model.metabolites.get_by_id("cpd00011_e0").summary(solution = sol))

```

## Dynamic flux balance analysis 
```

```

## Lipid biomarker production
```

```

## Diurnal community model
```

```
