<!--<h3><b>Colorful Image Colorization</b></h3>-->
## <b>Colorful Image Colorization</b> [[Project Page]](http://richzhang.github.io/colorization/) <br>
[Richard Zhang](https://richzhang.github.io/), [Phillip Isola](http://web.mit.edu/phillipi/), [Alexei A. Efros](http://www.eecs.berkeley.edu/~efros/). In [ECCV, 2016](http://arxiv.org/pdf/1603.08511.pdf).

![Teaser Image](http://richzhang.github.io/colorization/resources/images/teaser3.jpg)

### Overview ###
This repository contains:
 - (0) a test time script to colorize an image (python script)
 - (1) a test time demonstration (IPython Notebook)
 - (2) code for training a colorization network
 - (3) pre-trained AlexNet, used for representation learning tests (Section 3.2)
 - (4) links to our results on the ImageNet test set, along with a pointer to AMT real vs fake test code

### Clone this repository ###
Clone the master branch of the respository using `git clone -b master --single-branch https://github.com/richzhang/colorization.git`

### Dependencies ###
This code requires a working installation of [Caffe](http://caffe.berkeleyvision.org/) and basic Python libraries (numpy, pyplot, skimage, scipy). For guidelines and help with installation of Caffe, consult the [installation guide](http://caffe.berkeleyvision.org/) and [Caffe users group](https://groups.google.com/forum/#!forum/caffe-users).

### (0) Test-Time Python Script ###
We provide a script for colorizing a single image. Run `./models/fetch_release_models.sh` to download the model. Then, run `python ./colorize.py -img_in [[INPUT_IMG_PATH]] -img_out [[OUTPUT_IMG_PATH]]`. For example, try `python ./colorize.py -img_in ./demo/imgs/ILSVRC2012_val_00041580.JPEG -img_out ./out.png`.

### (1) Test-Time Demo in IPython Notebook ###
We also include demo usage as an iPython notebook, under [`./demo/colorization_demo_v2.ipynb`](https://github.com/richzhang/colorization/blob/master/demo/colorization_demo_v2.ipynb). This IPython Notebook demonstrates how to use our colorization network to colorize a grayscale image. To run this, after cloning the directory, `cd` into the `demo` directory, run `ipython notebook` and open `colorization_demo_v2.ipynb` in your web browser.

### (2) Training Usage ###
The following contains instructions for training a colorization network from scratch. After cloning the repository, from the root directory:

(1) Run `./train/fetch_init_model.sh`. This will load model `./models/init_v2.caffemodel`. This model was obtained using the k-means initialization implemented in [Kraehenbuehl et al, ICLR 2016](https://github.com/philkr/magic_init).

(2) Run `./train/fetch_caffe.sh`. This will load a modified Caffe into directory `./caffe-colorization`. For guidelines and help with installation of Caffe, consult the [installation guide](http://caffe.berkeleyvision.org/) and [Caffe users group](https://groups.google.com/forum/#!forum/caffe-users).

* Note that this is the same as vanilla-Caffe, with a `SoftmaxCrossEntropyLayer` layer added. You likely can add the layer to your current build of Caffe by adding the following files (found in the `./resources` directory) and re-compiling:
	`./src/caffe/layers/softmax_cross_entropy_loss_layer.cpp`
	`./src/caffe/layers/softmax_cross_entropy_loss_layer.cu`
	`./include/caffe/layers/softmax_cross_entropy_loss_layer.hpp`
If you do this, link your modified Caffe build as `./caffe-colorization` in the root directory and proceed.

(3) Add the `./resources/` directory (as an absolute path) to your system environment variable $PYTHONPATH. This directory contains custom Python layers.

(4) Modify paths in data layers `./models/colorization_train_val_v2.prototxt` to locate where ImageNet LMDB files are on your machine. These should be BGR images, non-mean centered, in [0,255].

(5) Run `./train/train_model.sh [GPU_ID]`, where `[GPU_ID]` is the gpu you choose to specify. Notes about training:

(a) Training completes around 450k iterations. Training is done on mirrored and randomly cropped 176x176 resolution images, with mini-batch size 40.

(b) Snapshots every 1000 iterations will be saved in `./train/models/colornet_iter_[ITERNUMBER].caffemodel` and `./train/models/colornet_iter_[ITERNUMBER].snapshot`.

(c) If training is interupted, resume training by running `./train/train_resume.sh ./train/models/colornet_iter_[ITERNUMBER].snapshot [GPU_ID]`, where `[ITERNUMBER]` is the last snapshotted model.

(d) Check validation loss by running `./val_model.sh ./train/models/colornet_iter_[ITERNUMBER].caffemodel [GPU_ID] 1000`, where [ITERNUMBER] is the model you would like to validate. This runs the first 10k imagenet validation images at full 256x256 resolution through the model. Validation loss on `colorization_release_v2.caffemodel` is 7715.

(e) Check model outputs by running the IPython notebook demo. Replace the release model with your snapshotted model.

(f) To download reference pre-trained model, run `./models/fetch_release_models.sh`. This will load reference model `./models/colorization_release_v2.caffemodel`. This model used to generate results in the [ECCV 2016 camera ready](arxiv.org/pdf/1603.08511.pdf).

For completeness, this will also load model `./models/colorization_release_v2_norebal.caffemodel`, which is was trained without class rebalancing. This model will provide duller but "safer" colorizations. This will also load model `./models/colorization_release_v1.caffemodel`, which was used to generate the results in the [arXiv v1](arxiv.org/pdf/1603.08511v1.pdf) paper.

### (3) Representation Learning models ###

(1) Run `./models/fetch_alexnet_model.sh`. load model `./models/alexnet_release_450000_nobn_fc_rs.caffemodel`. This model was used for the representation learning tests.

(2) You have two choices.

(i) If you do the color conversion into Lab space outside of the network, use prototxt `./models/alexnet_deploy_lab.prototxt`. The input blob will be an image in Lab color space

(ii) If you wish to do the color conversion inside of the network, use prototxt `./models/alexnet_deploy.prototxt`. The input should be BGR images, non-mean centered, in [0,255]. You will have to follow Caffe installation (described in step (2) in the previous section).

### (4) Results / Real vs Fake Test ###

To run the "real vs fake" Amazon Mechanical Turk test (Table 1 of the paper), see [this repository]( https://github.com/phillipi/AMT_Real_vs_Fake). See line 1 of the **Usage** section. Corresponding paths are: [Ours (full)](http://colorization.eecs.berkeley.edu/imgs/classrebal_turk_imgs_438000/), [Ours (class, no rebal)](http://colorization.eecs.berkeley.edu/imgs/classnorebal_turk_imgs_446000/), [Ours (L2)](http://colorization.eecs.berkeley.edu/imgs/regress_turk_imgs_534000/), [Ours (L2, ft from class)](http://colorization.eecs.berkeley.edu/imgs/regressft_turk_imgs_312000/), [Ground Truth](http://colorization.eecs.berkeley.edu/imgs/gt_imgs_0/).

### Citation ###
If you find this model useful for your resesarch, please use this [bibtex](http://richzhang.github.io/colorization/resources/bibtex_eccv2016_colorization.txt) to cite.

### Misc ###
Should you wish to share your colorizations with us, please email Richard Zhang with subject "MyColorization" at rich.zhang@eecs.berkeley.edu. Also contact Richard for any questions or comments.
