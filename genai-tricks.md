# Local Generative AI - Text and Image
_You want Jarvis? We have Jarvis at home._

_I've leveled up far enough in life that I remember running ELIZA on an Apple II for the first time, and being fascinated with concept of a communicating with an artificial intelligence. Mix in a steady consumption of science fiction novels, television, and movies, and it's somewhat surprising that I didn't immediately experiment with recent attempts at AI._

_Analyzing the why is a philosophical discussion, but a practical reason was ease of use. Like Skyrim or New Vegas modding, tools to make the process simpler and get to playing make tinkering and experimenting much easier._

## One-Liners and llamafile

Due credit to this [hackaday article](https://hackaday.com/2023/12/29/using-local-ai-on-the-command-line-to-rename-images-and-more/) :pirate_flag: for my initial discovery of Justine Tunny's [Bash One-Liners for LLMs](https://justine.lol/oneliners/) and Mozilla's [llamafile](https://github.com/Mozilla-Ocho/llamafile) project.

If you want to download a few files, run a relatively simple command-line instruction, and get a generative AI model to do something, it's a great way to get started. As an example, here's how to ask the LLaVA model what it sees in an image:

``
llava-v1.5-7b-q4.llamafile --cli --image lemurs.jpg --temp 0 -e -p '### User: What do you see?\n### Assistant:' --silent-prompt 2>/dev/null
``

## Hardware - It's Not Much

My CPU/GPU is no powerhouse, I'd say it is compariable to a SteamDeck in specs:

- SteamOS via [HoloISO Immutable](https://github.com/holoiso-staging/releases) ([tips and tricks](https://github.com/emil-muzz/emil-muzz/blob/main/steamos-tricks.md))
- AMD Radeon RX 6600 @ 8GB VRAM
- Intel Core i3-9100F @ 3.60 GHz
- Corsair Vengeance LPX DDR4 @ 16 GB

Even with these limited specs I can run a 7b text-completion model and a Stable Diffusion 1.5 model simultaneously, and see generated responses within a minute or less.

:warning: _The immutable version of HoloISO makes most of the filesystems read-only, which makes installing build packages and compiling from source challenging. My (inelegant) solution is to run everything in an arch chroot jail. This raises what can grow into a significant storage requirement if you experiment with a number of models. If you're going to try this with a Steam Deck, I advise you do similar from an SD card._

## API - Running a Model

_After playing around with llamafiles and developing an increasingly large directory of text files containing prompts, I dug a little deeper into front-ends that launch a given model and provide and API that requests can be sent to anytime, mostly to eliminate waiting for the model to load every time every time I invoked llamafile._

To run a model locally, you'll want a front-end that takes care of loading the model to memory (and optionally to a GPU), and then exposing an API you can use to send generation requests. This is similar to the interactive mode of llamafile, but offers additional functionality.

I'm using [koboldcpp](https://github.com/LostRuins/koboldcpp) to load and send requests to models. I'm specifically using the the ROCm (AMD) build by [YellowRoseCx](https://github.com/YellowRoseCx/koboldcpp-rocm), since it supports offloading to my AMD RX 6600. _By comparison, generating a response using only the CPU (i3-9100F) takes 3-4x longer than it does with GPU offloading._

:warning: _Due to some creative challenges thanks to HoloISO Immutable, installing the required dependencies and compiling from source while in a chroot jail is worthy of it's own guide that I might add later. Binary versions for most platforms are available on the releases page, but may have their own dependencies._

Recently koboldcpp added support for loading both a text-generation model, and a Stable Diffusion text-to-image model simultaneously. At the moment my command-line to load Gemma 7b and Dreamshaper SD looks something like this:

``
python /home/deck/koboldcpp-rocm/koboldcpp.py --threads 4 --blasthreads 4 --usecublas mmq lowvram --gpulayers 18 --blasbatchsize 256 --contextsize 8192 --model /home/deck/models/gemma-7b-it.Q4_K_M-v2.gguf --sdconfig /home/deck/models/dreamshaper_8_pruned.safetensors quant
``

 ## Models - Under the Hood

There are countless [models](https://huggingface.co/models?pipeline_tag=text-generation&library=gguf) available for download on [HuggingFace](https://huggingface.co). At first it can be intimidating to figure out which model to try, what version of it to download, and what to expect from it.

_WIP, sorry. Super short version._

mistral-7b-instruct-v0.2.Q5_K_M.gguf

_Think of the 'b' number (7b) 'size' of model, or page count of a book. The larger the model, the more memory you'll need to run it. I've been using 7b models based on Mistral and LLaMA 2._

_Think of the 'Q' code as the 'depth' of the model, how many pages are devoted to a chapter or topic. Q5_K_M is fairly balanced in my experience._

I'm going to personify models for a moment, because it's helped me contextualize many of the buzzwords thrown around when discussing AI. This advice won't apply to all models, your mileage may vary.

The best way I've been able to think of AI models, and communicating with them is to imagine them as a librarian; my most often used prompt is a characterization of a librarian. A good number of models on HF are based on Mistral or LLaMA 2, and for general use these base models do a pretty great job on their own. 

Some go further and 'train' the model with customized datasets. To extend the librarian analogy, think of trained models as librarians in specialized libraries. Librarians for business, chemistry, computer programming, creative writing, finance, interactive chat, law, medicine, etc.

### Text Generation

To get some kind of data, I'm going to ask my Librarian to answer the antibiotics question:
```
Antibiotics question:
Antibiotics are a type of medication used to treat bacterial infections. They work by either killing the bacteria or preventing them from reproducing, allowing the body’s immune system to fight off the infection. Antibiotics are usually taken orally in the form of pills, capsules, or liquid solutions, or sometimes administered intravenously. They are not effective against viral infections, and using them inappropriately can lead to antibiotic resistance.

Explain the above in one sentence:
```
With the character's description and dialog examples, this comes out to 1,125 tokens to process.

### Stable Diffusion 

To get a sample of dreamshaper's performance, I'll ask the Librarian to render a portrait of himself (/sd you).

### Results Chart:
```
|----------------------|-------------|-----------|---------|---------|
|               model  | contextsize | gpulayers | biotics | /sd you |
|----------------------|-------------|-----------|---------|---------|
|   gemma-7b-it.Q4_K_M |        8024 |        18 |     19s |     50s |
|----------------------|-------------|-----------|---------|---------|

```

 ## PROMPT FORMATING
 
 ChatML prompt format:
 	<|im_start|>system 
 	Provide some context and/or instructions to the model.
 	<|im_end|> 
 	<|im_start|>user 
 	The user’s message goes here
 	<|im_end|> 
 	<|im_start|>assistant 

### FRONTEND

 I am using the staging build of SillyTavern. https://github.com/SillyTavern/SillyTavern

 TODO: Update to include information on running SD with OH at the same time.

## ST SETUP

Set Text Completion:
Set response to 512
Set Context to 8000
Use Default Preset

### Set API:	

Text Completion
KoboldCpp
http://localhost:5001/

### Set Advanced Formatting:

Context Template: ChatML
Instruct Mode: Enabled
Preset: ChatML
Tokenizer: API (WebUI / koboldcpp)

## Barebones Character Guide (WIP)

### PERSONA

The first thing you should do is create your own Persona. These details
will be included in the prompt, and will give the character context 
relevant to you when generating chat responses. The same guidelines 
apply to creating a persona for yourself, or the character.

Note that persona management only has a description box, while characters
have description, first message, personality, scenario, note, and dialog.

### TRAITS

My advice is to limit descriptive context to traits, clothes, and body.
Adding genre, tags, environment, and scenario context is done elsewhere.

### GENRES

Genre is a category or type of artistic work, such as literature, film, or game, that typically follows a particular set of conventions or themes. It helps to define the overall style and content of the work.

### TAGS

Tags are more specific keywords or topics that provide more detailed information about the content.

## PROMPTS

.. to be continued.
