# satellite-object-detection

This project requires **three separate virtual environments** because each phase or component has different (and sometimes conflicting) dependencies.

## Environments Overview

1. **`venv_preprocessing`**: Used for data exploration (EDA), dataset validation, and labeling.   
2. **`venv_azure_infra`**: Used for tasks related to deployment or infrastructure setup in Microsoft Azure.
3. **`venv_train`**: Used for training the model with Tensorflow Model Garden. 

Each environment is **isolated** so that libraries with conflicting versions can coexist across the different phases of the project without causing issues.

---