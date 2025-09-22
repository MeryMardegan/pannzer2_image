# Escolhendo a base do SO dentro do container
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive PIP_NO_CACHE_DIR=1 LC_ALL=C PYTHONUNBUFFERED=1

# Instalando as dependências de sistema que o PANNZER2 precisa
# Usando --no-install-recommends para evitar instalar pacotes desnecessários
# rm -rf /var/lib/apt/lists/* para limpar o cache do apt e reduzir o tamanho da imagem
# Debian frontend noninteractive para evitar prompts interativos durante a instalação
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 python3-pip perl git wget curl ca-certificates ncbi-blast+ build-essential cmake unzip \
 && apt-get clean \ 
 && rm -rf /var/lib/apt/lists/*

# Defininando a pasta de trabalho
WORKDIR /opt
# Baixando e extraindo a ferramenta
RUN curl -fsSL http://ekhidna2.biocenter.helsinki.fi/sanspanz/SANSPANZ.3.tar.gz \
    -o SANSPANZ.3.tar.gz \
    && tar -xvzf SANSPANZ.3.tar.gz \
    && rm SANSPANZ.3.tar.gz

# instalar deps Python que listados na repo repo
COPY requirements.txt /opt/SANSPANZ.3/requirements.txt
RUN pip3 install --no-cache-dir -r /opt/SANSPANZ.3/requirements.txt || true

# Baixando as dependências do python e o || true para evitar falhas caso já estejam instaladas
RUN pip3 install --no-cache-dir -r /opt/pannzer2/requirements.txt || true

# Atalhos no PATH
RUN ln -s /opt/pannzer2/runsanspanz.py /usr/local/bin/runsanspanz.py || true \
 && ln -s /opt/pannzer2/pannzer2.py     /usr/local/bin/pannzer2.py     || true

# Criando o atalho no path para chamar o PANNZER2 de qualquer lugar garantindo que o binários estejam acessíveis
ENV PATH="/opt/pannzer2:${PATH}" \

# Diretório padrão para I/O
WORKDIR /data

# ENTRYPOINT para travar no cliente:
ENTRYPOINT ["runsanspanz.py"]
