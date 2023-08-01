# .github
minaiml (pronounced minimal) min ai ml local language models<br />

# on-device-inference
notes and links on Python machine learning for mobile

On-device inference refers to the process of running machine learning models directly on edge devices, such as smartphones, IoT devices, or embedded systems, without the need for constant internet connectivity or reliance on cloud-based servers. Performing inference on the device itself offers several advantages, including reduced latency, enhanced privacy and security, and decreased dependency on cloud services. In Python, there are several techniques and tools available for enabling on-device inference:

    TensorFlow Lite (TFLite): TensorFlow Lite is a lightweight version of TensorFlow designed for mobile and embedded devices. It allows you to convert trained TensorFlow models into a format optimized for mobile and edge devices. The tflite Python library provides tools for model conversion and inference with TensorFlow Lite.

    PyTorch Mobile: PyTorch Mobile is an extension of PyTorch that enables running PyTorch models on mobile and edge devices. Similar to TensorFlow Lite, PyTorch Mobile optimizes the model for mobile deployment, and the torchscript feature allows for ahead-of-time (AOT) compilation for better performance.

    ONNX Runtime: ONNX Runtime is an open-source inference engine that supports running models across different frameworks, including TensorFlow and PyTorch. It allows you to convert models to the ONNX format and then perform inference with the onnxruntime Python library on edge devices.

    Edge TPU (Tensor Processing Unit): Edge TPU is a hardware accelerator designed by Google to accelerate machine learning inference on edge devices. It works with TensorFlow Lite and can be used to perform on-device inference efficiently.

    Core ML: Core ML is Apple's machine learning framework that enables running machine learning models on iOS, macOS, watchOS, and tvOS devices. You can convert models to the Core ML format and use the coremltools Python library for model conversion and inference.

    OpenVINO (Open Visual Inference & Neural Network Optimization): OpenVINO is an Intel toolkit that optimizes deep learning models for inference on Intel CPUs, GPUs, and VPUs. It supports TensorFlow, PyTorch, and ONNX models and provides Python bindings for inference.

    TensorRT: TensorRT is an NVIDIA library that optimizes deep learning models for NVIDIA GPUs. It can speed up inference significantly and supports TensorFlow and ONNX models. Python bindings are available for TensorRT.

To enable on-device inference using these techniques, you typically need to:

    Train and optimize your machine learning model on a powerful machine or cloud server.
    Convert the trained model to a format suitable for on-device deployment (e.g., TensorFlow Lite, ONNX, Core ML).
    Deploy the optimized model to the target edge device.
    Use the appropriate Python libraries (e.g., tflite, torchscript, onnxruntime, coremltools) to perform inference on the device.

Keep in mind that the choice of the on-device inference technique may depend on the target platform (e.g., Android, iOS, Raspberry Pi, etc.) and the specific hardware capabilities available on the edge device.

