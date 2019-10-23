### Title
[Style and Content Disentaglement in Generative Adversial Networks (SCGAN)](https://arxiv.org/pdf/1811.05621.pdf)

### tl;dl

A new unsupervised algorithm for training GANs that learns disentangled style and content representations of the data.

### Describe the method

Based on the fundamental principle of image generation, which is the disentaglement of the scene's content and style.
Scene content -> geometry
Style -> texture and illumination

Used architecture:

	LSGAN (Least Squares Generative Adversial Network)
		Has 6 strided convolutional layers
		All the layers double the spatial size of their inputs except the first layer which quadruples the in input

	BiGAN (Bidirectional Generative Adversial Network) :  Unsupervised feature learning framework
		Makes use of encoder along with generator which maps data x to its latent representation z
		Discriminates jointly in the data and latent space

Network architecture:

	1 Generator block:
		4 sub blocks: 
			Decoder#1 -> input content code; uses LSGAN as baseline; all convolution layers are followed by IN insted of Batch Normalization
			MLP -> input style code; output AdaIN parameters; 5 fully connected layers with ReLu activation
			Style blocks -> input decoded image and AdaIn parameter; 4 Residual blocks with AdaIN normalization layers
			Decoder#2 -> input styled encoded information; output generated image; batch normalization used

	Generated image is passed to a dicriminator to find loss between real and fake images
	Generated image is also passed to a VGG19 block to find the content and style consistency loss
  
  ![Netowrk architecture](https://github.com/yash12khandelwal/important_papers/blob/master/images/Screenshot%20from%202019-10-23%2022-02-44.png)

Training SC-GAN:

	Take random code z (z_c[content code], z_s[style code]) as input.
	Network needs to maintain mechanism, if the z_c is unaltered then the content of the generated image should be unaltered and vice versa.

	Randomly generate 4 z
		z11 = (z_c1, z_s1)
		z21 = (z_c2, z_s1)
		z12 = (z_c1, z_s2)
		z22 = (z_c2, z_s2)

	To adopt SC-GAN by any GAN framework, the generator is the only part which needs to be modified. Discriminator could be left intact.

Loss function proposed:
	
	Content Consistency Loss or Perceptual Loss:
		Learn complex cross domain relationships.
		Uses VGG19 network as a loss network which defines a feature reconstruction loss that measures the difference in high level content between images.

	Style Consistency Loss:
		Peanlizes difference in style
		Squarerd Frobenious norm of the difference between the Gram Matrices of the output and target images.

	Diversity Loss:
		Reason of use is that using only Style and Content Consitency loss results in network to learn limited number of styles.
		Minimum distance in style or content matrices if two images do not share the same style or content code.

	Cycle Consistency Loss:
		It is used to check whether the proposed network can learn disentanged content and style representation.
		Measures the L2 distance between the encoded information from generated image and input code.

Experiments:

	Initial experiments were done using the above network as LSGAN as baseline network

	BiGAN encoder learns to invert the generated image which is used to observe whether proposed network can learn disentangled representation
	Two encoder for style and content retrieval are used.
	Content encoder uses several downsample strided convolution followed by IN layers.
	Style encoder follows similar structure but does not use IN layers as they remove the style information.
  
  ![BIGAN + LSGAN network architecture](https://github.com/yash12khandelwal/important_papers/blob/master/images/Screenshot%20from%202019-10-23%2022-07-23.png)

### Any further details

Instance Normalization performs a form of style normalization by normalizing the feature statistics, namely the mean and variance. In another words, it normalizes the input to a single style specified by it affine parameters.

![Formula](https://github.com/yash12khandelwal/important_papers/blob/master/images/Screenshot%20from%202019-10-23%2022-03-26.png)

Adapative Instance Normalization (AdaIN): Adjust the mean and variance of the content features with those of the style features. It has no affine parameter to learn, it simply scale and shift the normlaized content input by mean and standard deviation of style features.

![Formula](https://github.com/yash12khandelwal/important_papers/blob/master/images/Screenshot%20from%202019-10-23%2022-02-59.png)

Here mu(x) and sigma(x) are computed across spatial dimension independently for each channel and each sample

Replacing Batch Normalization layers with IN layers significantly improve the style transfer networks.

### My two cents

By fixing the style portion of the latent representation, we can generate diverse images in a particular style. Reversely, we can set the content code and generate a specific scene in a variety of styles. This can be used to create a more diverse dataset and can be used for a more generalized learning.
