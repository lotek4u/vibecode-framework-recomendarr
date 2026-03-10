FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/

VOLUME ["/config"]

ENV CONFIG_PATH=/config/config.yaml
ENV DB_PATH=/config/recomendarr.db

EXPOSE 5000

CMD ["gunicorn", "app.main:application", "--bind", "0.0.0.0:5000", "--workers", "1", "--timeout", "120", "--log-level", "info"]
