FROM python:3.11-slim-buster

WORKDIR /usr/src/app

RUN apt-get update && apt-get install -y g++

RUN pip install --no-cache-dir --upgrade pip

RUN pip install --no-cache-dir gunicorn flask langchain openai

COPY . .

CMD [ "gunicorn", "-b", "0.0.0.0:80", "app" ]