# A imagem de Build, para construir o virtual env
FROM python:3.11-buster AS builder

# Instalacao do Poetry no Container
RUN pip install poetry==1.7.1

# Variaveis de Ambiente de apoio na construcao
ENV POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

# Define a pasta padrao do projeto
WORKDIR /app

# Migra as definicoes de projeto do poetry
COPY utils/pyproject.toml utils/poetry.lock ./

# Instala as dependencias a partir das definicoes de projeto
RUN --mount=type=cache,target=$POETRY_CACHE_DIR poetry install --no-root

# A imagem de execucao, utilizada para executar que estara no virtual env
FROM python:3.11-slim-buster AS runtime

# Instala as dependencias Java e procps
RUN apt-get update && apt-get install -y openjdk-11-jdk procps

# Define a variavel JAVA_HOME
ENV JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
ENV PATH="$JAVA_HOME/bin:$PATH"

# Define a variavel do ambiente virtual
ENV VIRTUAL_ENV=/app/.venv \
    PATH="/app/.venv/bin:$PATH"

# Copia o ambiente da imagem builder para imagem de execucao
COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

# Define a pasta padrao do projeto
WORKDIR /app

# Migra os dados e notebooks de teste
COPY data/ ./app
COPY notebooks/ ./app

# Executa um comando no CMD para rodar o JupyterLab ao iniciar o container
CMD ["jupyter-lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]