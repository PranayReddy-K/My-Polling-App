# Base image for building the application
FROM python:3.11-slim AS base

# Set the application directory
WORKDIR /usr/local/app


# Add curl for healthcheck
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Copy the requirements file and install dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

###########################################################################
## a stage specifically for development
## where file system changes are observed
FROM base AS dev
RUN pip install watchdog
ENV FLASK_ENV=development
CMD ["watchmedo", "auto-restart", "--debug-force-polling", "--interval=1","--recursive", "--directory=/usr/local/app","--", "python", "app.py"]

###########################################################################
# Define the final stage that will bundle the application for production
FROM base AS final

# Copy our code from the current folder to the working directory inside the container
COPY . .

# Make port 80 available for links and/or publish
EXPOSE 80

# Define our command to be run when launching the container
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:80", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]


