# COMPUTER VISION NOTES - WEEK 3

### Edges, Features and Matching

- [1. Cross relation vs Convolutions](#1-cross-relation-vs-convolutions)
- [2. Convolution operation properties](#2-convolution-operation-properties)
- [3. Possible filters](#3-possible-filters)
  - [i. Edge detection](#i-edge-detection)
    - [Taking derivative](#taking-derivative)
    - [Difference of Gaussians](#difference-of-gaussians)
    - [Canny Edge detection](#canny-edge-detection)
  - [ii. Feature detection](#ii-feature-detection)
  - [iii. Matching patches](#iii-matching-patches)
    - [Matrix represenatation](#matrix-representation)
    - [Types of Transformations](#types-of-transformations)


- ## 1. Cross relation vs Convolutions
  - Cross Relation: Basically the filter is applied as it is on the image
  - Convolution: The filter is rotated by 180 degrees, and then again flipped horizontally and vertically
  ![](https://i.imgur.com/TyWUSFl.png)

- ## 2. Convolution operation properties
    - Commutatively: $Filter * image = Image * filter$
    - Associativity: $(filter1 * filter2) * image = filter1 * (filter2 * image)$
    - Distributive over addition: $A*(B + C) = A * B + A * C$
    - Scalars: $X(A * B) = (XA) * B = A * (XB)$

    - For example, instead of running a 2-D (n $\times$ n) Gaussian filter, we can apply two 1-D Gaussian filters of dimesions (1 $\times$ n) and (n $\times$ 1)

  ![](https://i.imgur.com/W957SlQ.png)

- All of CV is convolutions, mostly. 

- ## 3. Possible filters
    - ### i. Edge detection: 
      - #### Taking derivative
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
        ![](https://i.imgur.com/18aXE8H.png)

      - #### Difference of Gaussians: 
        - Edges are considered as high frequency changes, and hence we find edges of specific size, i.e., frequency (DoG)
          - Gaussian: Low pass, strongly reduces components before threshold (standard deviation)
          ![](https://i.imgur.com/UKGuThu.png)

            ![](https://i.imgur.com/nL9YIgQ.png)

          - Larger the frequency changes, more color info will be produced, and the edges become less sharper
          ![](https://i.imgur.com/hufhOIh.png)
          
      - #### Canny Edge detection
          - Just taking the value of first derivative directly
          ![](https://i.imgur.com/YfCUqew.png)
          - But initially returns fuzzy approximation. We want the exact edges
          - Refers to Canny Edge Detection. 
          ![](https://i.imgur.com/IR3tkLU.png)
              - Gaussian
              - Sobel Filter
              - Supress pixels which aren't max, applying interpolation for pixels that come in between two existing pixels, when the edge is angled
              ![](https://i.imgur.com/mUvt7Av.png)
              - 2 thresholds, 3 conditions (strong, weak and no edges) to check range of the suppressed, as we want strong edges to be connected by the weak ones, iff they in the neighbourhood
              - Increasing std. deviation will give us only more defined edges
          ![](https://i.imgur.com/S3KbXuY.png)

    - ### ii. Feature detection: 
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
    - ### iii. Matching Patches: 
        - To represent the patches, we make use of pixel-level info. But doesn't work for highly rotated transformations
        - Complex descriptors include gradients, edges, context, etc..
         ![](https://i.imgur.com/HRhDtYC.jpg)
        - #### Matrix representation: 
             Each point can be represented as a vector
            x = [x, y]$^T$
            - To map a point to a new point, we can represent it using a matrix transformation as:
            x' = Mx
        - #### Types of Transformations
            - Scaling matrix: Just multiplying each coordinate with a constant
              ![](https://i.imgur.com/wDjqNpN.png)
            - Translation: First create  $\bar{x}$ = [x y 1]$^T$, i.e., augmeneted vector
            ${x}' = [I | T] * \bar{x}$
            ![](https://i.imgur.com/wFrS14m.png)
            - Euclidean : Rotation + Transformation
            ${x}' = [R | T] * \bar{x}$
            where, 
            $$R = \begin{bmatrix}
       cos(\theta) & -sin(\theta) \\
       sin(\theta) & cos(\theta)
     \end{bmatrix}$$
             - If we want to combine multiple tranformations, we can multiply them, but they are 2 $\times$ 3 matrices. Hence, we add the row $O^T$ 1
               ![](https://i.imgur.com/74V9Hyn.png)
             - But for projection, a full 3-D transformation matrix will be required. So we represent the vector now as homogeneous coordinates
             $$\tilde{x} = \begin{bmatrix}
       \tilde{x}\\
       \tilde{y} \\
       \tilde{w}
     \end{bmatrix}$$
             where, $\bar{x} = \tilde{x}/\tilde{w}$
            - Projective tranformation or Homography: 3D transformation, 
              ![](https://i.imgur.com/JC0mboy.png)
              ![](https://i.imgur.com/mIN282K.png)
            - To find A in $x' = Ax$, we will first find the system of equations
            - For example
            ![](https://i.imgur.com/JIPmYrc.png)


            
     
 

            










             

    
        

    

    