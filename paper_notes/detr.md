# [DETR:End-to-End Object Detection with Transformers](https://arxiv.org/abs/2005.12872) 

_June 2020_

tl;dr: Transformer used for object detection as direct set prediction .

#### Overall impression
Formulate the object detection problem as direct set prediction problem. No need for engineering-heavy anchor boxes and NMS. 

The attention mechanism from Transformer is similar to [Non-local Networks](non_local_net.md). The attention has perfect memory and has same "distance" between any two points in the image.

#### Key ideas
- Training:
	- Two step process. Bipartite matching loss + Prediction loss. (the second term is called "Hungarian loss" which is quite confusing)
	- The bipartite matching forces a 1-to-1 matching, without missing.
	- Null padding of training set to N.
- DETR infers a fixed size set of N predictions. Predicts normalized center coordinates wrt the input image. 
- Resizing feature map HxWxd to dx(HW), as a sequence of feature dim d and length of HW. 
- Object query can be learned with SGD. It is part of the model's weight. It is better to be learned at attention.
- Decoding output is non-autoregressive parallel decoding (feed previous output to the decoder to get next output).
- Need extremely long training (300 epochs) to converge, vs 1x = 12 epochs for Faster RCNN.

#### Technical details
- Matching loss + Hungarian loss
	- $L_{matching} = -\mathbb{1}(c_i \neq \varnothing)p(c_i) + \mathbb{1}(c_i \neq \varnothing) L(y_{bbox}, \hat{y}_{bbox})$
	- $L_{Hungarian} = -p(c_i) + \mathbb{1}(c_i \neq \varnothing) L(y_{bbox}, \hat{y}_{bbox})$
	- Note that the class mask in Hungarian loss is gone. This is needed to suppress false positive or near-duplicates.
- [Generalized IoU loss](giou.md) for scale invariant and more robust alternative to IoU or Dice loss.
- Seems like object detectors usually do not use Adam nor dropout. ("We are not aware of successful applications of ...")
- Key findings for explainability of DETR:
	- Encoder is able to separate individual objects. 
	- Decoder typically attends to object extremities.
	- The visualization of bbox prediction of various output slots in DETR demonstrates that each slot attends to a particular type of objects. This is highly intriguing, similar to the prototype masks in [YOLACT](yolact.md).
- Uses updated AdamW for training.

#### Notes
- [Youtube explanation](https://www.youtube.com/watch?v=T35ba_VXkMY)

