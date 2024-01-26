FROM python:3.11-slim-buster as prod

RUN sed -i 's#http://deb.debian.org#https://mirrors.aliyun.com#g' /etc/apt/sources.list

RUN apt-get update && apt-get install -y \
  default-libmysqlclient-dev \
  gcc \
  pkg-config \
  openjdk-11-jdk \
  build-essential \
  && rm -rf /var/lib/apt/lists/*

RUN pip config set global.index-url https://mirrors.aliyun.com/pypi/simple  && pip install poetry==1.4.2

# Configuring poetry
RUN poetry config virtualenvs.create false

# Copying requirements of a project
COPY pyproject.toml /app/src/
WORKDIR /app/src

# Installing requirements
RUN poetry install --only main

# Removing gcc
RUN apt-get purge -y \
  g++ \
  gcc \
  pkg-config \
  && rm -rf /var/lib/apt/lists/*

# Copying actual application
COPY . /app/src/
RUN poetry install --only main
RUN pip install langchain==0.0.354

CMD ["/usr/local/bin/python", "-m", "reworkd_platform"]

FROM prod as dev

RUN poetry install
