FROM docker.io/bitnami/spark:3

USER root
ENV VIRTUAL_ENV=/opt/bitnami/spark/python/.venv
RUN python -m venv "$VIRTUAL_ENV"
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

COPY requirements.txt .
RUN pip install -r requirements.txt
