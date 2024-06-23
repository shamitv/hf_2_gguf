# hf_2_gguf
Documentation on How to get GGUF Versions of Hugging Face (HF) Models

This documenation uses Phi-3 as an example. 

## Why ? 

### Need

https://huggingface.co/microsoft/Phi-3-mini-128k-instruct 

![image](https://github.com/shamitv/hf_2_gguf/assets/8604949/80e0ca2c-edbb-4d78-8dfd-cb9b369e300c)

As you might notice, some models are not availalbe in GGUF and lower quantized versions. GGUF and 2 / 4 bit quantized versions make development easier. Larger models can be used for actual deployments. 

### issues 

  Llama Cpp Python has Handy converion scripts. Installation and usage requires few unusal steps. 

## Steps 

#### Create and activate a Python environment

python3 -m venv venv
source venv/bin/activate

#### Install dependencies 

##### https://github.com/abetlen/llama-cpp-python

pip install --upgrade llama-cpp-python

##### Hugging Face hub 

pip install --upgrade huggingface_hub 
pip install 'huggingface_hub[cli,torch]'

##### Lamma Cpp binaries (for quantization) and "gguf" python library
git clone https://github.com/ggerganov/llama.cpp.git
pip install ./llama.cpp/gguf-py

![image](https://github.com/shamitv/hf_2_gguf/assets/8604949/792f3e33-e1de-4a9c-a40b-d4af003f1cd4)


###### Why not gguf from pypi ?
There are some comatibility issues; so it needs to be installed from source 
