# define the base image on which this image is based
# in this case the base image is the Python3.7 Docker image
FROM python:3.7.9

# define the maintainer
MAINTAINER David Quinlan

# add additional content to the image
# update the package information and install python and pip
RUN apt-get update -y && apt-get install -y python3-pip python3-dev

# Install poetry:
RUN pip install poetry

# Copy in the config files:
COPY pyproject.toml poetry.lock ./

# Install only dependencies:
RUN poetry install --no-root --no-dev