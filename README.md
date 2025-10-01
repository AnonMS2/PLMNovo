# Protein Language Model–Aligned Spectra Embeddings for De Novo Peptide Sequencing

## Setting Up the Environment
Create a new virtual Conda environment, called `plmnovo`, with the required libraries using the following commands:
```
conda create -n plmnovo python=3.10
conda activate plmnovo
pip install -r requirements.txt
```

## Running PLMNovo

### Toy Dataset
You can train PLMNovo on a small train/test dataset (provided under `data/sample`) by running the following command:
```
python plmnovo.py train data/sample/train.mgf --validation_peak_path data/sample/test.mgf
```
Due to the small size of the dataset (1000 training samples and 128 test samples), modify the parameter `val_check_interval` in `config.yaml` as well as `log_every_n_steps` in `denovo/model_runner.py` to ensure proper validation and logging.

### Downloading the MSKB Dataset
The `download_mskb_data.sh` script can be used to download the MSKB datasets using for training and validating PLMNovo. The data will be downloded into `data/mgf_data/` by running
```
bash download_mskb_data.sh
```

### Training on the MSKB Dataset
Once the dataset is downloaded, run the following command to train PLMNovo:
```
python plmnovo.py train data/mgf_data/mskb_final/mskb_final.train.mgf --validation_peak_path data/mgf_data/mskb_final/mskb_final.test.mgf
```
Runs will be logged into [Weights and Biases](https://wandb.ai/site), and the best models (with the lowest validation loss) will be saved under `checkpoints`. You can modify the PLMNovo hyperparameters in `config.yaml`.

## Evaluating the Trained Models
The model checkpoints saved under `checkpoints` can be used for evaluation. As an example, you can evaluate a model `checkpoints/RUN_NAME/best_model.ckpt` on the multi-enzyme test set by running
```
python plmnovo.py evaluate data/mgf_data/multi_enzyme_simple/multi-enzyme-simple.test.mgf --model checkpoints/RUN_NAME/best_model.ckpt
```

## Alternative PLMs and Pooling Strategies
The current implementation of PLMNovo support two PLMs (ESM-2 8M and ESM-2 650M) and one pooling mechanism (average pooling). Alternative PLMs can be also be used with PLMNovo by modifying `denovo/model.py`, especially following similar steps as in lines 218-229. Also, alternative pooling mechanisms can be added to PLMNovo by updating the `pooling` method in `denovo/model.py`.

