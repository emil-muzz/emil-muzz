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

## Hardware

My CPU/GPU is no powerhouse, I'd say it is compariable to a SteamDeck in specs:

- SteamOS via [HoloISO Immutable](https://github.com/HoloISO/holoiso) ([tips and tricks](https://github.com/emil-muzz/emil-muzz/blob/main/steamos-tricks.md))
- AMD Radeon RX 6600 @ 8GB VRAM
- Intel Core i3-9100F @ 3.60 GHz
- Corsair Vengeance LPX DDR4 @ 16 GB

Even with these limited specs I can run a 7b text-completion model and a Stable Diffusion 1.5 model simultaneously, and see generated responses within a minute or less.

:warning: _The immutable version of HoloISO makes most of the filesystems read-only, which makes installing build packages and compiling from source challenging. My (inelegant) solution is to run everything in an arch chroot jail. This raises what can grow into a significant storage requirement if you experiment with a number of models. If you're going to try this with a Steam Deck, I advise you do similar from an SD card._

 ## API
 
 My API of choice is koboldcpp, specifically the ROCm (AMD) build by YellowRoseCx:
 	https://github.com/YellowRoseCx/koboldcpp-rocm

TODO: Update to include information on running SD with OH at the same time.

 ## MODELS

TODO: Provide example models, and summary output from each using the same prompt.

 ## Stable Diffusion (Dreamshaper)

 TODO: Give more details on SD and Dreamshaper
 
 ## STARTUP
 
 This is the startup command I use with koboldcpp:
 	python /home/deck/koboldcpp-rocm/koboldcpp.py --threads 3 --blasthreads 3 --usecublas --gpulayers 32 --blasbatchsize 256 --contextsize 8192 --quiet --model /home/deck/models/openhermes-2.5-mistral-7b.Q5_K_M.gguf
 
 TODO: Update to include information on running SD with OH at the same time.
 
 ## PROMPT FORMAT
 
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
