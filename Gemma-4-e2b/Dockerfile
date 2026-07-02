FROM python:3.11-slim

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends gcc libc6-dev && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install Python dependencies
RUN pip install --no-cache-dir flask flask-cors Pillow litert-lm huggingface_hub

# Create dummy Vulkan library to satisfy litert-lm on CPU
RUN printf 'void vkGetInstanceProcAddr(){}\nvoid vkCreateInstance(){}\nvoid vkDestroyInstance(){}\nvoid vkEnumeratePhysicalDevices(){}\nvoid vkGetDeviceProcAddr(){}' > /tmp/vk.c \
    && gcc -shared -fPIC -o /app/libvulkan.so.1 /tmp/vk.c \
    && rm /tmp/vk.c

# Create model directory
RUN mkdir -p /app/models/gemma

# --- KEY FIX: Updated 'hf download' to fetch the 12B model ---
RUN hf download litert-community/gemma-4-E2B-it-litert-lm gemma-4-E2B-it.litertlm --local-dir /app/models/gemma
# Copy the server code
COPY server.py .

# Setup permissions for Hugging Face Spaces (requires user 1000)
RUN useradd -m -u 1000 user
RUN chown -R user:user /app
USER user

# Set environment variables
ENV PORT=7860
ENV LD_LIBRARY_PATH=/app

# --- KEY FIX: Updated path to point to the 12B model file ---
ENV GEMMA_MODEL_PATH=/app/models/gemma/gemma-4-E2B-it.litertlm
EXPOSE 7860

CMD ["python3", "server.py"]
