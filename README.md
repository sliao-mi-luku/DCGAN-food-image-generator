# GAN-food-image-generator
Use GANs to generate food images (with Kaggle's Food-101 dataset)


## (10/01/2020) This project is still in progress... 


**Original papers**

**GAN**\
I. J. Goodfellow et al. (2014) **Generative adversarial nets** [[arXiv]](https://arxiv.org/abs/1406.2661)

**DCGAN**\
A. Radford et al. (2016) **Unsupervised representation learning with deep convolutional generative adversarial networks** [[arXiv](https://arxiv.org/abs/1511.06434)]


---
# The Dataset
In this repo I used the **Food-101 dataset on Kaggle** [[kaggle dataset]](https://www.kaggle.com/kmader/food41)

Because of my local desktop's memory/computation capacity, I used the subset **food_c101_n10099_r64x64x3.h5**, which consists of 10,999 colored images of size 64x64 pixel^2

Please go to the Kaggle link above to download the dataset.

I trained my network on **Google Colab**. After starting an notebook and setting the GPU runtime, upload the file **food_c101_n10099_r64x64x3.h5** to the current work directory.

To access to the dataset, simply use the following commands:

``` python3
import h5py

# dataset filename
filename = "food_c101_n10099_r64x64x3.h5"

food_dataset = h5py.File(filename, 'r')
print(food_dataset.keys())

category = np.array(food_dataset['category'])
category_names = np.array(food_dataset['category_names'])
orig_data = np.array(food_dataset['images'])

# check shape
orig_data.shape

```

The dataset consists of 3 keys: `images`, `category`, and `category_names` 

`food_dataset['images']` gives a (10099, 64, 64, 3) array of the pixel values of all images.\
`food_dataset['category']` gives the one-hot coded vector of the category\
`food_dataset['category_names']` gives a list of food names in the entire dataset\

To visualize the category of *an image* (index = `idx`):

``` python3
item_name = category_names[category[idx]][0].decode('utf-8')
```

To visualize an image (index = `idx`) and its category:

``` python3
import matplotlib.pyplot as plt

idx = 56 # or pick your own number

item_name = category_names[category[idx]][0].decode('utf-8')

plt.figure()
plt.imshow(orig_data[idx])
plt.title(item_name)
plt.axis('off')
plt.show()
```

---
# Model Atchitectures

In this repo, I implemented 2 models of GANs using different types of layers/architectures.

The first model uses deep fully connected layers as a benchmark model.

The second model (DCGAN) I used deep convolutional layers.


## Model 1 - fully connected layers

### The Generator

The generator consists of 4 fully connected layers:

**Inputs**: an noise vector (**z**) of dimension (hidden_dim) = 100

**FC1**: 512 units, LeakyReLU activation with alpha = 0.2, and batch normalizaion (momentum = 0.9)\
**FC2**: 1024 units, LeakyReLU activation with alpha = 0.2, and batch normalizaion (momentum = 0.9)\
**FC3**: 2048 units, LeakyReLU activation with alpha = 0.2, and batch normalizaion (momentum = 0.9)\
**FC4 (output layer)**: hxwx3 units (the size of the fake image, including the rgb dimension) and the **tanh** activation

**Optimizer**: Adam with learning rate = 2e-4, beta_1 = 0.5

### The Discriminator

The discrimanator also consists of 4 fully connected layers:

**Inputs**: the image of shape (64, 64, 3)

**FC1**: 2048 units, LeakyReLU activation with alpha = 0.2\
**FC2**: 1024 units, LeakyReLU activation with alpha = 0.2\
**FC3**: 512 units, LeakyReLU activation with alpha = 0.2\
**FC4 (output layer)**: 1 unit and the **sigmoid** activation

**Optimizer**: Adam with learning rate = 2e-4, beta_1 = 0.5

### Hyperparameters
I trained the networks for 100,000 epochs. At each epoch a batch of 128 (`batch_size`) of real food images are sampled from the food dataset. Another batch of 128 fake images are generated by the generator by pass an input of shape (`batch_size`, 100) noises (uniformly random from -1 to 1).

### Results



# Model 2 - DCGAN (deep convolutional layers)

**ref** https://gluon.mxnet.io/chapter14_generative-adversarial-networks/dcgan.html

### The Generator
The generator consists of 4 transposed convolutional (Conv2DT) layers:

**Inputs**: an noise vector of dimension (`hidden_dim`) = 100\
**FC**: 4x4x1024 (16384) units, no activation, the output is reshaped to (4, 4, 1024) (`channels_last`)\
**Conv2DT-1**: 512 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Conv2DT-2**: 256 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Conv2DT-3**: 128 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Conv2DT-4**: 3 units, filter size = 5, stride = 2, padding = 'same', **tanh** activation, and batch normalizaion\

**Optimizer**: Adam with learning rate = 2e-4, beta_1 = 0.5

### The Discriminator
The generator consists of 4 convolutional (Conv2D) and 1 dense layers:

**Inputs**: an image of shape (64, 64, 3)\
**Conv2D-1**: 128 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Conv2D-2**: 256 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Conv2D-3**: 512 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Conv2D-4**: 1024 units, filter size = 5, stride = 2, padding = 'same', **LeakyReLU** activation, and batch normalizaion\
**Flatten**: flatten to a 1-D vector (1024 units)
**FC**: 1 unit, sigmoid activation to determine true (1) or fake (0)\

**Optimizer**: Adam with learning rate = 2e-4, beta_1 = 0.5



