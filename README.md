
# Minecraft Style

### [[Paper]](https://drive.google.com/file/d/1qkyM1AJvInGOhicSeu35ukghbOPZ19zh/view?usp=sharing) [[Slide]](/docs/static/Minecraft_ify_slide.pdf)

This repository is official Pytorch implementation for [Arxiv](Arxiv).

*(code will be released)*

## Directory

```
code
    |--checkpoint    # path of saved model weight
    |    |  ...     
    |--result       # path of generated image during training each iteration
    |    |  ... 
    |--dataset      # paht of our dataset (.jpg)
    |    |  ...
    |--main.py      # main python file
    |--train.py     # train 
    |--model.py     # model architecture
    |--dataset.py   # dataloader
```

<hr>

## How to use
### A) Inversion your real image

<!--
(exmaple of alignment for real image) <br>
<img src='docs/static/images/real_sample_01.png' width=32%>
<img src='docs/static/images/real_sample_02.png' width=32%>
<img src='docs/static/images/real_sample_03.png' width=32%>
-->

In this step, we inverse the real image to StyleGAN distribution.
```
python run_I2SG.py --img_path [IMG_PATH]

(example)
python run_I2SG.py --img_path samples.png
```

You can see the results with intermediate results with final latent vector (.npy). Your results will be generated in :
```
code
    |--inversion    # path of saved inversion data
        | --[INDEX]_[IMG_PATH]
            | --final_latent_[IMG_PATH].npy     # *this will be used for editing*
```

<br><br>

### B-1) Latent Vector Interpolation

In this step, you can interpolate the two inversed latent vector for not pixel interpolation but latent space interpolation with semantic changes. This step is just for interpolating two vector to generate intermediate results with semantically fused appearance.

```
python run_StyleCLIP.py --first_vector_path [FIRST_VECTOR_PATH] --second_vector_path [SECOND_VECTOR_PATH] 


(example)
python run_StyleCLIP.py --first_vector_path inversion/0000_sample/final_latent_sample_01.npy --second_vector_path inversion/0001_sample/final_latent_sample_02.npy
```



<br><br>


### B-2) Manipulate inversed latent vector with text-guidance

In this step, we manipulate the inversed latent vector with text description.
```
python run_StyleCLIP.py --vector_path [VECTOR_PATH] --description [TEXT_DESCRIPTION]


(example)
python run_StyleCLIP.py --vector_path inversion/0000_samples/final_latent_samples.npy --description a princess face
```

You can see the results with intermediate optimized image with final image (.jpg). Your reults will be genereted in :
```
code
    |--edit    # path of saved edited data
        | --[INDEX]_[IMG_PATH]
            | --final_result.jpg                # *this is final result*
```

<br>
(optional)

If you want to refine the results, you can modify below parameters:
```
--lr        : (default) 0.1,  (recommended range) 0.1~0.001 (e.g., 0.0025)
        'higher value derive the fast convergence in text-guided optimization step'

--l2_lambda : (default) 0.01, (recommeded range) 0.01~0.001 (e.g., 0.005)
        'higher value derive the more near results from initial appearance'

(usage)
python run_StyleCLIP.py --vector_path inversion/0000_samples/final_latent_samples.npy --description a princess face --lr 0.001 --l2_lambda 0.001
```
