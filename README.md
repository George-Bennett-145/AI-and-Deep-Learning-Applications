# ENGI 4607 AI and Deep Learning Applications 4 coursework

This repo contains my ENGI 4607 summative practical coursework. The aim was to classify two diabetes related eye diseases

- Diabetic Retinopathy DR  
- Diabetic Macular Edema DME  

using both structured clinical data plus biomarkers, and OCT B scan images.

The work follows the three tasks from the coursework brief and is written up in the report. The notebook is set up to reproduce the results.

## Project overview

The dataset contains, per eye and per visit, three main things

- Clinical labels such as BCVA and CST  
- 16 binary biomarkers graded by a clinician  
- OCT imaging data  

The key thing is avoiding leakage between train and test sets by splitting by Eye ID, because the same eye appears across visits.

## What is in the notebook

The main notebook runs end to end and is organised exactly by task.

### Task 1 supervised learning, visualisation, feature importance

Inputs tested separately and then combined

- Clinical labels only  
- Biomarkers only  
- Combined clinical labels plus biomarkers  

What I did

- Visualisation using UMAP to see separation between DR and DME
- Trained and compared two models  
  - Support Vector Machine with multiple kernels and a manual grid search  
  - A feed forward neural network with Optuna hyperparameter optimisation  
- Evaluation using accuracy, precision, recall, F1, ROC AUC
- Feature importance using SHAP, then used the most important features later in Task 3

Key takeaway from the feature importance step  
CST and BCVA came out as the main drivers, with Biomarker 11, Biomarker 2, and Biomarker 7 also contributing.

### Task 2 CNN on OCT images plus augmentation plus fine tuning

What I did

- Built a custom CNN from scratch and tuned architecture and hyperparameters with Optuna
- Tested data augmentation methods  
  - Rotation  
  - Flipping  
  - Scaling  
  - Gaussian noise  
  and limited ranges when they hurt performance
- Reported learning curves and full classification metrics
- Fine tuned VGG16 and compared different freezing depths

### Task 3 combining structured data and images

Two approaches

- Feature fusion  
  Add the most important numerical features after the CNN flattening stage  
- Ensemble learning  
  Average predictions from the best structured model and the CNN model  

## Results

Task 1
- SVM with combined features performed best overall and achieved strong ROC AUC and F1
- SHAP highlighted CST and BCVA as the top features

Task 2
- The augmented CNN improved validation performance versus the baseline model trained without augmentation
- Fine tuning VGG16 worked better when freezing more layers, which stabilised training and improved recall

Task 3
- Feature fusion improved validation accuracy versus using images alone
- The averaging ensemble outperformed the individual models, although this likely needs a fully independent test set to confirm generalisation

## Repository contents

- `BENNETT_George_ENGI4607.ipynb`  
  Main notebook containing all tasks, training, evaluation, and plots

## Data notes

- The dataset is not included in this repo.
- The code expects a CSV that includes at least  
  - `Path`  
  - `Disease Label`  
  - `Eye_ID`  
  - `BCVA` and `CST`  
  - biomarker columns such as `Biomarker 1` to `Biomarker 16`  
- The image pipeline assumes one image per row, and the notebook includes a step that copies and renames images into a clean folder for consistent loading.

## Reproducibility

- A fixed random seed is set in the notebook for repeatability.
- Splitting is done with grouping by `Eye_ID` to reduce leakage risk.

