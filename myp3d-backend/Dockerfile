# syntax=docker/dockerfile:1

# Node runs YouTube's JavaScript challenges for yt-dlp, which requires Node 22+.
# Copy just the binary instead of the whole Node image.
FROM node:22-alpine AS node

FROM python:3.12-alpine

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    USE_SYSTEM_FFMPEG=true \
    MYP3D_LIBRARY_DIR=/data/downloads \
    MYP3D_DB_PATH=/data/db/myp3d.db

COPY --from=node /usr/local/bin/node /usr/local/bin/node
RUN ln -sf /usr/local/bin/node /usr/bin/node

RUN apk add --no-cache ffmpeg libstdc++ ca-certificates

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN mkdir -p /data/downloads /data/db

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=5s --start-period=20s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/', timeout=3)"

CMD ["python", "main.py"]