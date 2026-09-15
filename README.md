# Análise de Repositórios do GitHub

Projeto desenvolvido para consumir a API do GitHub, coletar informações de usuários e repositórios e gerar um dataset personalizado em formato JSON.

## Objetivo

O projeto foi desenvolvido para realizar três atividades principais:

1. Explorar uma conta do GitHub.
2. Analisar um projeto open-source.
3. Criar um dataset personalizado com dados de vários repositórios.

## Funcionalidades

### 1. Explorar conta do GitHub

O programa realiza uma requisição para:

`/users/{username}`

A partir disso, são obtidas informações como:

- Nome do usuário
- Nome da conta
- Quantidade de seguidores
- Quantidade de pessoas seguindo
- Quantidade de repositórios públicos
- Repositórios públicos
- Quantidade de estrelas de cada repositório
- Total de estrelas acumuladas

O usuário analisado neste projeto é:

`henriquegg10`

### 2. Analisar projeto open-source

O projeto utilizado como exemplo é:

`numpy/numpy`

São coletadas as seguintes informações:

- Nome do projeto
- Descrição
- URL
- Quantidade de estrelas
- Quantidade de forks
- Quantidade de issues abertas
- Linguagem principal
- Linguagens utilizadas no projeto

### 3. Criar dataset personalizado

O programa coleta informações de vários repositórios públicos:

- numpy/numpy
- tensorflow/tensorflow
- pallets/flask
- django/django
- facebook/react

Os dados são organizados e salvos no arquivo:

`dataset_github.json`

### 4. Estatísticas

Após coletar os dados, o programa calcula:

- Quantidade de repositórios analisados
- Total de estrelas
- Total de forks
- Total de issues abertas
- Média de estrelas
- Média de forks
- Quantidade de repositórios por linguagem

As estatísticas são salvas no arquivo:

`estatisticas.json`

## Tecnologias utilizadas

- Python
- API REST do GitHub
- Biblioteca `requests`
- JSON

## Instalação

Primeiro, clone o repositório:

```bash
git clone https://github.com/henriquegg10/Repositorio-rio-1.git
