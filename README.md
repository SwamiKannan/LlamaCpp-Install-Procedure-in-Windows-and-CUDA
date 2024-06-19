# LlamaCpp Install Procedure in Windows
<p align ="center">
 <img align="middle" src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/meme.jpg">
</p>

I was trying to install Llama.CPP directly on my system to run my LLM server there. I had considered the following: <br />

1. Tried using ollama, but the server is constrained on the types of roles you can use. They only allow 'system', 'user' and 'tool' roles. However, fine-tuned models like NousResearch's Theta models are fine-tuned specifically for function calling using a new role "tool". Hence, a lot of wrangling and manipulating the user instructions were required that increased my tokens per query.
2. I tried using llama-cpp-server. Pretty brilliant but there were some <a href="https://github.com/abetlen/llama-cpp-python/issues/148">issues about it being slower than the bare-bones Llama.cpp</a>. Since, I am GPU-poor, I decided to see if I can get Llama.cpp installed in my laptop since it had an RTX 3070 laptop graphics card with 8 GB RAM.

I struggled with this installation a lot, traversing nvidia groups due to CUDA errors, VS forums, because it wasn't detecting the CUDA installation and downloading, editing, screwing up and redownloading the Llama.cpp repo. I also saw a lot of posts that were asking for solution for errors that were similar to the ones that I saw (and there were a lot!). I finally found the key to my solution <a href="https://forums.developer.nvidia.com/t/cudacompile-nvcc-error-cudafe-died-with-status-0xc0000409/260651/15">here </a>. More specifically, in the screenshot below:
<p align ="center">
 <img align="middle" src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/nvidia_incompatibility.PNG">
</p>
