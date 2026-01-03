# Multi-stage Dockerfile for a Python FastAPI application
# Stage 1: Builder stage - Install dependencies in a separate layer for optimization
FROM python:3.11-slim AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy requirements file and install Python dependencies
# Using --no-cache-dir to reduce image size by not caching pip packages
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Final runtime image - Use a minimal base image for security and size
FROM python:3.11-slim

# Create a non-root user for security (optional but recommended)
# RUN useradd --create-home --shell /bin/bash app \
#     && chown -R app:app /app
# USER app

# Set the working directory inside the container
WORKDIR /app

# Copy installed Python packages from builder stage to final image
# This avoids installing dependencies in the final image, reducing size and build time
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages

# Copy application source code and static files
# Includes main.py (FastAPI app), stock.json (data), and other necessary files
COPY . .

# Expose port 8000 for the FastAPI application
# This port should match the one configured in your Kubernetes service/deployment
EXPOSE 8000

# Run the FastAPI application using Uvicorn ASGI server
# Command format: uvicorn main:app --host 0.0.0.0 --port 8000
# --host 0.0.0.0 allows connections from outside the container
# --port 8000 matches the exposed port
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
