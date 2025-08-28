# Technical Documentation: Project Ondex

## 1. Our Approach

### The Problem
Large Language Models (LLMs) are powerful, but personalizing them often requires sending sensitive user data to the cloud, posing a significant privacy risk. Full fine-tuning on-device is computationally prohibitive for multi-billion parameter models.

### Our Solution: A Framework for On-Device Personalization
Project Ondex is a proof-of-concept Android framework designed to enable efficient, privacy-preserving personalization of LLMs directly on a user's device. Our approach is centered around **Parameter-Efficient Fine-Tuning (PEFT)**, specifically Low-Rank Adaptation (LoRA), to dramatically reduce the computational and memory footprint of training.

Our unique approach is to build a complete on-device pipeline:
1.  **Efficient Inference:** We use PyTorch ExecuTorch to run a quantized LLM, ensuring fast and low-resource text generation.
2.  **On-Device Loss Calculation:** The framework can perform a forward pass on user-provided data and calculate the cross-entropy loss *on the device*. This is the critical first step in any training loop and is fully implemented in our `LLM_Manager`.
3.  **Architected for LoRA:** The system is designed to update only a tiny fraction of the model's weights (the LoRA adapters). The inclusion of `lora_B_index.json` in our assets is a manifest that maps these trainable weights, demonstrating the architectural intent.
4.  **Privacy by Design:** Since the entire process—from inference to loss calculation—happens locally, no user data ever leaves the device.

While this submission presents a fully functional inference and loss calculation engine, the backpropagation and weight update steps are defined as future work, completing the full P-RGE (Plan-Respecting Gradient Estimation) training loop.

## 2. Technical Stack

-   **Mobile Framework:** Android SDK (API Level 26+)
-   **Programming Language:** Kotlin
-   **ML Runtime:** [PyTorch ExecuTorch](https://pytorch.org/executorch/) v0.7.0 for executing the quantized model (`.pte` file).
-   **Tokenizer Library:** [Deep Java Library (DJL)](https://djl.ai/) with the `huggingface-tokenizers` extension for processing text based on the `tokenizer.json` file.
-   **Build System:** Gradle with Kotlin KTS.

## 3. Technical Architecture

The application is structured into three main layers:



1.  **UI Layer (`MainActivity.kt`)**
    -   Provides a simple user interface for interacting with the LLM.
    -   Collects prompts for text generation and sentences for personalization.
    -   Uses Kotlin Coroutines (`lifecycleScope`) to offload heavy ML tasks from the main UI thread, preventing the app from freezing.
    -   Displays model status, generated responses, and training metrics (like loss).

2.  **Logic Layer (`LLM_Manager.kt`)**
    -   The core of the application, encapsulating all ML logic.
    -   **Asset Management:** Copies the `model.pte` and `tokenizer.json` from the app's assets to internal storage, making them accessible to the native libraries.
    -   **Model Loading:** Initializes the ExecuTorch `Module` and the DJL `HuggingFaceTokenizer`.
    -   **Inference (`generate` method):** Implements a standard auto-regressive generation loop with greedy decoding (argmax) to produce text token-by-token.
    -   **Fine-Tuning Foundation (`runFineTuningStep` method):** This method demonstrates the first part of a training step. It takes user text, tokenizes it, performs a forward pass through the model to get logits, and then calculates the cross-entropy loss against the target tokens.

3.  **Model Layer (Assets)**
    -   `model.pte`: The LLM, converted into the ExecuTorch format. This is a portable, efficient graph representation of the model.
    -   `tokenizer.json`: Contains the vocabulary and rules required by the Hugging Face tokenizer to convert text to and from token IDs.
    -   `lora_B_index.json`: A JSON manifest mapping the names of LoRA "B" matrix weights to their tensor shapes. This file is crucial for the planned weight-update mechanism, as it identifies which tensors are trainable.

## 4. Installation Instructions

1.  Clone the repository: `git clone <your-repo-link>`
2.  Open the project in a recent version of Android Studio (e.g., Iguana or later).
3.  Allow Gradle to sync and download all the required dependencies as defined in `build.gradle.kts` and `gradle/libs.versions.toml`.
4.  **Model Placement:** Due to their size, the `model.pte` and `tokenizer.json` files are not included in this repository. You must place your exported ExecuTorch model and its corresponding tokenizer in the `app/src/main/assets/` directory.
5.  Connect an Android device (or start an emulator) with API Level 26 or higher.
6.  Select the `app` run configuration and click the 'Run' button.

## 5. User Guide

The application provides a simple interface to showcase its core functionalities.



1.  **Loading:** Upon launch, the app begins loading the model from assets. The status text will display "Status: Loading Model...". Once complete, it will change to "Status: Model Loaded Successfully!", and the buttons will become enabled.
2.  **Text Generation:**
    -   Enter a prompt (e.g., "The capital of France is") into the "Enter your prompt here" field.
    -   Tap the **Generate Response** button.
    -   The app will generate a response, which will appear in the text view below the button.
3.  **Personalization (Proof-of-Concept):**
    -   Enter a sentence you want the model to learn from in the "Enter a full sentence to learn from" field. For example: "My favorite team is the innovators."
    -   Tap the **Personalize (1 Step)** button.
    -   The app will perform a forward pass and calculate the loss for this text. The status text will update to show the result, e.g., "Status: Finished 1 step! Initial Loss: 3.45".

## 6. Salient Features

-   **Privacy-First Personalization:** All operations, including future training steps, run 100% on-device. User data is never transmitted to a server.
-   **Highly Efficient Runtime:** Leverages the PyTorch ExecuTorch backend for optimized performance and minimal resource usage on Android.
-   **Extensible Framework:** Provides the foundational blocks (inference and loss calculation) necessary for implementing a complete on-device LoRA fine-tuning loop.
-   **Clean and Modular Code:** The separation of UI and ML logic in `MainActivity` and `LLM_Manager` makes the project easy to understand, maintain, and extend.
