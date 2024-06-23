# hf_2_gguf
Documentation on How to get GGUF Versions of Hugging Face (HF) Models

This documentation uses Phi-3 as an example. 

## Why ? 

### Need

https://huggingface.co/microsoft/Phi-3-mini-128k-instruct 

![image](https://github.com/shamitv/hf_2_gguf/assets/8604949/80e0ca2c-edbb-4d78-8dfd-cb9b369e300c)

As you might notice, some models are not availalbe in GGUF and lower quantized versions. GGUF and 2 / 4 bit quantized versions make development easier. Larger models can be used for actual deployments. 

### issues 

  Llama Cpp Python has Handy conversion scripts. Installation and usage requires few unusal steps. 

## Steps 

#### Create and activate a Python environment

````python3 -m venv venv
source venv/bin/activate
````
#### Install dependencies 

##### https://github.com/abetlen/llama-cpp-python

````pip install --upgrade llama-cpp-python````

##### Hugging Face hub 

````pip install --upgrade huggingface_hub 
pip install 'huggingface_hub[cli,torch]'
pip install sentencepiece
````

##### Lamma Cpp binaries (for quantization) and "gguf" python library
###### Why not gguf from pypi ?
There are some compatibility issues; so it needs to be installed from source 

````git clone https://github.com/ggerganov/llama.cpp.git
pip install ./llama.cpp/gguf-py
````
![image](https://github.com/shamitv/hf_2_gguf/assets/8604949/792f3e33-e1de-4a9c-a40b-d4af003f1cd4)

###### Build Llama.cpp
````cd llama.cpp
make -j6
````
At this point, disk usage was about ~5 GB for me 

![image](https://github.com/shamitv/hf_2_gguf/assets/8604949/3ee369d0-c52e-4fa7-a36f-6236efa34efa)


## Test the conversion script  
````llama.cpp/convert-hf-to-gguf.py --help````

Should not  geneate any error and produce output like this. 

![image](https://github.com/shamitv/hf_2_gguf/assets/8604949/e9a06e31-7466-4313-ae13-90de4968a846)

## Convert model 
````llama.cpp/convert-hf-to-gguf.py  ~/proj/models/microsoft/Phi-3-mini-128k-instruct --outfile ~/proj/models/microsoft/Phi-3-mini-128k-instruct.gguf````


## Quantize Model
````llama.cpp/llama-quantize --help```` 

llama-quantize --help should validate that quantize binaries have been compiled and dependencies are available. 

````llama.cpp/llama-quantize ~/proj/models/microsoft/Phi-3-mini-128k-instruct.gguf ~/proj/models/microsoft/Phi-3-mini-128k-instruct_Q4_K_M.gguf Q4_K_M````

This reduces size of 128k mini model from ~7.2 GBto ~2.2 GB 

Summary of quantize command : 

```
<command> <input gguf> <output gguf> <quantization type>
```

## Types of quantization : 
````
quantization types:
   2  or  Q4_0    :  4.34G, +0.4685 ppl @ Llama-3-8B
   3  or  Q4_1    :  4.78G, +0.4511 ppl @ Llama-3-8B
   8  or  Q5_0    :  5.21G, +0.1316 ppl @ Llama-3-8B
   9  or  Q5_1    :  5.65G, +0.1062 ppl @ Llama-3-8B
  19  or  IQ2_XXS :  2.06 bpw quantization
  20  or  IQ2_XS  :  2.31 bpw quantization
  28  or  IQ2_S   :  2.5  bpw quantization
  29  or  IQ2_M   :  2.7  bpw quantization
  24  or  IQ1_S   :  1.56 bpw quantization
  31  or  IQ1_M   :  1.75 bpw quantization
  10  or  Q2_K    :  2.96G, +3.5199 ppl @ Llama-3-8B
  21  or  Q2_K_S  :  2.96G, +3.1836 ppl @ Llama-3-8B
  23  or  IQ3_XXS :  3.06 bpw quantization
  26  or  IQ3_S   :  3.44 bpw quantization
  27  or  IQ3_M   :  3.66 bpw quantization mix
  12  or  Q3_K    : alias for Q3_K_M
  22  or  IQ3_XS  :  3.3 bpw quantization
  11  or  Q3_K_S  :  3.41G, +1.6321 ppl @ Llama-3-8B
  12  or  Q3_K_M  :  3.74G, +0.6569 ppl @ Llama-3-8B
  13  or  Q3_K_L  :  4.03G, +0.5562 ppl @ Llama-3-8B
  25  or  IQ4_NL  :  4.50 bpw non-linear quantization
  30  or  IQ4_XS  :  4.25 bpw non-linear quantization
  15  or  Q4_K    : alias for Q4_K_M
  14  or  Q4_K_S  :  4.37G, +0.2689 ppl @ Llama-3-8B
  15  or  Q4_K_M  :  4.58G, +0.1754 ppl @ Llama-3-8B
  17  or  Q5_K    : alias for Q5_K_M
  16  or  Q5_K_S  :  5.21G, +0.1049 ppl @ Llama-3-8B
  17  or  Q5_K_M  :  5.33G, +0.0569 ppl @ Llama-3-8B
  18  or  Q6_K    :  6.14G, +0.0217 ppl @ Llama-3-8B
   7  or  Q8_0    :  7.96G, +0.0026 ppl @ Llama-3-8B
   1  or  F16     : 14.00G, +0.0020 ppl @ Mistral-7B
  32  or  BF16    : 14.00G, -0.0050 ppl @ Mistral-7B
   0  or  F32     : 26.00G              @ 7B
          COPY    : only copy tensors, no quantizing
````
