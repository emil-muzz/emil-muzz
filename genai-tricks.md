# Local Generative AI - Text and Image

## LLAMAFILE

TODO: Give some love to [Justine](https://justine.lol) and their work on [llamafile](https://github.com/Mozilla-Ocho/llamafile) which got me started down this path.

If you want something simple to experiment with check out: [Bash One-Liners for LLMs](https://justine.lol/oneliners/)

## HARDWARE

My CPU/GPU is no powerhouse, I'd say it is compariable to a SteamDeck in specs:

- SteamOS 3.4 via HoloISO (holoiso-staging build 1.1)
-	AMD Radeon RX 6600 @ 8GB VRAM
-	Intel Core i3-9100F @ 3.60 GHz
-	Corsair Vengeance LPX DDR4 @ 16 GB

 ## API
 
 My API of choice is koboldcpp, specifically the ROCm (AMD) build by YellowRoseCx:
 	https://github.com/YellowRoseCx/koboldcpp-rocm

TODO: Update to include information on running SD with OH at the same time.

 ## MODEL
 
 This guide is written specifically for the OpenHermes-2.5-Mistral-7B model:
 	https://huggingface.co/teknium/OpenHermes-2.5-Mistral-7B
	https://huggingface.co/TheBloke/OpenHermes-2.5-Mistral-7B-GGUF

 TODO: Update to include information on running a SD safetensor with mistral at the same time.

 ## Stable Diffusion (Dreamshaper)

 TODO: Give more details on SD and Dreamshaper
 
 ## STARTUP
 
 This is the startup command I use with koboldcpp:
 	python /home/deck/koboldcpp-rocm/koboldcpp.py --threads 3 --blasthreads 3 --usecublas --gpulayers 32 --blasbatchsize 256 --contextsize 8192 --quiet --model /home/deck/models/openhermes-2.5-mistral-7b.Q5_K_M.gguf
 
 TODO: Update to include information on running SD with OH at the same time.
 
 ## PROMPT FORMAT
 
 OpenHermes uses the ChatML prompt format:
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

# PROMPTS

.. to be continued.
