# Qwen2-VL-7B NPU
![Alt text](https://github.com/user-attachments/assets/6d297a34-c516-4cb1-be4a-bca471d40fa6)
<br><br>**User**:\<image\>Describe the image.<br><br>
**Answer**: The image depicts an astronaut lounging on the moon, enjoying a drink from a green bottle while sitting in a chair. The astronaut is wearing a white spacesuit and has a helmet with a visor. The background features a view of Earth from the moon's surface, with stars visible in the sky. The scene is set against a black and white backdrop, giving it a surreal and futuristic feel.

------------

## Qwen2-7B VLM for RK3588 NPU (Rock 5, Orange Pi 5). <br/>
[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)<br/><br/>
Paper: [Qwen2 Technical Report](https://arxiv.org/pdf/2407.10671)<br/><br/>
Hugging face: https://huggingface.co/Qwen/Qwen2-VL-7B-Instruct

------------

## Introduction

LLMs (Large Language Models) are neural networks trained on large text datasets to understand and generate language.<br>
VLMs (Vision-Language Models) add a visual encoder so the model can process images and text together.<br> 
A combined VLM+LLM system is often referred to as a multimodal model.

These models can be large—hundreds of millions to billions of parameters—which impacts accuracy, memory use, and runtime speed.<br>
On edge devices like the RK3588, available RAM and compute are limited, and even the NPU has strict constraints on supported operations.<br>
Because of this, models typically need to be quantised or simplified to fit.

Performance is usually expressed in tokens (words) per second.<br>
Once converted to RKNN, parts of the model can run on the NPU, improving speed.<br>
Despite these limits, models like Qwen2-7B run well on the RK3588 because the NPU efficiently accelerates the heavy math, and the vision encoder can be optimised. This makes advanced multimodal AI feasible on small, power-efficient devices.

------------

## Model performance benchmark (FPS)

All models, with C++ examples, can be found on the Q-engineering GitHub.<br><br>
All LLM models are quantized to **w8a8**, while the VLM vision encoders use **fp16**.<br>

| model         | RAM (GB)<sup>1</sup> | llm cold sec<sup>2</sup> | llm warm sec<sup>3</sup> | vlm cold sec<sup>2</sup> | vlm warm sec<sup>3</sup> | Resolution | Tokens/s |
| --------------| :--: | :-----: | :-----: | :--------: | :-----: | :--------:  | :--------: |
| [Qwen3-2B](https://github.com/Qengineering/Qwen3-VL-2B-NPU) | 3.1 | 21.9 | 2.6 | 10.0  | 0.9 | 448 x 448 | 11.5 |
| [Qwen3-4B](https://github.com/Qengineering/Qwen3-VL-4B-NPU) | 8.7 | 49.6 | 5.6 | 10.6  | 1.1 | 448 x 448 | 5.7 |
| [Qwen2.5-3B](https://github.com/Qengineering/Qwen2.5-VL-3B-NPU) | 4.8 | 48.3 |  4.0 | 17.9  | 1.8 | 392 x 392 | 7.0 |
| [Qwen2-7B](https://github.com/Qengineering/Qwen2-7B-NPU) | 8.7 | 86.6 |   34.5 | 37.1  | 20.7 | 392 x 392 | 3.7 |
| [Qwen2-2.2B](https://github.com/Qengineering/Qwen2-2B-NPU) | 3.3 | 29.1 |   2.5 | 17.1  | 1.7 | 392 x 392 | 12.5 |
| [InternVL3-1B](https://github.com/Qengineering/InternVL3-NPU) | 1.3 |  6.8 |   1.1 | 7.8    | 0.75 | 448 x 448 | 30 |
| [SmolVLM2-2.2B](https://github.com/Qengineering/SmolVLM2-2B-NPU) | 3.4 | 21.2 |   2.6 | 10.5   | 0.9  | 384 x 384 | 11 |
| [SmolVLM2-500M](https://github.com/Qengineering/SmolVLM2-500M-NPU) | 0.8 |  4.8 |   0.7 | 2.5    | 0.25 | 384 x 384 | 31 |
| [SmolVLM2-256M](https://github.com/Qengineering/SmolVLM2-256M-NPU) | 0.5 |  1.1 |   0.4 | 2.5    | 0.25 | 384 x 384 | 54 |

<sup>1</sup> The total used memory; LLM plus the VLM. <br>
<sup>2</sup> When an llm/vlm model is loaded for the first time from your disk to RAM or NPU, it is called a cold start.<br>
The duration depends on your OS, I/O transfer rate, and memory mapping.<br> 
<sup>3</sup> Subsequent loading (warm start) takes advantage of the already mapped data in RAM. Mostly, only a few pointers need to be restored.<br><br>
<img width="600" height="450" alt="Figure_1" src="https://github.com/user-attachments/assets/fac39e85-3f6c-422a-82bc-3d7e4c62cf17" />

------------

## Dependencies.
To run the application, you have to:
- OpenCV 64-bit installed.
- rkllm library.
- rknn library.
- Optional: Code::Blocks. (```$ sudo apt-get install codeblocks```)

### Installing the dependencies.
Start with the usual 
```
$ sudo apt-get update 
$ sudo apt-get upgrade
$ sudo apt-get install cmake wget curl
```
#### OpenCV
To install OpenCV on your SBC, follow the Raspberry Pi 4 [guide](https://qengineering.eu/install-opencv-on-raspberry-64-os.html).<br><br>
Or, when you have no intentions to program code:
```
$ sudo apt-get install lib-opencv-dev 
```
------------

## Installing the app.
```
$ git clone https://github.com/Qengineering/Qwen2-VL-7B-NPU
```

#### RKLLM, RKNN
To run InternVL3, you need to have the **rkllm-runtime** library version **1.2.2** (or higher) installed, as well as the **rknpu driver** version **0.9.8**.<br>
If you don't have these on your machine, or if you have a lower version, you need to install them.<br>
We have provided the correct versions in the repo.<br>
```bash
$ cd ./InternVL3-NPU/aarch64/library
$ sudo cp ./*.so /usr/local/lib
$ cd ./InternVL3-NPU/aarch64/include
$ sudo cp ./*.h /usr/local/include
```
### Download the LLM and VLM model.
The next step is downloading the models.<br>
Download the two needed files (8.9 GB) from our Sync.com server:<br>
[Qwen2-VL-7B_llm_w8a8_rk3588.rkllm](https://ln5.sync.com/dl/53d0c6240#7xj9ipg9-4ifttpaq-y32wff53-ymn3qvqx) and [Qwen2_VL_7B_vision_rk3588.rknn](https://ln5.sync.com/dl/53d0c6240#7xj9ipg9-4ifttpaq-y32wff53-ymn3qvqx)<br>
Copy both into this folder.

### Building the app.
Once you have the two models, it is time to build your application.<br>
You can use **Code::Blocks**.
- Load the project file *.cbp in Code::Blocks.
- Select _Release_, not Debug.
- Compile and run with F9.
- You can alter command line arguments with _Project -> Set programs arguments..._ 

Or use **Cmake**.
```
$ mkdir build
$ cd build
$ cmake ..
$ make -j4
```
### Running the app.
The app has the following arguments.
```bash
VLM_NPU Picture RKNN_model RKLLM_model NewTokens ContextLength
```
| Argument   | Comment |
| --------------| --  |
| picture | The image. Provide a dummy if you don't want to use an image | 
| RKNN_model | The visual encoder model (vlm) | 
| RKLLM_model | The large language model (llm) | 
| NewTokens | This sets the maximum number of new tokens. Optional, default 2048| 
| ContextLength | This specifies the maximum total number of tokens the model can process. Optional, default 4096| 

<br>In the context of the Rockchip RK3588 LLM (Large Language Model) library, the parameters NewTokens and ContextLength both control different limits for text generation, and they're typical in LLM workflows.<br>
**NewTokens**<br> 
This sets the maximum number of tokens (pieces of text, typically sub-word units) that the model is allowed to generate in response to a prompt during a single inference round. For example, if set to 300, the model will not return more than 300 tokens as output, regardless of the prompt length. It's important for controlling generation length to avoid too-short or too-long responses, helping manage resource use and output size.<br>
**ContextLength**<br>
This specifies the maximum total number of tokens the model can process in one go, which includes both the prompt (input) tokens and all generated tokens. For example, if set to 2048 and your prompt already uses 500 tokens, the model can generate up to 2048-500 = 1548 new tokens. This is a hardware and architecture constraint set during model conversion and deployment, as the context window cannot exceed the model's design limit (for instance, 4096 or 8192 tokens depending on the model variant).

A typical command line can be:
```bash
VLM_NPU ./Moon.jpg ./models/Qwen2_VL_7B_vision_rk3588.rknn ./models/Qwen2-VL-7B_llm_w8a8_rk3588.rkllm 2048 4096
```
The NewTokens (2048) and ContextLength (4096) are optional and can be omitted.
### Using the app.
Using the application is simple. Once you provide the image and the models, you can ask everything you want.<br>
Remember, we are on a bare Rock5C, so don't expect the same quality answers as ChatGPT can provide.<br>
On the other hand, when you see the examples below, the app performs amazingly well.<br><br>
If you want to talk about the picture, you need to include the token `<image>` in your prompt once.<br>
The app remembers the dialogue until you give the token `<clear>`.<br>
With `<exit>`, you leave the application.
### C++ code.  
Below, you find the surprisingly little code of main.cpp. 
```cpp
#include "RK35llm.h"

int main(int argc, char** argv)
{
    std::string input_str;
    std::string output_str;
    RK35llm RKLLM;

    RKLLM.SetInfo(true);            //yes, you may give me additional model information
    RKLLM.SetSilence(false);        //you may print the incremental text chunks on the terminal

    if     (argc< 4) {std::cerr << "Usage: " << argv[0] << " image vlm_model llm_model [option]NewTokens [option]ContextLength\n"; return -1;}
    else if(argc==4) RKLLM.LoadModel(argv[2],argv[3]);
    else if(argc==5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]));
    else if(argc> 5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]),std::atoi(argv[5]));

    cv::Mat Pic = cv::imread(argv[1]);
    RKLLM.LoadImage(Pic);

    while(true) {
        printf("\n");
        printf("User: ");

        std::getline(std::cin, input_str);
        if (input_str == "exit") break;

        output_str = RKLLM.Ask(input_str);
//        std::cout << "\nLLM Reply: " << output_str << std::endl;
    }

    return 0;
}
```
Most code speaks for itself. One remark.<br>
The LLM generates the answer to your request in little pieces of text. You see them as if the LLM is typing on your terminal.<br>
RKLLM.SetSilence controls this behaviour. When set to `true`, it will suppress this output. RKLLM.Ask() also returns the complete answer.<br>
In main.cpp uncomment this line:
```cpp
std::cout << "\nLLM Answer: " << output_str << std::endl;
```

------------

## VLM examples.
| Image         | Dialog |
| --------------| --  |
| ![Singapore_S](https://github.com/user-attachments/assets/e742059a-3bdb-41be-9647-e7767da12ce6) |**User**: \<image\>Which city is this?<br>**Answer**: This image depicts the skyline of Singapore, featuring the Marina Bay Sands and the Singapore Flyer Ferris wheel. The Marina Bay Sands is one of the largest hotels in the world, while the Singapore Flyer is a popular tourist attraction.| 
| ![ChineseWall_S](https://github.com/user-attachments/assets/3ba4b57a-4b22-4050-b995-9599c6d239de) |**User**: \<image\>Tell me something about the history of this place.<br>**Answer**: The Great Wall of China is one of the most iconic landmarks in the world, stretching over 13,000 miles (21,000 kilometers) across northern China and parts of Mongolia. It was built to protect against invasions from nomadic tribes and has a rich history dating back several centuries.<br><br>The construction of the Great Wall began around the 5th century BC during the Warring States period when various Chinese states were building defensive structures along their borders. The first emperor of the Qin dynasty, Qin Shi Huangdi, unified China in 221 BC and ordered the construction of a continuous wall to protect his empire from invasions.<br><br>The Great Wall was built by millions of workers over several centuries, with different sections being constructed or renovated at various times throughout history. It is estimated that around 700,000 people died during its construction, including soldiers, laborers, and prisoners who were forced to work on the wall.<br><br>Over time, the Great Wall became a symbol of Chinese culture and identity, and it has been designated as a UNESCO World Heritage Site in recognition of its historical significance. Today, the Great Wall is one of the most popular tourist destinations in China and attracts millions of visitors each year.|
| ![Pizza_S](https://github.com/user-attachments/assets/26fd5da3-f491-4b48-978a-14eae0819247) |**User**: \<image\>What are the ingredients I need to make this?<br>**Answer**: The pizza in the picture appears to be a Margherita, which is made with fresh mozzarella cheese and fresh basil leaves. The ingredients you would need for this type of pizza include:<br>1. Fresh Mozzarella Cheese (cheese)<br>2. Tomato Sauce (sauce)<br>3. Fresh Basil Leaves (herbs)<br><br>You may also want to have some additional toppings like tomato sauce, if desired.<br><br>To make the pizza, you will need a pizza stone or baking sheet that can withstand high temperatures. You'll also need a pizza cutter and a rolling pin for shaping the dough.<br><br>**User**: What is the first step?<br>**Answer**: The first step in making this Margherita pizza would be to preheat your oven to the desired temperature, typically around 450°F (230°C), and prepare the pizza stone or baking sheet.<br><br>If you're using a pizza stone, make sure it's hot by placing it directly on the rack of the oven. If you're using a baking sheet, place it in the oven and preheat for about 10 minutes before adding your toppings.<br><br>Next, you'll need to prepare the dough if you haven't already. You can either use store-bought pizza dough or make your own from scratch by following a recipe that includes flour, yeast, salt, and water.<br><br>Once the dough is ready, it's time to add the toppings. In this case, you would spread tomato sauce over the entire surface of the dough and then sprinkle fresh mozzarella cheese and fresh basil leaves on top. You can also add additional toppings like tomato sauce if desired.<br><br>Finally, place the pizza in the oven and bake until the crust is golden brown and the cheese is melted and bubbly.|
| ![Titanic_S](https://github.com/user-attachments/assets/bf6da76c-c9a4-41c6-bea1-2b4411dd4013) |**User**: \<image\>Which movie is this?<br>**Answer**: Answer: The image appears to be from the movie "Titanic."<br><br>**User**: Can you tell me the plot?<br>**Answer**: Sure, "Titanic" is a romantic drama film set on the ill-fated maiden voyage of the RMS Titanic. The story follows the love affair between Jack Dawson (played by Leonardo DiCaprio) and Rose DeWitt Bukater (played by Kate Winslet). The movie explores themes of class, romance, and tragedy as the ship sinks in the North Atlantic Ocean.|

## LLM example.
Notice the nice Markdown makeup of the answer!<br><br>
**User**: Can you make me a C++ call that embeds the Pythagorean theorem?<br><br>
**Answer**: Sure, here's an example of how to use the Pythagorean theorem in C++:
```c++
#include <cmath>
#include <iostream>

int main() {
    double side1 = 3.0;
    double side2 = 4.0;
    double hypotenuse = sqrt(side1 * side1 + side2 * side2);
    std::cout << "The length of the hypotenuse is: " << hypotenuse << std::endl;
    return 0;
}
```

This code uses the `sqrt` function from the `<cmath>` library to calculate the square root of the sum of squares of two sides (side1 and side2) using the Pythagorean theorem. The result is then printed to the console.
Here's how it works:
```c++
double hypotenuse = sqrt(side1 * side1 + side2 * side2);
```

This line calculates the square root of the sum of squares of two sides (side1 and side2) using the Pythagorean theorem. The result is then stored in the variable `hypotenuse`.

------------

## **[Rock5GPT](https://rock5gpt.qengineering.eu)**
To get a taste, try our professional Qwen3 AI-chatbot running on a Rock 5C: https://rock5gpt.qengineering.eu
<img width="815" height="1151" alt="Rock5GPT" src="https://github.com/user-attachments/assets/3ce5ad31-bc2b-4513-8ac9-42be793a86db" /><br>

------------

[![paypal](https://qengineering.eu/images/TipJarSmall4.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=CPZTM5BB3FCYL) 




