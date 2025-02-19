+++
date = '2025-02-17T21:29:48+01:00'
draft = true
title = 'Truly Open AI at FOSDEM 2025'
+++

This month, I attended FOSDEM, the conference that unites developers and open-source software enthusiasts across Europe. It's an amazing opportunity to learn about the latest improvements in open-source software. At FOSDEM, speakers share their latest contributions to open-source projects and how they can be used or improved. The energy in the open-source community is astonishing. With over a thousand lectures in a single weekend, FOSDEM offers something for everyone.

This year's edition took place on the campus of Université Libre de Bruxelles. While there were many lectures to enjoy, I spent most of Sunday in the *Low-level AI Engineering and Hacking* developer room. In this article, I look at three projects that caught my interest.

## ZML

ZML promises to provide software for high-performance inference of any model, on any hardware. While frameworks like vLLM and llama.cpp are well-established for accelerated inference, they don't support deployment on a wide range of hardware based on a single model script. ZML allows you to deploy just about any model, granted that you write the Zig code to integrate it. 

[Zig](https://ziglang.org/) provides performance on par with C/C++, but retains simplicity and robustness for production use. ZML lets you define the model in layers using Zig structs. In turn, each layer is built up using `zml.Tensor` objects. You can reuse some well-known layers from `zml`. Once the neural network is defined, you code the forward function that executes the operations for model inference.

Although ZML still has to prove itself against existing libraries like llama.cpp and vLLM, it looks very promising. Find out more on their [website](https://zml.ai/), or the [GitHub page](https://github.com/zml/zml/tree/master).

## InstructLab

Large foundation models like GPT-4 and Claude are good at many tasks, but if you want to run a model on local infrastructure, the size of your model is a limiting factor. If you've been working with smaller language models, in the range of 1 to 7 billion parameters, you might have discovered that your specific use case doesn't work that well.

In some cases, fine-tuning the model to the specific task can be the solution. However, curating data for fine-tuning is labour-intensive. InstructLab promises to help by providing tools to fine-tune your model according to the LAB method. The LAB method stands for IBM Research's Large-Scale Alignment for chatBots, and defines how to fine-tune a model, by generating synthetic fine-tuning data based on a set of human-curated training data. 

Learn more about it in [this excellent blog post](https://www.redhat.com/en/topics/ai/what-is-instructlab) or have a look at [the project on GitHub](https://github.com/instructlab).

## Paddler

Application load balancers are ubiquitous these days. Almost all API or web applications run behind a reverse proxy and load balancer to evenly distribute application load across compute resources. The algorithms are highly optimized for a deterministic and predictable request load. However, many AI applications have very unpredictable execution times. Models with different sizes can differ highly in execution time. Furthermore, depending on the input, a response from the same model can differ dozens of seconds. In addition, large models can leverage dedicated batching algorithms and concurrency to increase throughput.

Paddler introduces load balancing for AI models, based on llama.cpp servers. A user agent monitors available llama.cpp servers, and keeps track of available slots. Requests are buffered and evenly distributed across available server slots.

Check out the GitHub repository [here](https://github.com/distantmagic/paddler).

## Other 

Here’s some other projects worth mentioning:

- [Model Openness Framework](https://isitopen.ai/): A framework to assess and compare the openness of a model. It distinguishes different levels of openness and completeness based on the model weights, training data, license terms and documentation.
- [Ramalama](https://github.com/containers/ramalama): A project to make it easier to get an AI model running on your hardware using OCI container images.
- [DataPrepKit](https://github.com/IBM/data-prep-kit): Toolkit for data preparation of LLM training data. It includes functions for document ingestion, deduplication, filtering and language-specific tasks such as document chunking.

## Conclusion

Attending FOSDEM introduces you to a plethora of open-source tools that are worth a look. The amount of knowledge that is shared is staggering, with over 1000 lectures to attend in over 70 rooms. As I attended only a single developer room, I didn't even scratch the surface. The event is completely volunteer-driven, and I was impressed by the amount of engagement to organize this event.

For those who have not attended before, let me leave you with some small advice: plan your day wisely, because the schedule is too tight for any coffee breaks or lunchtime without missing any lectures. I wholeheartedly recommend visiting the 2026 edition! Find out more on the [FOSDEM website](https://fosdem.org/2025/about/).
