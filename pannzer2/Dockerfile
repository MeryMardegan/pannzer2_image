# Base do SO dentro do container
FROM ubuntu:22.04

# Ambiente
ENV DEBIAN_FRONTEND=noninteractive PIP_NO_CACHE_DIR=1 LC_ALL=C PYTHONUNBUFFERED=1

# Instalando as dependências de sistema que o PANNZER2 precisa
# Usando --no-install-recommends para evitar instalar pacotes desnecessários
# rm -rf /var/lib/apt/lists/* para limpar o cache do apt e reduzir o tamanho da imagem
# Debian frontend noninteractive para evitar prompts interativos durante a instalação
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 python3-pip perl wget curl ca-certificates ncbi-blast+ build-essential cmake unzip \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Instalação do SANSPANZ.3 (PANNZER2)
WORKDIR /opt
RUN curl -fsSL http://ekhidna2.biocenter.helsinki.fi/sanspanz/SANSPANZ.3.tar.gz -o SANSPANZ.3.tar.gz \
 && tar -xzf SANSPANZ.3.tar.gz \
 && rm SANSPANZ.3.tar.gz

# Dependências Python do repo
COPY panzzer2/requirements.txt /opt/SANSPANZ.3/requirements.txt
RUN pip3 install --no-cache-dir -r /opt/SANSPANZ.3/requirements.txt || true

# Atalhos no PATH (apontando para SANSPANZ.3)
RUN ln -s /opt/SANSPANZ.3/runsanspanz.py /usr/local/bin/runsanspanz.py \
 && ln -s /opt/SANSPANZ.3/anno2html.pl   /usr/local/bin/anno2html.pl

# PATH com binários do SANSPANZ.3
ENV PATH="/opt/SANSPANZ.3:${PATH}"

# Diretório padrão para I/O
WORKDIR /data

# ENTRYPOINT para travar no cliente:
ENTRYPOINT ["runsanspanz.py"]


