<!--<h3><b>Colorful Image Colorization</b></h3>-->
## <b>Colorful Image Colorization</b> [[Project Page]](http://richzhang.github.io/colorization/) <br>
[Richard Zhang](https://richzhang.github.io/), [Phillip Isola](http://web.mit.edu/phillipi/), [Alexei A. Efros](http://www.eecs.berkeley.edu/~efros/). In [ECCV, 2016](http://arxiv.org/pdf/1603.08511.pdf).

### (+ automatic colorization functionality for Real-Time User-Guided Image Colorization with Learned Deep Priors. Zhang, Zhu, Isola, Geng, Lin, Yu, Efros. SIGGRAPH 2017)

![Teaser Image](http://richzhang.github.io/colorization/resources/images/teaser4.jpg)

**[Sept20 Update]** Since it has been 3-4 years, I converted this repo to be minimal test-time functionality in PyTorch. I also added our SIGGRAPH 2017 (it's an interactive method but can also do automatic).

### Quick and easy start

Clone the repository.

```
git clone https://github.com/richzhang/colorization.git
git checkout pytorch
pip install requirements.txt
```

This script will colorize an image. The results should match the images in the `imgs_out` folder.

```
python demo_release.py -i imgs/ansel_adams3.jpg
```

The following loads pretrained colorizers. See [demo_release.py](demo_release.py) for some details on how to run the model. There are some pre and post-processing steps: convert to Lab space, resize to 256x256, colorize, and concatenate to the original full resolution, and convert to RGB.

```python
import colorizers
colorizer_eccv16 = colorizers.eccv16().eval()
colorizer_siggraph17 = colorizers.siggraph17().eval()
```

### Original implementation functionality

The original implementation contained train and testing, our network and AlexNet (for representation learning tests), as well as representation learning tests. It is in Caffe and is no longer supported. Please see the **caffe** branch for it.

### Citation ###

If you find this model useful for your resesarch, please cite with these bibtexs.

```
@inproceedings{zhang2016colorful,
  title={Colorful Image Colorization},
  author={Zhang, Richard and Isola, Phillip and Efros, Alexei A},
  booktitle={ECCV},
  year={2016}
}

@article{zhang2017real,
  title={Real-Time User-Guided Image Colorization with Learned Deep Priors},
  author={Zhang, Richard and Zhu, Jun-Yan and Isola, Phillip and Geng, Xinyang and Lin, Angela S and Yu, Tianhe and Efros, Alexei A},
  journal={ACM Transactions on Graphics (TOG)},
  volume={9},
  number={4},
  year={2017},
  publisher={ACM}
}
```

### Misc ###
Contact Richard Zhang at rich.zhang at eecs.berkeley.edu for any questions or comments.
