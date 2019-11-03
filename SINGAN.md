### title

[SinGAN: Learning a Generative Model from a Single Natural Image](https://arxiv.org/pdf/1905.01164.pdf)

### tl;dr

Goal is to learn an unconditional generative model that captures the internal distribution of patches within the image and is then able to generate high quality diverse sample that carry the same visual content as the image.

### Describe the Method

Motivation:

	GANs have small receptive fields and limited capacity, preventing them from memorizing single image.

	Framework with pyramids of fully convolutinal GANs each resposible for learning the patch distribution as different scale of the image allows generating new samples of arbitary size and aspect ratio that have significant variability, yet maintainting both global structure and fine textures of the image.

	The effective receptive field for full image is typically 1/2 of the image height, hence generator G_N generates the general layout of the image and object structure. Each of the G_n (n < N) is responsible for adding the finer scale details.
	All the generators and discriminator have the same receptive field and thus captures structures of decreasing size as we go up the generation process.

Training and Architecture:

	Consists of hierarchy of patch-GAN.

	5 conv blocks Conv(3 X 3) - Batch Norm - Leaky Relu
	Start with 32 kernel per block at the coarsest scale and increase this number by a factor of 2 every 4 scales.
	At test time generate images of arbitary size and aspect ratio.

	Generation of an image sample starts at the coarsest scale and sequentially passes through all generators up to the finest scale, with noise injected at every scale.
	Each higher level accepts spatial noize along with an upsampled version of the image from coarser scale.

![Model Architecture](https://github.com/yash12khandelwal/papers_read/blob/master/images/SINGAN.png)
![Model Architecture 1](https://github.com/yash12khandelwal/papers_read/blob/master/images/SINGAN_1.png)

Loss:
	
	Adverserial Loss:

		Penalizes the distance between the distribution of patches and the distribution of generated images.

		Use the WGAN_GP loss which was found to increae the training stabililty.

		Instead of using this to single image GAN for textures, they have defined the loss over the whole image rather than over random crops which allows the network to learn boundary conditions.

	Reconstruction Loss:

		Ensures that there exists a specific set of input noise maps, which generates the orignal image x.

		The reconstructed image has another role during training,  which is to determine the standard deviation of the noise z_n in each scale. 
		They take that standard deviation to be proportional to RMSE between upsampled reconstructed image and orignal image which gives the indication of the amount of details that need to be added at that scale. 

### My two Cents

The use of pyramid network is increasing in the field of image generation and generating segmentation maps.
SOTA paper of semantic segmentation also uses the Pyramid Network.

This idea is new and presented in ICCV19 so can be explored more
