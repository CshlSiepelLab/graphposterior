# graphposterior

A Python package for analyzing [BEAM (Bayesian Evolutionary Analysis of Metastasis)](https://github.com/CshlSiepelLab/beam) output as well as other method outputs that provide a posterior distribution of tissue migration graphs in a nexus tree file format.

<div style="text-align: left;">
  <img src="graphposterior.png" alt="example outputs" width="250"/>
</div>

## Installation

The main python package can be installed using pip after ensuring installation of python 3.10 (the python version requirement is only due to the dated ete3 dependency, so we plan to remove this later):

```bash
git clone https://github.com/CshlSiepelLab/graphposterior.git
cd graphposterior
pip install -e .
```

If you want to use the simulation related functionalities, then you must compile the `simulate` executable that relies on a c++ code base for efficient agent based model generation of a metastatic cancer population. To compile this executable, obtain a gcc compiler if you do not already have one available and then run the following from within `graphposterior/simulator_cpp`:
```
mkdir build
cd build
cmake ..
make
```

The compilation does rely on [LEMON](https://lemon.cs.elte.hu/trac/lemon) that should be handled by the provided pre-installed package, or otherwise can be installed from source and the `CMakeLists.txt` updated to find the correct path.

This should produce an executable file at `./graphposterior/simulator_cpp/build/simulate` which is essential for the simulation process and needs to be added to your `PATH` to access it from within the python package functions. It is also possible to just use this agent based model directly without the crispr barcode overlay if desired, just run `simulate --help` to see the options. 

## Basic usage for interpreting posterior results

```python
from graphposterior import PosteriorResults

# Initialize with output files
results = PosteriorResults(
    trees_file = "examples/data/example.trees", 
    log_file = "examples/data/example.log", 
    primary_tissue="LL",
    total_time=54,
    cores=1
    )

# Get information about the loaded data
results.info()

# List available parameters
parameters = results.get_parameters()

# Get statistics for a parameter
results.get_parameter_stats(parameters[10])

# Plot parameter distributions
results.plot_parameters(parameter=parameters[10], output_file="examples/output/param.pdf")

# Plot the mean rate matrix as a heatmap
results.plot_rate_matrix(output_file="examples/output/rate_matrix.pdf")

# Get consensus graph
results.get_consensus_graph(output_file="examples/output/probability_graph.csv")

# Plot consensus graph with probability weighted edges
results.plot_probability_graph(output_file="examples/output/probability_graph.pdf")

# Plot consensus graph with edges above threshold included
results.plot_thresholded_graph(threshold=[0.5, 0.75, 0.90], output_file_prefix="examples/output/thresholded_graph")

# Calculate mutual information based on a migration count matrix from traversing the posterior trees
results.compute_posterior_mutual_info(output_file_matrix="examples/output/mutual_info_matrix.csv", output_file_information="examples/output/mutual_info.txt")

# Sample and plot individual posterior tree samples as tree, graph, and timing plots
results.sample_and_plot_trees(n=2, output_prefix="examples/output/posterior_tree_sample")

# Record and plot metastasis times across all posterior samples
metastasis_times = results.get_metastasis_times(output_prefix="examples/output/metastasis_timing")
```

## Basic usage for simulating data

To simulate metastatic cancer data with overlayed crispr barcode data after installing the package, check that you have the executable available with `which run_met_cancer_barcode_simulation` and then run the following in the terminal for a default simulation run:
```
run_met_cancer_barcode_simulation
```
To view options to modify the simulation run from defaults, then run `run_met_cancer_barcode_simulation --help`.

## Basic usage for other functions

There are many other functions available, most of which are called by seperate analysis pipelines in producing results for the BEAM methods paper. These functions just need to be imported explicitly such as `from graphposterior.module import function` where module is the name of the script in `graphposterior/` with the function wanted.

## License

This project is licensed under the MIT License.

