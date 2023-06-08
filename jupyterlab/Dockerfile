FROM --platform=linux/amd64 python:3.8-slim-buster


ARG DEBIAN_FRONTEND=noninteractive

COPY ./ ./TenSEAL
WORKDIR /TenSEAL


RUN .github/workflows/scripts/install_req_docker.sh

ENV CC=clang \
    CXX=clang++

RUN pip3 install tenseal

#FROM python:3.8

# Set the JupyterLab password
ARG JUPYTER_PASSWORD

# Install JupyterLab and dependencies
RUN pip install jupyter jupyterlab

# Set the JupyterLab password
RUN jupyter notebook --generate-config && \
    echo "c.NotebookApp.password = '$(python -c "from notebook.auth import passwd; print(passwd('$JUPYTER_PASSWORD'))")'" >> ~/.jupyter/jupyter_notebook_config.py

# Set the working directory
WORKDIR /opt/app

# Copy the notebook files
COPY data /opt/app/data

# Expose the JupyterLab port
EXPOSE 8888

# Start JupyterLab
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--notebook-dir=/opt/app/data"]
