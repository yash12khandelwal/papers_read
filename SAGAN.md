### title

Self Attention Generative Adversial Network

### tl;dr

Paper discusses about the use of Self-Attention for image generation in both Generator and Discriminator of the GAN model. It also discusses stablizing the training of GAN by using spectral normalization and TTUR for regularized discriminator.

### Describe the Method

Understanding the use of Self Attention for image generation:

	Convolution processes the information in a local neighbourhood, thus using convolution layer along is computationally inefficient for modelling long range dependencies in an image.
	Attention drives long range depedency modelling for image generation tasks.

	Traditional GAN generate high resolution details as a function of only spatially local points in lower rersolution feature maps. Image net GAN model excels at synthesizing image classes with full structure constaints, it fails to capture geometric or structural patters that occur consistently in some classes

	Self attention exhibits better balance between the ability to model long range dependency, computatuinal and statiscal efficiency.

	Self attention calculates response at a position as a weighted sum of the features at all positions where the weights or attention vectors are calculated with only a small computational cost.

Stabalizing the training of GAN:

	Recent evidence shows that the conditioning of the generator is an important causal facotor in GAN performance. It prevent the escalation of parameter magitude and avoid unusual gradients.

	Proposed enforcing good conditioning of GAN generators using spectral normalization that has previously been applied only to discriminator.

	Earlier method proposed limiting the spectral normalization fo the weight matrices in the discriminator in order to constrain tht Lischitz constant of discriminator function.

	Spectral normalization does not require extra hyperparamter tuning, moreover low computational cost.

Method:

	Image features from the previous hidden layer are first transformed into 2 feature spaces; f and g to calulate the attention.

	The term beta indicates the extent to which the model attends to the ith location when synthesizing the jth region. It is the attention map which is then multiplied with the feature space h and gives self-attention map(Oj) by 1 * 1 convolution over the matrix multiplication output.

	The final output is prepared by multiplying the attention map with a scale factor and adding back to the input feature map.

	Initially scaling factor in initalized as 0, this allows the network to firstly rely on the cues in locacl neighbourhood since this is easier for model to learn and the gradually learn to assing more weight to the non local evidence. Intution is that we want to learn the easy task first and then progressively incrase the complexity of the task.

	While implementing spectral normalization for both generator and discriminator it has been emprirically found that it is possible to use fewer discriminator updates per generator update. This significantly reduces the cost of training. This approach shows more stable training.

	They also propose using sperate learning rates (TTUR) for discriminator and generator specifically to compensate the problem of slow training of regularized discriminator, also making it possible to use fewer discriminator steps per generator steps.
	

	
![Attention map formula](https://github.com/yash12khandelwal/papers_read/blob/master/images/SAGAN.png)

![Self-Attention Map formula](https://github.com/yash12khandelwal/papers_read/blob/master/images/SAGAN1.png)

![Model architecture](https://github.com/yash12khandelwal/papers_read/blob/master/images/SAGAN2.png)

### Any further details

Achieves SOTA performance on class conditional image generation on Image Net.

Spectral normalization involves replacing the weight(W) by W/sigma(W). Calulation of Sigma(W) involves power iteration whose proof is explained the Spectral Normalization paper.

### My two cents

The method of stabalizing GAN training can be tested as most of the time GAN training is unstable and requires too much computation and time. 
State of the art papers are nowdays using Attention layers, using such method can be useful in getting good results
