# Stage 1: Build Stage (Optional, but good for complex builds)
FROM python:3.11-slim AS builder

# Set the working directory
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# --- Final Image Stage ---
# Use a lean base image for the final container
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy the installed packages from the builder stage (optimized for size)
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages

# Copy the rest of the application files
# This includes main.py, stock.json, etc.
COPY . .

# Expose the port Uvicorn will listen on
EXPOSE 8000

# Command to run the FastAPI application using Uvicorn
# The format is: uvicorn [file_name]:[app_instance_name] --host 0.0.0.0 --port 8000
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
