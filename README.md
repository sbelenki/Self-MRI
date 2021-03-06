# Self-MRI (Self-Supervised Learning for Accelerated MRI Image Reconstruction)

## About  

### Abstract  

*Magnetic resonance imaging (MRI) has long scan times
which makes it both costly and prohibitive for some patients.
By not sampling the entire spatial frequency both the
scan time and the cost can be reduced. When scans are converted
from frequency domain (k-space) to spatial domain,
artifacts such as Shannon-Nyquist aliasing can appear due
to this sub-sampling. Deep Neural Networks (DNN) can be
used in a supervised setting scenario to perform an imageto-
image reconstruction, from the sub-sampled scan to a
full scan, but this would require ground-truth fully-sampled
data which is expensive to obtain. Using self-supervised
learning combined with transfer learning we were able to achieve 60% wall-time
speedup for the image reconstruction process measured in
total training time, comparing to the fastMRI benchmark,
with matching performance on 4x-acceleration test data.  
**Keywords-** self-supervised learning, transfer learning, spatial
domain, frequency domain*

## Dataset

Download the dataset from the [fastMRI dataset page](https://fastmri.med.nyu.edu/).  This project relies on single-coil knee dataset.

## Source Code

Facebook AI Research (FAIR) fastMRI and VISSL frameworks were used in this project.  
The modified clones of these frameworks may be accessed here:  
[fastMRI](https://github.com/sbelenki/fastMRI)  
[VISSL](https://github.com/sbelenki/vissl)  
[Additional run_pretrained scripts](https://github.com/sbelenki/run_pretrained)  

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

### Some pre-training results

Segmentations obtained by PIRL after 100 and 200 epochs of pre-training:

![PIRL segmentations after 100 and 200 epochs](https://github.com/sbelenki/Self-MRI/blob/master/images/pirl_pretrained/pirl_PT_100eps-200eps_comparison.png?raw=true)

Colorized MRI segmentations generated by PIRL after 200 epochs of pre-training:

![PIRL colorized segmentations after 200 epochs](https://github.com/sbelenki/Self-MRI/blob/master/images/pirl_pretrained/pirl_PT_200epochs_color_file1000000_index20.png?raw=true)

The images are courtesy of [David Schmidlin](https://github.com/dschmidlin)

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

## See also

[fastMRI](https://github.com/facebookresearch/fastMRI): collaborative research project from Facebook AI Research (FAIR) and NYU Langone Healt.  
  
[NYU Langone Health](https://fastmri.med.nyu.edu/): fastMRI dataset  
  
[VISSL](https://github.com/facebookresearch/vissl): Facebook AI Research (FAIR) VIsion library for state-of-the-art Self-Supervised Learning research with PyTorch  
  
## Citing Self-MRI

If you find Self-MRI useful in your research or wish to refer to this project, please use the following BibTeX entry.

```BibTeX
@misc{gatech2021selfmri,
  author =       {Claudiu Comsa and Yuriy Lichman and David Schmidlin and Sergei Belenki},
  title =        {Self-MRI},
  howpublished = {\url{https://github.com/sbelenki/Self-MRI}},
  year =         {2021}
}
```


