# How RamaLama Makes AI Boring

## Introduction

This project documents my exploration of **RamaLama**, a tool that allows developers to run Large Language Models locally using different model transports such as Hugging Face, Ollama, ModelScope, and OCI registries.

The goal of this exercise was to:

* Install RamaLama
* Run different AI models using multiple transports
* Document commands and outputs
* Analyze the differences between the transports
* Reflect on the developer experience of working with AI tools like RamaLama

# System Environment

**Operating System:** Windows
**Terminal:** Command Prompt
**Container Runtime:** Docker
**RAM:** 16GB
**CPU:** Intel (R) Core (TM) processor
**WSL Fedora Environment:**

Due to hardware constraints, I selected **smaller models (0.5B – 1B parameters)** to ensure smooth execution.

# Step 1 — Installing RamaLama

RamaLama was installed on my system following the official documentation.

Command used:

```

pip install ramalama

```

Verification command:

```
ramalama version

```

Output:

![installing ramalama](https://github.com/user-attachments/assets/4ee143af-80ed-4a1e-a12e-5a1b05e08eaa)

This confirmed that RamaLama was successfully installed.

---

# Step 2 — Running My First Model (Hugging Face Transport)

For my first experiment, I selected a small instruction-tuned model hosted on **Hugging Face**.

Model used:

```
Qwen/Qwen2.5-0.5B-Instruct-GGUF
```

Command used:

```
ramalama run huggingface://Qwen/Qwen2.5-0.5B-Instruct-GGUF/qwen2.5-0.5b-instruct-q4_k_m.gguf
```

Prompt used:

```
What are the Four Foundations of the Fedora Project?
```

Output:

![hugging face qwen](https://github.com/user-attachments/assets/af7b2579-2ea5-49cc-a0b7-a7216f26ee75)

I used another model using hugging face transport called microsoft phi

Command used:

```
ramalama run huggingface://microsoft/Phi-3-mini4k-instruct-gguf/Phi-3-mini-4k-instruct-q4.gguf
```

Prompt used:

```
What are the Four Foundations of the Fedora Project?
```

Output:

![hugging microsoft phi](https://github.com/user-attachments/assets/013fda6d-cbde-4a2c-825a-572d0c622822)

### Observation

The Hugging Face transport was downloaded, executed successfully but gave the wrong answer.

I tried other models using Hugging Face transport, but they did not work because of the space constraints.

Deepseek model

![hugging face deepseek model](https://github.com/user-attachments/assets/0b7df4d9-5d0e-47e6-99c8-fc5ba1c26958)

Gemma model

![hugging face gemma model](https://github.com/user-attachments/assets/d2a5db9a-f0cd-4a9a-9fdb-239b17a407a6)

instructlab model
![hugging face instructlab model](https://github.com/user-attachments/assets/2898f8a6-1382-4214-960d-c105a15bd940)

---

# Step 3 — Running Models Using Ollama Transport

Next, I experimented with **Ollama** transport.

Model used:

```
granite3-moe
```

Command:

```
ramalama run granite3-moe
```

Prompt used:

```
What are the Four Foundations of the Fedora Project?
```

output:

![ollama granite model](https://github.com/user-attachments/assets/3402d5c0-d284-48af-a286-a08283d67a1f)

Next, I experimented with another model.

```
Qwen3
```

Command:

```
ramalama run ollama://qwen3:0.6b
```

Prompt used:

```
What are the Four Foundations of the Fedora Project?
```

output:

![ollama Qwen model](https://github.com/user-attachments/assets/ce58e2f4-2391-4f73-9046-2ab3a8a62b6e)


### Observation

The Ollama transport was easy to use since the model ecosystem is already integrated with Ollama. It required minimal configuration, it executed successfully, but also gave the wrong answer.

---

# Step 4 — Running Models Using ModelScope Transport

I also tested the ModelScope model registry.

Command:

```
ramalama run modelscope://Qwen/Qwen2.5-0.5B-Instruct-gguf/qwen2.5-0.5b-Instruct-q4-k-m.gguf
```

Prompt used:

```
What are the Four Foundations of the Fedora Project?
```

Output:

![modelscope qwen model 2](https://github.com/user-attachments/assets/fb039657-154a-4925-9120-bab76ca8a1a7)

I used another model.

Command:

```
ramalama run modelscope://unsloth/Llama-3-2-1B-Instruct-GGUF/Llama-3.2-1B-Instruct-Q4-K_M.gguf
```

Prompt used:

```
What are the Four Foundations of the Fedora Project?
```

Output:

![modelscope unsloth llama 3 2](https://github.com/user-attachments/assets/880941cc-c674-450f-970a-310796865c6c)


The model did not respond successfully.

### Observation

ModelScope worked similarly to Hugging Face, but with a smaller selection of models compared to Hugging Face.

---

# Step 5 — Attempting OCI Transport

I attempted to convert a model into an OCI container image using the following command:

```
ramalama convert ollama://tinyllama ghcr.io/codinghappiness-web/tinyllama:gguf
```

However, I encountered the following error:

```
denied: requested access to the resource is denied
unauthorized: authentication required
```

![oci tinyllama model](https://github.com/user-attachments/assets/d33a47c5-5d55-4a10-8b09-69d8a3e17ac8)

I tried another model

```
ramalama convert ollama://llama3.2 ghcr.io/codinghappiness-web/llama3.2:gguf
```

![error oci llama 3 2 model](https://github.com/user-attachments/assets/eb798f56-d676-4ecf-b7bd-119f0918a0dd)

And lastly, tried the Mistral model
```
ramalama convert ollama://mistral ghcr.io/codinghappiness-web/mistral:gguf
```

![error oci mistral model](https://github.com/user-attachments/assets/fa8df669-f003-4f43-95a0-3f5739ad6d69)

### Investigation

This issue appeared to be related to *GitHub Container Registry (GHCR) authentication permissions*.

Even after logging in with Docker:

```
docker login ghcr.io
```

The error persisted when pushing the OCI image.

### Possible Reasons

* GitHub Container Registry permission configuration
* Repository namespace requirements
* Token permission scope limitations

Due to these constraints, the OCI push was unsuccessful.

### Decision

Since the OCI registry configuration required additional setup beyond the scope of this experiment, I proceeded with the working transports.

---

# Comparison of Model Transports

| Transport    | Ease of Use | Model Availability | Performance                          |
| ------------ | ----------- | ------------------ | --------------------------------     |
| Hugging Face | Easy        | Moderate           | Not Good                             |
| Ollama       | Very Easy   | Small              | Not Good                             |
| ModelScope   | Easy        | Moderate           | Not Good                             |
| OCI          | Complex     | Very Large         | Not tested due to registry issue     |

### Key Observations

* Hugging Face provided the **largest model ecosystem**.
* Ollama provided the **simplest developer experience**.
* ModelScope worked well but had **fewer available models**.
* OCI transport offers **powerful deployment capabilities** but requires **registry configuration**.

---

# Challenges Encountered

### Hardware Constraints

Running large models locally can require significant RAM and storage. With an 16GB RAM system, I had to choose smaller models.

### OCI Registry Authentication

The OCI registry push failed due to authentication or permission configuration issues.

---

# Additional Commands Explored

Some useful RamaLama commands explored:

List models:

```
ramalama list
```

![ramalama list](https://github.com/user-attachments/assets/a18a8a53-20db-484b-b55b-351b4c555c1c)


Pull model without running:

```
ramalama pull huggingface://Qwen
```

Run without container:

```
ramalama run --nocontainer huggingface://granite-moe
```

---

# Does RamaLama Make Working With AI Boring?

Interestingly, my experience while completing this task made me reflect on whether working with AI tools like RamaLama can sometimes feel boring or frustrating.

During my experiments, I used several models across different transports, including Hugging Face, Ollama, and ModelScope. I asked each model the same prompt:

What are the Four Foundations of the Fedora Project?

However, most models did not consistently return the exact or complete answer. Some responses were partially correct, while others were vague or incomplete.

Because of this, the interaction began to feel repetitive. I kept asking the same question to different models but received inconsistent responses. This made the experience feel somewhat boring and mechanical, since the models did not reliably provide the precise information I expected.

However, this experience also highlighted something important about working with AI models, the quality of responses depends heavily on the model itself, its training data, and how prompts are interpreted. Even when using a tool like RamaLama that simplifies running models, the behavior of the models themselves can vary significantly.

So while RamaLama made it easy to run and switch between models, it also revealed that AI outputs are not always predictable or perfectly accurate.

In that sense, the process was both boring and insightful, boring because of the repetitive attempts to get the correct answer, but insightful because it demonstrated the limitations of AI models.

This experience reinforced an important lesson, running AI models is easy, but getting reliable answers from them is still an active challenge.


# Conclusion

Through this exploration, I successfully:

* Installed RamaLama
* Ran models using multiple transports
* Compared different model registries
* Investigated and documented errors
* Analyzed the developer experience

This exercise helped me better understand how RamaLama simplifies local AI model execution and highlights the trade-offs between different model distribution methods.
