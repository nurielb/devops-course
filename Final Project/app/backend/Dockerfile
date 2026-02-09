# Use a specific Python version on a slim Debian base as an example
FROM python:3.12-slim

# Set the working directory inside the container
WORKDIR /app

# Copy your application code into the container
COPY . /app

# Install any required Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Specify the command to run when the container starts
CMD ["python", "./index.py"]