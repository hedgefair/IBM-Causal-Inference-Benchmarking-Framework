# Causal Inference Benchmarking Framework
Framework for evaluating causal inference methods.

 - [Overview](#overview)
 - [Getting Started](#getting-started)
   - [Prerequisites](#prerequisites)
   - [Installation](#installation)
   - [Usage](#usage)
 - [Citing](#citing)
 - [License](#license)
 - [Authors](#authors)

## Overview
Causality-Benchmark is a library developed by IBM Research Haifa for 
benchmarking algorithms that estimate the causal effect of a treatment on 
some outcome. The framework includes unlabeled data, labeled data, and code 
for scoring algorithm predictions. It can benchmark predictions of both 
population effect size and individual effect size.  

Currently, the framework contains one essential dataset, 
a feature matrix that is derived from the 
[linked birth and infant death data](https://www.cdc.gov/nchs/nvss/linked-birth.htm),
and the labeled and unlabeled data are simulated models of the 
treatment assignment, treatment effect and censoring data based on it.  
More details regarding the data can be found in the [LBIDD README file](data/LBIDD/README.md).

However, the evaluation script is not bounded to the provided data, 
and can be used on other data as 
long as some basic requirements are kept regarding the formats. 
Full technical details regarding the calculated metrics and the formats of the 
labeled and unlabeled data will be published soon through a related manuscript. 
Meanwhile, most of the details can be found in the 
[2018 Causal Inference Challenge](https://www.synapse.org/ACIC2018Challenge)
website.

Please note that due to GitHub limitation, only a sample of the data is 
available in this repository. However, you can manually access and download 
the entire dataset from the 
[framework's corresponding data repository](https://www.synapse.org/IBMCausalityData) 
located on the Synapse sharing platform.
Furthermore, since the benchmarking tool is used in the 
[Causal Inference Challenge 2018](https://www.synapse.org/#!Synapse:syn11294478),
the dataset currently includes a handful of example data with labels.
The full set of labeled data will be available when the challenge ends.

## Getting Started
### Prerequisites
Causality-Benchmarking is a Python 3.x library with some backward support for Python 2.7.x.  
The code heavily depends on pandas and requires:
* pandas >= 0.20.3
* numpy >= 1.13.1
* future >= 0.16.0 (for Python 2 compatibility)

### Installation
#### Using git clone
This will clone the entire repository first, so you would have both the data and the unittests as well,
on top of the evaluation scripts of the library. This way you could use your tools on the benchmark's
data also.
```bash
git clone https://github.com/IBM-HRL-MLHLS/IBM-Causal-Inference-Benchmarking-Framework.git
cd IBM-Causal-Inference-Benchmarking-Framework
python setup.py install
```

#### Using pip 
This will only install the evaluation scripts of the library and will include neither the tests
nor the data. Use this option in case you only want to score using the evaluation metrics.
```bash
pip install git+https://github.com/IBM-HRL-MLHLS/IBM-Causal-Inference-Benchmarking-Framework.git
``` 

### Usage
#### Evaluation
The evaluation script can be used either from a command line or from inside another Python
script.  
##### Command-line API
```bash
$ cd IBM-Causal-Inference-Benchmarking-Framework/evaluattion
$ evaluate PATH_TO_PREDICTION_OUTPUT PATH_TO_COUNTERFACTUAL_FILES_DIRECTORY
```
Type `evaluate -h` for the full manual.

##### Python module API
```python
from causalbenchmark.evaluate import evaluate
PATH_TO_PREDICTION_OUTPUT = "/SOME/PATH/TO/YOUR/ESTIMATES" 
PATH_TO_COUNTERFACTUAL_FILES_DIRECTORY = "/SOME/PATH/TO/GROUND/TRUTH/DATA" 
scores = evaluate(PATH_TO_PREDICTION_OUTPUT, PATH_TO_COUNTERFACTUAL_FILES_DIRECTORY)
```

##### Population vs individual prediction
The default behaviour of the scoring script is to evaluate the average treatment effect 
in the population.
In case the user wish to estimate individual effect size, one should add the `individual` flag:  
```bash
$ evaluate PATH_TO_PREDICTION_OUTPUT PATH_TO_COUNTERFACTUAL_FILES_DIRECTORY --i
``` 
```python 
scores = evaluate(PATH_TO_PREDICTION_OUTPUT, PATH_TO_COUNTERFACTUAL_FILES_DIRECTORY,
                  individual_prediction=True)
```
##### Expected Files
* The counterfactual outcomes files (holding $y^1$, $y^0$ for each individual), are expected to be a
  directory with different comma-separated-files and their file names corresponding to the
  data-instance but having some suffix (e.g. `"_cf.csv"`).
* The predictions for population effect size are expected to be one comma-delimited-file with
  every row corresponding to a different data-instance.
* The prediction for individual effect size are expected to be a directory containing different
  comma-delimited-files, each corresponding to a data-instance and each containing the
  estimated outcome under no-treatment and under positive treatment.

For full explanation, please refer to the menuscript.

#### Estimation
To avoid inflating file sizes for nothing, 
we supply one main covariate file and multiple files containing simulated treatment 
assignment and simulated outcome based on the main covariate matrix.    
An observed dataset, to apply causal inference methods on, can be achieved by compiling 
the covariate matrix and the simulated matrix together. This is done by a simple 
*inner join*.  
A python generator is provided to iterate over all simulated files, combine them with
the covariate matrix into one complete observed dataset so user can obtain causal estimations
from.
```python
from causalbenchmark.utils import combine_covariates_with_observed
COVARIATE_FILE_PATH = "/SOME/MAIN/COVARIATE/FILE.csv"
FACTUAL_FILE_DIR = "/SOME/PATH/TO/DIRECTORY/WITH/FACTUAL/FILES"
for observed_dataset in combine_covariates_with_observed(COVARIATE_FILE_PATH,FACTUAL_FILE_DIR):
    causal_effect_estimations = apply_my_awesome_model(observed_dataset)
```
 
## Citing
If you use either the data, the evaluation metrics or the evaluation code, please cite this 
repository as follows ([bibtex format](https://zenodo.org/record/1163587/export/hx#.WnHnPq6WY-U)):
```
Ehud Karavani, Yishai Shimoni, & Chen Yanover. (2018, January 31). 
IBM Causal Inference Benchmarking Framework (Version v1.0.0). 
Zenodo. http://doi.org/10.5281/zenodo.1163587
```

## License
The current content is open source under Apache License 2.0. For full specification see: 
[License.txt](License.txt)

## Authors
* Yishai Shimoni ([Homepage](http://researcher.watson.ibm.com/researcher/view.php?person=il-YISHAIS))
* Chen Yanover ([Homepage](http://researcher.watson.ibm.com/researcher/view.php?person=il-CHENY))
* Ehud Karavani ([Github](https://github.com/ehudkr))
 
