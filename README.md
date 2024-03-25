# replicate_car_paper
code to replicate car paper


<p align="center"><img src="./conjoint_analysis_and_ideal_point_design/data_conjointanalysis/disentanglement_example.png" alt="disentanglement_example" style="display: block; margin-left: auto; margin-right: auto; width: 100%; text-align: center;" > </p>

## Market Structure Mapping with Visual Characteristics
Code to replicate results in, "Market Structure Mapping with Visual Characteristics."  

If you find this work useful, please use the following citation information in plaintext or BibTeX format:

Sisodia, A, Kumar, V and Burnap, A (2024). Market Structure Mapping with Visual Characteristics. Marketing Science (under review).

```
@article{sisodia2024marketstructure,
  title= Market Structure Mapping with Visual Characteristics},
  author={Sisodia, A, Kumar, V and Burnap, A},
  journal={Marketing Science (under review)},
  volume={XXX},
  number={XXX},
  pages={XXX},
  year={2024},
  publisher={XXX}
}
```
This code will reproduce Figures and Tables of the paper.

## Installation
#### Libraries
This codebase was built using the Pytorch neural net framework.  However, it also depends on over 100 other Python packages, as well as GPU libraries including CUDA runtimes and cuDNN kernel support. We can not guarantee this code will run and replicate unless the same environment and library versions are used.  To install these packages (including the same library versions), please use the following:

    conda env create -f marketstructure_env.yml

#### Data
To download the dataset, please use the following links. Copy these files to `./hyperparameter_selection/data/watches/` and `./post_model_search/data/watches/` directories.

* #### [Watches Dataset](https://www.dropbox.com/scl/fo/akj3w8pat0lg1fa4ax480/h?rlkey=5d4ykq5br3kzkwarhi4ld4na8&dl=0)

## Replication Steps

#### Step 1: Run BLP Demand Model

1. Change directory to `./blp_before_disentanglement` and estimate the BLP demand model by executing `python pyblp_code.py`.
2. Execute `Rscript helper_script.R` to produce `exp_python_image_table.csv`.
3. Move  `exp_python_image_table.csv` to `../dataset_creation`.

#### Step 2: Dataset Creation

1. Change directory to `./dataset_creation`.
2. Unzip `jpeg_files.zip`.
3. Execute `python npz_file_creation.py`.

#### Step 3: Grid Search for Hyperparamaters

Go to `./hyperparameter_selection` and run disentanglement model with a unique $\beta$, $\delta$, and supervisory signal combination with 10 different seeds. Vary $\beta$, $\delta$, and supervisory signal combination.

For example, in the below command, the seed is set to 1, $\beta$=18, $\delta$=50, and the supervisory signal is brand. The model name is `brand_s1`. 

```
python main.py --sup_signal brand -s 1 --name brand_s1 --btcvae-B 18 --btcvae-M 50
```

In the above command, seed, $\beta$, and $\delta$ is a scalar value. This codebase, specific to the watch dataset, supports the following set of discrete supporting signals. Using any other name will result in an error.

```
discreteprice
brand
circa
material
movement
discreteprice_brand
discreteprice_circa
discreteprice_material
discreteprice_movement
brand_circa
brand_material
brand_movement
circa_material
circa_movement
material_movement
discreteprice_brand_circa
discreteprice_brand_material
discreteprice_brand_movement
discreteprice_circa_material
discreteprice_circa_movement
discreteprice_material_movement
brand_circa_material
brand_circa_movement
brand_material_movement
circa_material_movement
discreteprice_brand_circa_material
discreteprice_brand_circa_movement
discreteprice_brand_material_movement
discreteprice_circa_material_movement
brand_circa_material_movement
discreteprice_brand_circa_material_movement
```

The above command will create a directory `results/<model-name>/` which will contain:

* **model.pt**: The model at the end of training.
* **specs.json**: The parameters used to run the program (default and modified with CLI).
* **train_losses.csv**: All (sub-)losses computed during training on the train and validation dataset.
* **test_losses.log**: All (sub-)losses computed at the end of training on the test1 and test2 dataset. 
* **filename_test1.csv**: filenames of all watches in the test1 dataset. 
* **filename_test2.csv**: filenames of all watches in the test2 dataset. 
* **filename_train.csv**: filenames of all watches in the train dataset. 
* **mean_params_test1.csv**: mean visual characteristics of all watches in the test1 dataset. 
* **mean_params_test2.csv**: mean visual characteristics of all watches in the test2 dataset. 
* **mean_params_train.csv**: mean visual characteristics of all watches in the train dataset. 

Select the value of $\beta$ and $\delta$ for each supervisory signal at which the average supervised loss across 10 seeds on the test1 dataset is lowest. The supervised loss on the test1 set is stored as `sup_loss_test` in the first json object in the filename ending in `test_losses.log` in the directory `results/<model-name>/` for each combination of seed, $\beta$, $\delta$, and the supervisory signal. 

#### Step 4: Comparison of Different Supervisory Signals

Go to `./post_model_search` and run disentanglement model at the optimal $\beta$ and $\delta$ for each supervisory signal combination at 10 different seeds. 

For the watch dataset, execute the commands listed in `execute_step2.txt` to use the values listed in the paper. For example, execute the following command:

```
python main.py --sup_signal brand_circa_movement -s 10 --name brand_circa_movement_s10 --btcvae-B 50 --btcvae-M 1
```

The above command will create a directory `results/brand_circa_movement_s10/` which will contain:

* **model.pt**: The model at the end of training.
* **specs.json**: The parameters used to run the program (default and modified with CLI).
* **train_losses.csv**: All (sub-)losses computed during training on the train and validation dataset.
* **test_losses.log**: All (sub-)losses computed at the end of training on the test1 and test2 dataset. 
* **brand_circa_movement_s10_filename_test1.csv**: filenames of all watches in the test1 dataset. 
* **brand_circa_movement_s10_filename_test2.csv**: filenames of all watches in the test2 dataset. 
* **brand_circa_movement_s10_filename_train.csv.csv**: filenames of all watches in the train dataset. 
* **brand_circa_movement_s10_mean_params_test1.csv**: mean visual characteristics of all watches in the test1 dataset. 
* **brand_circa_movement_s10_mean_params_test2.csv**: mean visual characteristics of all watches in the test2 dataset. 
* **brand_circa_movement_s10_mean_params_train.csv**: mean visual characteristics of all watches in the train dataset. 

```
python main_viz.py --name brand_circa_movement_s10
```

The above command will create **brand_circa_movement_s10_posterior_traversals.png** (Figure 5 and Figure G1a) in `results/brand_circa_movement_s10/`.
```
python main_viz.py --name circa_s10
```
The above command will create **circa_s10_posterior_traversals.png** (Figure G1b) in `results/circa_s10/`.
```
python main_viz.py --name unsupervised_s10
```
The above command will create **unsupervised_s10_posterior_traversals.png** (Figure G1c) in `results/unsupervised_s10/`.
```
python main_viz.py --name ae_s10
```
The above command will create **ae_s10_posterior_traversals.png** (Figure G2a) in `results/ae_s10/`.
```
python main_viz.py --name vae_s10
```
The above command will create **vae_s10_posterior_traversals.png**: (Figure G2b) in `results/vae_s10/`.

#### Step 5: UDR Calculation

Copy the files stored in `results/<model_name>/` directory with the filename ending in `mean_params_test2.csv` to the `calculate_udr` folder. 

Go to `./calculate_udr` to compare the UDRs for different supervisory signals. Switch to an R environment and execute the Rscript `udr_calculation.R` with the supervisory signal as the argument. For example:

```
Rscript udr_calculation.R --sup_signal='brand'
```

The results will be appended to `filenamed udr.log`. It will replicate results in Table F.1 of the paper.

## Computing Resources

We used 64G GPU to run each model instance of 100 epoch, 64 batch size, 5e-4 learning rate. Each model run takes 8 minutes. We train 10 random seeds * 32 supervisory signal combinations * 25*15 grid values = 120,000 models. This would mean 16000 hours of model training.

## Citation

Feel free to use this code for your research. If you find this code useful, please use the following citation information in plaintext or BibTeX format:

Sisodia, A, Burnap, A, and Kumar, V (2024). Generative Interpretable Visual Design: Using Disentanglement for Visual Conjoint Analysis. Journal of Marketing Research (under review).

```
@article{sisodia2024disentangelment,
  title= Generative Interpretable Visual Design: Using Disentanglement for Visual Conjoint Analysis},
  author={Sisodia, A, Burnap, A, and Kumar, V},
  journal={Journal of Marketing Research (under review)},
  volume={XXX},
  number={XXX},
  pages={XXX},
  year={2024},
  publisher={XXX}
}
```

## Acknowledgments
Portions of this codebase were built on elements from the following open source projects, please see their respective licenses and cite accordingly:
* [disentangling-vae](https://github.com/YannDubs/disentangling-vae)
