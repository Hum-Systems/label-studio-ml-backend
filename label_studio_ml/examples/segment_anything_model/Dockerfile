FROM python:3.8-slim

WORKDIR /app

ENV PYTHONUNBUFFERED=True \
    PORT=9090 \
    VITH_CHECKPOINT=/app/sam_vit_h_4b8939.pth \
    ONNX_CHECKPOINT=/app/sam_onnx_quantized_example.onnx

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

RUN apt-get update && apt-get install -y wget \
    && pip install --no-cache-dir -r requirements.txt \
    && wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth

ARG IMAGE_WIDTH=640
ARG IMAGE_HEIGHT=480

ENV IMAGE_WIDTH=${IMAGE_WIDTH}
ENV IMAGE_HEIGHT=${IMAGE_HEIGHT}

COPY onnxconverter.py .
RUN python onnxconverter.py .

RUN pip install opencv-python

COPY _wsgi.py .
COPY segment_anything_model.py .
EXPOSE 9090
COPY * /app/



ENV ACCESS_TOKEN=muchsecretwow

CMD exec gunicorn --preload --bind :$PORT --workers 1 --threads 8 --timeout 0 _wsgi:app
