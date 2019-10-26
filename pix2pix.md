### Title

[Image-to-Image Translation with Conditional Adversial Networks](https://arxiv.org/pdf/1611.07004v3.pdf)

### tl;dr

Investigate conditional GAN as a general purpose solution for image to image translation problems.

### Describe the Method

Background Discussion:

	GAN not only learn mapping from input image to output image, but also learn a loss function to train this mapping. It provides a high level goal like to make the ouput indistinguishible from reality and then automatically learn a loss function appropriate for satisfying this goal.
	In analogy to automatic language tranlation, we define automatic image to image translation as the task of translating on a possible representation of a scene into another given scene provided sufficient traning data.

	GAN are generative models that learn mapping from random noise z to output image y
	Conditional GAN learn a mapping from observed image x and random noise z to output y

	Purpose of the paper is to develop a common framework for all the seperate tasks like Segmentation, Image Generation, Colorization which involve special purpose mahcinery.

Structured Loss for Image Modeling:

	Image to Image translation problem are often formulated as per pixel classification or regression problem. These formulations treat the output space as unstructured in the sense that each output pixel is considered conditionally independent from all other given the input image. Conditional GAN instead learn a structured loss which penalizes the joint configuration of the ouptut.

	If we take a naive approach and ask the CNN to minimize the euclidean distance between predicted and ground truth pixels, it will lead to produce blurry results. Reason is Euclidean distance is minimized by averaging all plausible output, which causes blurring.

	Previous approaches found it beneficial to mix GAN objective with more traditional loss such as L2 distance. In this case discriminator job remains unchanged but the generator is tasked to not only fool the discriminator but also to be near the ground truth in an L2 sense. 
	Here they explore L1 distance rather than L2 as L1 encourage less blurring.

	It is well known the the L2 loss and L1 produce blurry results on image generation problems. Although these losses fail to encourage high frequency crispiness, nevertheless they can model low frequency information.
	This motivates GAN discriminator to only model high frequency structure and relying on a L1 term to force low frequency correctness.

![cGAN loss function](https://github.com/yash12khandelwal/papers_read/blob/master/images/pix2pix1.png)

![cGAN + L1 loss function](https://github.com/yash12khandelwal/papers_read/blob/master/images/pix2pix.png)

Network Architecture:

	For generator we use U-NET bases architecture and for discriminator we use Convolutional Patch-GAN classifier, which only penalizes structure at the space of image patches.

	U-Net is used because in image translation problem there is a great deal of low level information shared between input and output and it would be desirable to shuttle the information directly across the net.
	Example provided in paper says that in case of image colorization input and output share the location of prominent edges.

	In order to model high frequency it is sufficient to restrict our attention to local image patches only. Therefore, we use Patch GAN that only penalizes structure at the scale of patches. This runs discriminator convolution across the image, averaging all the responses to provide the ultimate output.

Optimization:

	Alternate between 1 gradient descent step on discriminator D and then on generator G
	Rather than minimizing log(1 -D) term, maximizing log(D) term.
	Dividing objective by 2 while optimizing Discriminator D, which slows the rate at which discriminator D learns relative to generator G

Inference:

	At inference time, they use dropout at test time, and apply batch normalization using the statistics of the test batch rather than aggregated statistics of the training batch. 

### Any further details

Batch normalization when the batch size is 1 and that uses the test statistics is called Instance Normalization.

Many earlied papers said that without noise z the net could only learn a mapping from input X to output y but that would produce deterministic output and therefore fail to match any distribution other that delta function.
They did not found this strategy effective because the generator simply learned to ignore the noise. Instead they added noise in the form of dropout and used that at both training and test time.

### My two cents

The idea seems to be promising that instead of having a seperate architecture for sub tasks have a common architecture.
The intution of using U-Net as generator and adding L1 loss along with the GAN loss in image translation looks promising too.
