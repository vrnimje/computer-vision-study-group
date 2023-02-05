# COMPUTER VISION NOTES - WEEK 3

### Edges, Features and Matching

- [1. Cross Relation vs Convolutions](#1-cross-relation-vs-convolutions)
- [2. Convolution operation properties](#2-convolution-operation-properties)
- [3. Edge Detection](#3-edge-detection)
  - [i. Taking derivative (Sobel)](#i-taking-derivative-sobel) 
  - [ii. Difference of Gaussians](#ii-difference-of-gaussians)
  - [iii. Canny Edge Detection](#iii-canny-edge-detection)
- [4. Feature Detection](#4-feature-detection) 
- [5. Matching Patches](#5-matching-patches)
  - [i. Matrix representation](#i-matrix-representation)
  - [ii. Types of Transformations](#ii-types-of-transformations) 

## 1. Cross relation vs Convolutions
 - Cross Relation: Basically the filter is applied as it is on the image
 - Convolution: The filter is rotated by 180 degrees, and then again flipped horizontally and vertically
    
    ![](https://i.imgur.com/TyWUSFl.png)

## 2. Convolution operation properties
 - Commutatively: $Filter * image = Image * filter$
 - Associativity: $(filter1 * filter2) * image = filter1 * (filter2 * image)$
 - Distributive over addition: $A*(B + C) = A * B + A * C$
 - Scalars: $X(A * B) = (XA) * B = A * (XB)$

 - For example, instead of running a 2-D (n $\times$ n) Gaussian filter, we can apply two 1-D Gaussian filters of dimesions (1 $\times$ n) and (n $\times$ 1)

  ![](https://i.imgur.com/W957SlQ.png)

All of CV is convolutions, mostly. 

## 3. Edge detection
 - ### i. Taking derivative (Sobel)
   - Rapid changes in the function, i.e., the image tensor
          
 ![](https://i.imgur.com/V8hThl7.png)
      
   - So we can take the derivative of this 'function', to detect the edges
   - But images do contain noise, erratic spikes, and maybe picked up by the derivative
   - To overcome that, we can apply a Gaussian filter
   - So, derivative {estimated} * (Gaussian * Image)
   - Or using associativity, we combine the filters, to get the Sobel Edge detection kernel
        
 ![](https://i.imgur.com/3Yi3kTh.png)
        
   - But edges will exist in any direction, and hence, we want edges where the gradient is high and low
   - So we apply second derivative, where we check points where the curve passes zero
   - To estimate, we apply the Laplacians, and then get the following
        
 ![](https://i.imgur.com/qXmCh7U.png)
        
   - Laplacians are again prone to noise (caused by dust for eg.), so we apply Gaussian, to get the LoG filter. Good approximation for 5x5 to 9x9
        
 ![image](https://user-images.githubusercontent.com/103848930/216812881-eaed95e7-4814-44a1-adaa-01bdc5cf70ec.png)


 - ### ii. Difference of Gaussians: 
   - Edges are considered as high frequency changes, and hence we find edges of specific size, i.e., frequency (DoG)
   - Gaussian: Low pass, strongly reduces components before threshold (standard deviation)

 ![](https://i.imgur.com/UKGuThu.png)

 ![](https://i.imgur.com/nL9YIgQ.png)

   Larger the frequency changes, more color info will be produced, and the edges become less sharper
          
 ![](https://i.imgur.com/hufhOIh.png)
          
- ### iii. Canny Edge detection
   - Just taking the value of first derivative directly
   
     ![image](https://user-images.githubusercontent.com/103848930/216814766-a054598f-1915-4245-b1c1-d2db0bcecaee.png)
          
   - But initially returns fuzzy approximation. We want the exact edges
   - Refers to Canny Edge Detection.

     ![image](https://user-images.githubusercontent.com/103848930/216814810-554cdee1-0ff5-4729-a025-aade12181452.png)
 
   - Steps 
     - Gaussian
     - Sobel Filter
     - Supress pixels which aren't max, applying interpolation for pixels that come in between two existing pixels, when the edge is angled

      ![image](https://user-images.githubusercontent.com/103848930/216814851-1451b012-f2da-4264-bf31-5e444e8bda18.png)

   - 2 thresholds, 3 conditions (strong, weak and no edges) to check range of the suppressed, as we want strong edges to be connected by the weak ones, if and only if they in the neighbourhood
   - Increasing std. deviation will give us only more defined edges

 ![](https://i.imgur.com/S3KbXuY.png)

## 4. Feature detection: 
 - Highly descriptive local regions, and describing those regions
 - Used for matching, detection etc.
 - Finding patches in image that are useful or have meaning
 - Good features are unique. 
 - Distance between two patches

 ![](https://i.imgur.com/DYMKSDm.png)
        
  - Sky: Bad, as very little variation
    Edges: Ok, as variation in one direction
    Corners: Good, as variation in all directions
  - To find these unique patches, we can find distance between every pair of random patches, which is very computationally heavy
  - Instead, we can check auto correlation, i.e., checking with nearby patch at particular distance (self - differnece, i.e., how different it is)
       
 ![](https://i.imgur.com/glFbvKG.png)
        
  - For example, 
        
 ![](https://i.imgur.com/oaDlM7x.png)

  - But still lot of calculations required, and hence we can approximate using gradients
    - Gradients are mostly zero -> low self difference
    - Gradients are mostly in one direction -> mid self difference
    - Gradients in mostly 2 directions -> High self difference
  - So we make use of structure matrix
        
 ![](https://i.imgur.com/UY46RrR.png)
        
 ![](https://i.imgur.com/FNt7HM9.png)
 
  - Harris Corner detection
       
 ![](https://i.imgur.com/D7W7FE3.png)
        
 ![](https://i.imgur.com/S3ffgMh.png)
        
 ![](https://i.imgur.com/Yp1yfAn.png)
        
## 5. Matching Patches

![image](https://user-images.githubusercontent.com/103848930/216815120-9a5ae917-763f-4eb1-9eeb-285cd6611f24.png)

To represent the patches, we make use of pixel-level info. But doesn't work for highly rotated transformations

Complex descriptors include gradients, edges, context, etc..

   ### i. Matrix representation 
   Each point can be represented as a vector
   $x = [x \ y]^T$
    - To map a point to a new point, we can represent it using a matrix transformation as:
       $x' = Mx$
   ### ii. Types of Transformations
   - Scaling matrix: Just multiplying each coordinate with a constant
      
      ![image](https://user-images.githubusercontent.com/103848930/216814188-cf94235b-52dd-437b-a781-f6d9c7490d8a.png)
       
   - Translation: First create  $\bar{x} = [x \ y \ 1] ^T$, i.e., augmeneted vector
     ${x}' = [I | T] * \bar{x}$
     
     ![image](https://user-images.githubusercontent.com/103848930/216814273-aa8f1ab5-affc-4667-8680-3087f346d2cc.png)
            
   - Euclidean : Rotation + Transformation
      ${x}' = [R | T] * \bar{x}$
      where, 
      
     ![Screenshot from 2023-02-04 16-43-37](https://user-images.githubusercontent.com/103848930/216764157-4cc3d52b-7272-4bfa-b518-80d904f4160c.png)

      - If we want to combine multiple tranformations, we can multiply them, but they are 2 $\times$ 3 matrices. Hence, we add the row $O^T$ 1
        ![](https://i.imgur.com/74V9Hyn.png)
  
      - But for projection, a full 3-D transformation matrix will be required. So we represent the vector now as homogeneous coordinates
              
        ![Screenshot from 2023-02-04 16-46-00](https://user-images.githubusercontent.com/103848930/216764215-70450145-73d1-4ff8-b66e-74e2e53e67da.png)

         where, $\bar{x} = \tilde{x}/\tilde{w}$
  
   - Projective tranformation or Homography: 3D transformation, 

 ![](https://i.imgur.com/JC0mboy.png)
              
 ![](https://i.imgur.com/mIN282K.png)
              
 
