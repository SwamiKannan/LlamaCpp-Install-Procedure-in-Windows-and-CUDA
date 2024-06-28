# LlamaCpp Install Procedure in Windows
<p align ="center">
 <img align="middle" src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/meme.jpg">
</p>

## Introduction
I was trying to install Llama.CPP with CUDA support on my system as an LLM inference server to run my multi-agent environment. I had already tried a few other options but for various reasons, they came up a cropper:<br />

1. [Ollama](https://ollama.com/): Easy to use but the server is constrained on the types of roles you can use. They only allow **<system>**, **<user>** and **<assistant>** roles. However, fine-tuned models like NousResearch's [Theta](https://huggingface.co/NousResearch/Hermes-2-Theta-Llama-3-8B) and [Pro](https://huggingface.co/NousResearch/Hermes-2-Pro-Llama-3-8B) are fine-tuned specifically for [function calling using a new role **<tool>**.](https://huggingface.co/NousResearch/Hermes-2-Theta-Llama-3-8B#prompt-format-for-function-calling) Hence, a lot of wrangling and manipulating of the user prompts were required to get the right output. This also increased my token usage. I couldn't get a solution for this even on their Discord server.
   On a side note, though, if the functionality of Ollama is enough for you, it is a brilliant inference server and I can't stop recommending it. <br />
2. [llama-cpp-python](https://pypi.org/project/llama-cpp-python/). Pretty brilliant again, but there were some <a href="https://github.com/abetlen/llama-cpp-python/issues/148">issues about it being slower than the bare-bones Llama.cpp</a>. Since, I am GPU-poor and wanted to maximize my inference speed, I decided to install [Llama.cpp](https://github.com/ggerganov/llama.cpp) on my Windows laptop. <br /><br /> <b>Oh boy!</b>
<br /><br />
## Issues and attempts:
* Initially, tried building Llama.cpp using [w64devkit](https://github.com/skeeto/w64devkit/releases) and [OpenBLAS for Windows](https://github.com/xianyi/OpenBLAS/releases). CPU version worked but not CUDA.
* Visual Studio would not detect CUDA while making the executable. I traversed multiple discussions that on NVidia groups and VS forums that were complaining of similar errors.
* Tried installing stand-alone versions of CMake and the Windows SDK.
* Even tried editing the MAKE file as shown <a href="https://github.com/ggerganov/llama.cpp/issues/4409"> here</a>, but to no avail. Honestly, I am not a C++ guy so I had no idea what I was doing. <br />
<br />

## Solution:
I finally found the key to my problem <a href="https://forums.developer.nvidia.com/t/cudacompile-nvcc-error-cudafe-died-with-status-0xc0000409/260651/15">here </a>. More specifically, in the screenshot below:

<p align ="center">
 <img align="middle" src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/nvidia_incompatibility.PNG">
</p>

Basically, the only Community version of Visual Studio that was available for download from Microsoft was incompatible even with the latest version of cuda (As of writing this post, the latest version of Nvidia is CUDA 12.5). Hence, all my errors were fundamentally derived from there. I also saw a lot of questions on forums and issues on Github repos of how various pieces of libraries just weren't working together. Hence, I wrote down this post to explain in detail, all the steps I took to ensure a smooth installation and running of the Llama.CPP server on Windows with CUDA. 

## Steps (All the way from the basics):
To be fair, the [README file of Llama.cpp](https://github.com/ggerganov/llama.cpp?tab=readme-ov-file#usage) is pretty well written and the steps are easy to follow. The problems are with getting CUDA and the C++ Desktop environment of VS to talk to each other.

### CUDA:
1. Download and install CUDA from here: [Cuda Toolkit 12.5 downloads](https://developer.nvidia.com/cuda-downloads) . If you are worried about Pytorch compatibility, currently [CUDA 12.1](https://developer.nvidia.com/cuda-12-1-1-download-archive) is supported by Pytorch.

### VISUAL STUDIO 2019:
2. Download and install Visual C++ as follows:
    * Download and install the Visual Studio 2109 software from <b>[here](https://www.techspot.com/downloads/7241-visual-studio-2019.html)</b>. Unless you have a Professional or an Enterprise license, Microsoft does not give you access to Visual Studio 2019 versions i.e. there is no official download of Visual Studio 2019 Community available.
    * Run Visual Studio Installer from the Start Menu. This software is the gateway to download all the libraries that you need to work within Visual Studio <br />
        <img alt = "screenshot of VSC Installer from the Start Menu" src ="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/VSC/VS_installer.png" width=60%>
    * Once, the application has opened, click on the Modify option: <br />
        <img alt = "screenshot of VSC Installer launch screen" src ="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/VSC/VS_main%20screen.png" width=60%>
    * Select the **Desktop Development with C+++** <br />
        <img alt = "screenshot of VSC Installer launch screen" src ="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/VSC/VS Cplusplus_components.png" width=60%>
    * Make sure the following components are selected on the right side of your window: <br />
        <img src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/VSC/VS%20Cplusplus_components_1.png" width = 40%> <img src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/VSC/VS%20Cplusplus_components2.png" width = 40%>
    * Click on the "Install while downloading" link: <br />
        <img alt = "screenshot of VSC Installer launch screen" src ="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/VSC/install_button.png" width=40%>
3. There are 4 files that will be present in **C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.5\extras\visual_studio_integration\MSBuildExtensions** (Replace "v12.5" in the path to your CUDA version). These four files are:
   <ol type="a">
   <li>CUDA 11.8.props</li>
   <li>CUDA 11.8.targets</li>
   <li>CUDA 11.8.xml</li>
   <li>Nvda.Build.CudaTasks.v11.8.dll</li>
   </ol>

   Copy and paste all these files into the relevant Visual Studio directory: **C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Microsoft\VC\v160\BuildCustomizations**
### ENVIRONMENT VARIABLES IN WINDOWS:
4. Set the CMAKE_ARGS environment variable (Ensure your Windows account has administrative rights to perform these functions)
    * Click on the Start icon on the bottom left and type: environment
    * Click on "edit environment variables for your account
        ![screenshot of start menu](images/environment/access_environment_variables.png)
    * In the system variables section in the pop up window, click on "New"
    * Set the variable name as "CMAKE_ARGS" and the Variable value as "-DLLAMA_CUBLAS=on -DLLAMA_BLAS_VENDOR=OpenBLAS" as shown below and click "OK": <br>
        <img src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/environment/env_1.png" width=60%>
5. Set the CUDA_PATH variable
    * Similarly, create a second system variable. Set the variable name as CUDA_PATH. The Variable value should be the path to your CUDA library. Examples as below: <br>
        <img src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/environment/env_2.png" width=60%>
6. Set the LLAMA_CUDA variable
    * Create a third system variable. Set the variable name as LLAMA_CUDA and its value to "on" as shown below and click "OK": <br>
        <img src="https://github.com/SwamiKannan/LlamaCpp-Install-Procedure-in-Windows/blob/main/images/environment/env_3.png" width=60%>    
7. Ensure that the PATH variable for CUDA is set correctly. On installation of CUDA in step 1, the CUDA directory should have been set in PATH.
   * Go to the environment variables as explained in step 3.
   * Scroll through the system variables until you see a system variable named *PATH* or *path*
   * Select the variable and click on "Edit".
   * Ensure the CUDA path is configured in the list of entries provided:
       ![](images/environment/env_path.gif)
8. Once all the variables are configured, restart Windows.
### INSTALLATION OF LLAMA-CPP

9. Clone the Llama.cpp repo. You will need Python (version 3.8+ just to be safe), pip and git installed.
    * Run the following command in your command prompt: <br />
          ```
          git clone https://github.com/ggerganov/llama.cpp.git
          ```
    * Navigate to the location where this folder "llama.cpp" is downloaded <br />
         ```cd llama.cpp```

10. Build the executable for usage
    1. The **Release** version
    ```
    cmake -B build -DLLAMA_CUDA=ON
    cmake --build  --config Release -j 8
    ```
    2. The **Debug** version: For some reason, I was getting a few weird artifacts in the LLM response when I was using the Release version. I avoided these by switching to the Debug version of the build. If you face the same issues, you can re-perform step 9 and instead of step 10a, you can build the executable as follows:
     ```
     cmake -B build -DLLAMA_CUDA=ON
     cmake --build build -j 8
     ```
     ##### NOTE: The "-j 8" is optional. 'j' defines the number of workers that work in parallel to build the  executable. The more the faster, but it is still optional
11. If you plan to deploy Llama.cpp as a server, you can build it in the following way:
    1. For the **Release** version:
    ```
    cmake --build build --config Release -t llama-server
    ```
    2. For the **Debug** version:
    ```
    cmake --build build --config Release -t llama-server
    ```
### RUN THE SERVER
12. Setup and run the server as per your build (**Release** or **Debug**)
    1. **Release** version
        ```
        <path to server.exe within llama.cpp repo> -m <path to gguf model> -c <context length> --n-gpu-layers <no. of layers to be loaded onto the GPU> --host <ip address of host - typically 0.0.0.0 or the ip address itself> --port <port that you want the server to listen on>
        ```
        An example is as follows:
       ```
       "llama.cpp\build\bin\Release\server.exe" -m "D:\Hermes-2-Pro-Llama-3-Instruct-Merged-DPO-Q4_K_M.gguf" -c 2048 --n-gpu-layers 33 --host 0.0.0.0 --port 8080
       ```
    2. **Debug** version <br />
       The syntax is similar to the Release version. The only difference is the location of server.exe

       ```
       "llama.cpp\build\bin\Debug\llama-server.exe" -m "D:\Hermes-2-Pro-Llama-3-Instruct-Merged-DPO-Q4_K_M.gguf" -c 2048 --n-gpu-layers 33 --host 0.0.0.0 --port 8080`
       ```
       
