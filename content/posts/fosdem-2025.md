+++
date = '2025-02-17T21:29:48+01:00'
draft = true
title = 'Truly Open AI at FOSDEM 2025'
+++

Last month, I attended FOSDEM, the conference that unites developers and open-source software enthusiasts from across Europe. What began as a small developer meeting in 2000 has grown into an annual event, celebrating its 25th edition this year. Held at the Université Libre de Bruxelles, FOSDEM now attracts over 5000 enthusiasts each year. True to the spirit of open source, the conference is open to everyone, completely free of charge, and organized by dedicated volunteers. The commitment to organizing this event is remarkable.

Attending FOSDEM is a wonderful opportunity to discover advancements in open-source software. Speakers share their latest contributions to open-source projects and demonstrate innovative ways to use them. The energy within the open-source community is astonishing. With over a thousand lectures in a single weekend, FOSDEM offers something for everyone.

As a machine learning engineer, I have personally experienced the impact of open-source AI on the community. Open-source software in machine learning and AI has always been a large driver for innovation. In the early 2000s, academic research introduced foundational algorithms and libraries for AI. Later, the open-source community started playing a larger role, facilitated by collaborative platforms like GitHub. By the 2010s, during the rise of deep learning, the development of frameworks like TensorFlow and PyTorch and open datasets like ImageNet, made it possible for anyone to experiment with AI models. The emergence of generative AI, coupled with increasingly powerful computing capabilities, has introduced models that captivate the imagination and made AI accessible to anyone with a computer. Today, open-source software is more important than ever to share knowledge between coding enthusiasts, academic researchers, small businesses and Big Tech.

While there were many lectures to enjoy, I spent most of the second day in the *Low-level AI Engineering and Hacking* developer room. In this article, I look at three projects that caught my interest: ZML, InstructLab and Paddler. All of these projects promote the democratization of AI development. By offering the tools for free, they level the playing field, stimulate innovation, and boost the mass adoption of powerful AI tools. Each project addresses an important part to build or deploy AI models yourself. ZML helps you serve AI models on any existing infrastructure, InstructLab helps you fine-tune a model with a limited budget, and Paddler improves distributed model serving.

## ZML

ZML promises high-performance inference of any model, on any hardware. While frameworks like vLLM and llama.cpp are well-established for accelerated inference, they don't support deployment on a wide range of hardware based on a single model script. For example, llama.cpp is specifically built for inference of Meta's LLaMA model. Although the project has grown to include other models, integrating a custom model remains challenging [^10]. In contrast, ZML allows you to deploy just about any model, provided you write the Zig code to integrate it.

[^10]: https://github.com/ggml-org/llama.cpp/blob/master/docs/development/HOWTO-add-model.md

[Zig](https://ziglang.org/) provides performance comparable with C/C++ while retaining simplicity and robustness for production use. If your programming background is primarily in higher-level programming languages like Python, Zig might not be the easiest to learn. However, Zig aims to be more user-friendly than pure C, which is the primary language for llama.cpp. For example, Zig uses option types to ensure memory is released when the program no longer needs it, ensuring memory safety without a garbage collector, thus maintaining high performance. This approach is similar to [Rust](https://www.rust-lang.org/), which also uses option types to enforce null pointer checks.

With ZML you define the model in layers using Zig structs, with each layer built using `zml.Tensor` objects. You can reuse some well-known layers from `zml`. Once the neural network is defined, you code the forward function that executes the operations for model inference.
ZML can compile models to the following accelerator runtimes, including NVIDIA CUDA, AMD RoCM, Google TPU, and AWS Trainium/Inferentia 2. Although ZML still needs to prove itself against established libraries like llama.cpp and vLLM, it shows great promise. Find out more on their [website](https://zml.ai/), or the [GitHub page](https://github.com/zml/zml/tree/master).

## InstructLab

Large foundation models like GPT-4 and Claude excel at many tasks. However, because they are closed source, they can only be accessed through proprietary internet APIs. Industries that handle sensitive data, such as healthcare, are limited in using online services for their applications. Even though the adoption rate of cloud services in healthcare is rising, it is low compared to other industries due to stringent compliance laws [^1][^2]. Furthermore, new regulations can make it increasingly difficult to use personal health data in cloud systems [^3].

[^1]: https://www.skyhighsecurity.com/industry-perspectives/why-healthcare-organizations-are-slower-to-adopt-cloud-services.html

[^2]: https://www.forbes.com/councils/forbestechcouncil/2024/03/19/why-healthcare-is-moving-toward-cloud-computing/

[^3]: https://www.fieldfisher.com/en/insights/health-data-in-the-cloud-new-law-in-germany-raises-the-bar

Fortunately, some companies have embraced open-source and have released outstanding foundation models with permissive licenses. For example, [Meta's Llama](https://www.llama.com/), [Mistral AI](https://mistral.ai/models) and more recently, [DeepSeek](https://github.com/deepseek-ai/DeepSeek-R1). These models are not restricted behind a model API, instead you can download the model weights and run it on a self-hosted system. However, models with over 50 billion parameters are difficult and expensive to run. For example, for real-time inference of the Mixtral-7x8B model, you need at least 4 GPUs with 48GB of RAM, which can cost around €30 000 [^4]. In comparison, quantized models with around 7 billion parameters can run on a machine with a single NVIDIA T4 GPU with 16GB of memory, making it cheaper and more practical to use [^5]. However, the performance of these models might not meet the requirements for your use case.

[^4]: https://www.aime.info/blog/en/how-to-run-and-deploy-mixtral/

[^5]: To roughly calculate the model memory requirement, I use the formula expressed in Chapter 7 of Chip Huyen's AI Engineering book: 
N x M x 1.2, where model parameters count N = 7B, memory per parameter M = 1 byte (INT8 quantization), memory needed for activation and key-value vectors = 1.2. Therefore, 7B parameters x 1 byte x 1.2 = 8.4GB

This is where fine-tuning can help [^6]. Fine-tuning can make the model better at specific tasks. For example, you can fine-tune a smaller model to imitate the behaviour of a larger model, through a process called distillation [^7].

[^6]: Fine-tuning is not always the best way to improve the model's task-specific capabilities. If you want to learn more about this, I recommend Chip Huyen's AI Engineering book, Chapter 7.
[^7]: I recommend the book AI Engineering by Chip Huyen, Chapter 7, for more information on fine-tuning.

However, curating data for fine-tuning is labour-intensive. InstructLab promotes fine-tuning with synthetic data using IBM Research's Large-Scale Alignment for chatBots (referred to as LAB). LAB consists of two components: a taxonomy to enable data curation and guide the synthetic data generator, and a method for large scale alignment-tuning [^8]. The taxonomy classifies the manually curated instruction-response pairs into a tree, where each branch represents a specific task of interest. This tree then guides the synthetic data generation (SDG) to ensure targeted coverage of each task. The first method, skills generation, leverages Mixtral-7x8B to generate synthetic examples based on task examples. The second method, knowledge generation, uses the same model but relies on external knowledge sources like documents, books and manuals to ground the generated instruction data.

[^8]: [Sudalairaj, Shivchander et al. “LAB: Large-Scale Alignment for ChatBots.” ArXiv abs/2403.01081 (2024)](https://arxiv.org/abs/2403.01081)

Learn more about it in [this blog post](https://www.redhat.com/en/topics/ai/what-is-instructlab) or have a look at [the project on GitHub](https://github.com/instructlab).

## Paddler

Application load balancers are ubiquitous today, ranging from web server load balancers like NGINX, container load balancers like Kubernetes or Traefik, to cloud-native load balancers like AWS Elastic Load Balancing. Applications handling large volumes of traffic typically use load balancers to uniformly distribute application load across compute resources, improving performance and reliability. The algorithms are highly optimized and operate effectively for a deterministic request load. Some common algorithms are round-robin, which distributes traffic in rotation to each server, and the least connections algorithm, which distributes requests to the server with the least number of active connections. 

However, AI applications can have highly unpredictable execution times. Because larger models require more computational resources, they can have a large impact on execution time. Furthermore, depending on the input, a response from the same model can vary by several  seconds [^20]. 

[^20]: If you want to try it out yourself, [this website](https://openllmbenchmarks.com/index.html) enables you to simulate the generation time of different models on different hardware

Furthermore, in contrast with regular CPU-bound tasks, large foundation models can leverage dedicated batching algorithms and concurrency to increase throughput. For example, dynamic batching algorithms wait for a given number of requests to arrive before executing them as a single batch. 

Paddler introduces load balancing for AI models, based on llama.cpp servers, which utilize continuous batching algorithms. Paddler starts a user agent that monitors available llama.cpp servers and keeps track of available slots. Requests are buffered and evenly distributed across slots.

To learn more, see the GitHub repository [here](https://github.com/distantmagic/paddler).

## Other
Here's some other projects worth mentioning:
- [Model Openness Framework](https://isitopen.ai/): A framework to assess and compare the openness of a model. It distinguishes different levels of openness and completeness based on the model weights, training data, license terms and documentation.
- [Ramalama](https://github.com/containers/ramalama): A project to make it easier to get an AI model running on your hardware using OCI container images.
- [DataPrepKit](https://github.com/IBM/data-prep-kit): IBM's Toolkit for data preparation of LLM training data. It includes functions for document ingestion, deduplication, filtering and language-specific tasks such as document chunking.

## Conclusion
Attending FOSDEM introduces you to a wealth of intriguing open-source tools. The amount of knowledge exchanged is staggering, with over 1000 lectures to attend in over 70 rooms. Because I attended only a single developer room, I didn't even scratch the surface.

If you're planning to attend the conference next year, make sure to plan your schedule in advance, so you don't miss out on your favourite talks: the schedule is too tight for any coffee breaks or lunchtime. I wholeheartedly recommend visiting the 2026 edition! Find out more on the [FOSDEM website](https://fosdem.org/2025/about/).



