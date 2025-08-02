FROM tiangolo/uwsgi-nginx-flask:python3.6
WORKDIR /app
RUN pip install redis pytest
COPY /azure-vote .
COPY /tests ./tests