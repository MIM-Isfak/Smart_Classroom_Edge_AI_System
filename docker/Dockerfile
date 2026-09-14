# ─────────────────────────────────────────────────────────────────
# Smart Classroom Edge AI — Dockerfile
# Optimised for Linux / WSL edge deployment
# ─────────────────────────────────────────────────────────────────

FROM python:3.10-slim

# Metadata
LABEL maintainer="Smart Classroom Edge AI"
LABEL description="Occupancy detection dashboard using ONNX Runtime + Streamlit"

# System dependencies required by OpenCV / ONNX Runtime
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1 \
    libglib2.0-0 \
    libgomp1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy requirements first for Docker layer caching
COPY dashboard/requirements.txt ./requirements.txt

RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -r requirements.txt

# Copy dashboard application
COPY dashboard/app.py ./app.py
COPY dashboard/utils.py ./utils.py

# Copy trained Edge AI model
COPY model/model.onnx ./model.onnx
COPY model/labels.txt ./labels.txt

# Streamlit configuration
ENV STREAMLIT_BROWSER_GATHER_USAGE_STATS=false
ENV STREAMLIT_SERVER_HEADLESS=true
ENV STREAMLIT_SERVER_PORT=8501
ENV STREAMLIT_SERVER_ADDRESS=0.0.0.0

EXPOSE 8501

# Container health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=15s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8501/_stcore/health')" || exit 1

# Start Streamlit dashboard
CMD ["streamlit", "run", "app.py", \
    "--server.port=8501", \
    "--server.address=0.0.0.0", \
    "--server.headless=true", \
    "--browser.gatherUsageStats=false"]