# WASP Summer School 2022 - Workshop on Speech Synthesis

[cmuarctic_link]: https://umeauniversity-my.sharepoint.com/:u:/g/personal/nich0037_ad_umu_se/Edi0a9f2A3BGpmCI78YFqsEBBlcWAfxURocQgl9M1UDSPg?e=w8Q842
[github_new_issue_link]: https://github.com/shivammehta007/Neural-HMM/issues/new
[docker_install_link]: https://docs.docker.com/get-docker/
[tacotron2_link]: https://github.com/NVIDIA/tacotron2
[pretrained_model_link]: https://umeauniversity-my.sharepoint.com/:f:/g/personal/nich0037_ad_umu_se/Eh6sGtTfca9GukqZkLne0tABQSuPXLU-tdUtUlr-53e3Xg?e=lIMPzN
[hifigan_all]: https://umeauniversity-my.sharepoint.com/:f:/g/personal/nich0037_ad_umu_se/Eib8mOgmPXFGi0P9E3gCFbEBRNtHE04UUuCCa7yTf2etTQ?e=wMdQ3I
[pytorch_lightning_link]: https://github.com/PyTorchLightning/pytorch-lightning
[pytorch_dataloader_issue_link]: https://github.com/pytorch/pytorch/issues/57273
[nvidia_toolkit_link]: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html


## Installations
1. Clone this repository ```https://github.com/NitheshChandher/Neural-HMM.git``` 
2. Initalise the submodules ```git submodule init; git submodule update```
3. Make sure you have [docker installed][docker_install_link] and running. 
   * Follow the [instructions][nvidia_toolkit_link] to setup Nvidia Docker.
4. Download our [pre-trained LJ Speech models][pretrained_model_link] 
5. Download HiFi gan pretrained [HiFiGAN models and config file][hifigan_all] and place them on the `hifigan` folder
6. Run ``bash start.sh`` and it will install all the dependencies and run the container.
7. Run jupyter notebook and open ```speech_synthesis_workshop.ipynb```.

## Finetune on CMU Arctic dataset
1. Download and extract the [CMU Arctic dataset][cmuarctic_link] in the `data` directory. Also, move the files `train.txt` and `validation.txt` to the `data/filelists` directory.
2. Check `src/hparams.py` for hyperparameters and set GPUs.
    1. For multi-GPU training, set GPUs to ```[0, 1 ..]```
    2. For CPU training (not recommended), set GPUs to an empty list ```[]```
    3. Check the location of transcriptions
3. Once hparams are updated run `python generate_data_properties.py` to generate `data_parameters.pt` for your dataset.
4. Run ```python train.py -c checkpoints/Neural-HMM(Female).ckpt``` to fine tune the pre-trained LJ Speech model on the CMU Arctic dataset.
    1. Checkpoints will be saved in the `hparams.checkpoint_dir`.
    2. Tensorboard logs will be saved in the `hparams.tensorboard_log_dir`.
5. To resume training, run ```python train.py -c <CHECKPOINT_PATH>```


## Miscellaneous
### Mixed-precision training or full-precision training
* In ```src.hparams.py``` change ```hparams.precision``` to ```16``` for mixed precision and ```32``` for full precision.
### Multi-GPU training or single-GPU training
* Since the code uses PyTorch Lightning, providing more than one element in the list of GPUs will enable multi-GPU training. So change ```hparams.gpus``` to ```[0, 1, 2]``` for multi-GPU training and single element ```[0]``` for single-GPU training.


### Known issues/warnings

#### PyTorch dataloader
* If you encounter this error message ```[W pthreadpool-cpp.cc:90] Warning: Leaking Caffe2 thread-pool after fork. (function pthreadpool)```, this is a known issue in [PyTorch Dataloader][pytorch_dataloader_issue_link]. 
* It will be fixed when PyTorch releases a new Docker container image with updated version of Torch. If you are not using docker this can be removed with ```torch > 1.9.1```


## Citation information
If you use or build on this method or code for your research, please cite the paper:
```
@inproceedings{mehta2022neural,
  title={Neural {HMM}s are all you need (for high-quality attention-free {TTS})},
  author={Mehta, Shivam and Sz{\'e}kely, {\'E}va and Beskow, Jonas and Henter, Gustav Eje},
  booktitle={Proc. ICASSP},
  year={2022}
}
```
## Acknowledgements
The code implementation is based on [Nvidia's implementation of Tacotron 2][tacotron2_link] and uses [PyTorch Lightning][pytorch_lightning_link] for boilerplate-free code.
