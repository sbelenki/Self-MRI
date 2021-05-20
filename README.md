# Self-MRI

## Dataset

Download the dataset from the [fastMRI dataset page](https://fastmri.med.nyu.edu/).  This project relies on single-coil knee dataset.

## Generating Baseline

* Navigate to fastMRI directory
* Follow instructions in `README.md` to set up environment

To train the example model, provide path to single-coil dataset:

```
python ./fastmri_examples/unet/train_unet_demo.py
  --data_path /path/to/dataset
  --gpus 4
```

To test the model:

```
python ./fastmri_examples/unet/train_unet_demo.py
  --data_path /path/to/dataset
  --gpus 1
  --mode test
```

## Training SSLs with U-Net and U-Net++

* Navigate to `vissl` directory

### Setup Environment

* Follow instructions in `INSTALL.md`

### Run Models

The helper shell scripts for each of the SSL's have all appropriate command-line arguments.

The scripts can be run on command line from top-level directory `vissl`

* swav.sh
* pirl.sh
* rotnet.sh
* simclr.sh

The outputs (logs and torch checkpoints) will be produced into appropriate `checksum_...` folders.

### View Logs and Stats

To use tensorboard install it using `pip install tensorboard` command and point to your tb_logs or lightning_logs directory as following:  

```
tensorboard --logdir <path to your logs>
```

After that you will be able to launch your browser at `http://localhost:6006/` and see L1 training loss for the baseline, or SSL specific loss during pre-training, and fastMRI specific metrics for the baseline and fine-tuning phases.

### Fine-tuning

Run modified train_unet_demo.py from the `Self-MRI/run_pretrained` folder and start finetuning using the following line as an example for SwAV:

```
python train_unet_demo.py \
--data_path /path/to/data/ \
--max_epochs 10 \
--gpus 4 \
--output_path /output/of/finetuned/model \
--unet_module nestedunet \
--state_dict_file path/to/your/model \
```
This procedure will fine-tune your pre-trainded model.


## Testing and Inferenece 

* Navigate to `run_pretrained` directory

To run the inference on the final models (U-Net or U-Net++), use `run_pretrained_unet_inference.py` script.  The output is a directory of H5 files, each corresponding to the appropriate input test file.

For example:

```
python run_pretrained_unet_inference.py \
  --device cpu \
  --unet_module unet \
  --state_dict_file pirl.torch \
  --output_path ./results \
  --data_path /path/to/data/test \
```

To generate test image(s) for the final models, use `createImageFromH5.py`:

```
python createImageFromH5.py  --h5file ./results/reconstructions/file1000000.h5
```
