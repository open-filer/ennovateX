# Samsung EnnovateX 2025 AI Challenge Submission

- **Problem Statement** - *On-Device Fine-Tuning Framework for Billion+ Parameter scale LLMs*
- **Team name** - *XOR*
- **Team members (Names)** - *Manan Bhansali*, *Aditya Chourasia*, *Kritagya Singh*
- **Demo Video Link** - *[**Youtube Link**](https://youtu.be/DX-BswADDhk?si=yd1s4n1JXtZ026Jk)*


### Project Artefacts

- **Technical Documentation** - [**Docs**](docs/README.md) *(All technical details are in the docs folder)*
- **Source Code** - The complete source code for the Android application is contained within this repository. The main application logic can be found in the [`app/`](./app) directory.
- **Models Used** - Qwen 3 0.6b was used as the base model for this proof-of-concept. The framework is designed to be model-agnostic and can support other transformer-based architectures.
- **Models Published** - N/A. This project focuses on the framework for fine-tuning, not on producing a specific fine-tuned model.
- **Datasets Used** - N/A. The personalization framework is designed to learn directly from user-provided text input within the application, not from pre-existing public datasets.
- **Datasets Published** - N/A.

### Attribution

This project is built using several powerful open-source technologies. We extend our sincere gratitude to the developers and communities behind them.

- **[PyTorch ExecuTorch](https://pytorch.org/executorch/)**: Used as the core on-device inference engine for running the LLM efficiently on Android.
- **[Deep Java Library (DJL)](https://djl.ai/)**: Used for its robust and easy-to-use implementation of Hugging Face tokenizers on the JVM, which was essential for text processing.

Our novel contribution involves integrating these technologies into a cohesive framework specifically designed for on-device personalization. We have developed an Android application that not only performs inference but also includes the foundational components for a fine-tuning loop, such as on-device loss calculation. The architecture is designed to support Parameter-Efficient Fine-Tuning (PEFT) methods like LoRA, enabling future work on a complete, privacy-preserving training cycle on edge devices.
