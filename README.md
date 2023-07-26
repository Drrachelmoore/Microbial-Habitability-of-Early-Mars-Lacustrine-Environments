# Companion repository for the manuscript entitled _Microbial habitability of early Mars lacustrine environments sustained by iron redox cycling_

The purpose of this repository is to allow for the reproduction of our results utilizing the community genome-scale models.

## Requirements
* [Python](https://www.python.org/downloads/) (version 3.10 was used in the manuscript + the [PyCharm 2021.2.3 IDE](https://www.jetbrains.com/pycharm/))
* [COBRApy](https://github.com/opencobra/cobrapy) (version 0.25.0 was used in the manuscript)
* [Gurobi Optimizer](http://www.gurobi.com/registration/download-reg) for any simulations
* The model(s)


## Reading and simulating a model
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

**To generate the community model grwoth results in Table 2:**
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_1.xml")

# Set media to upper bounds of Gale Lake with light

```


