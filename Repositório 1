import requests
import json
from statistics import mean

USERNAME = "henriquegg10"

# Repositórios famosos para análise
OPEN_SOURCE_REPO = "numpy/numpy"

# Repositórios que serão usados para criar o dataset
REPOSITORIES = [
    "numpy/numpy",
    "tensorflow/tensorflow",
    "pallets/flask",
    "django/django",
    "facebook/react"
]

BASE_URL = "https://api.github.com"

# ============================================================
# CONFIGURAÇÃO DAS REQUISIÇÕES
# ============================================================

headers = {
    "Accept": "application/vnd.github+json"
}

if GITHUB_TOKEN:
    headers["Authorization"] = f"Bearer {GITHUB_TOKEN}"


def requisicao(url, params=None):
    """Faz uma requisição para a API do GitHub."""
    resposta = requests.get(
        url,
        headers=headers,
        params=params,
        timeout=10
    )

    if resposta.status_code != 200:
        print(f"Erro na API: {resposta.status_code}")
        print(resposta.text)
        return None

    return resposta.json()

# 1. EXPLORAR A PRÓPRIA CONTA


def analisar_usuario(username):
    print("\n" + "=" * 60)
    print("1. INFORMAÇÕES DA CONTA")
    print("=" * 60)

    # Requisição para /users/{username}
    url_usuario = f"{BASE_URL}/users/{username}"
    usuario = requisicao(url_usuario)

    if not usuario:
        return None

    print(f"Usuário: {usuario['login']}")
    print(f"Nome: {usuario.get('name')}")
    print(f"Seguidores: {usuario['followers']}")
    print(f"Seguindo: {usuario['following']}")
    print(f"Repositórios públicos: {usuario['public_repos']}")

    
    # Listar todos os repositórios públicos
    
    repositorios = []

    pagina = 1

    while True:
        url_repos = f"{BASE_URL}/users/{username}/repos"

        dados = requisicao(
            url_repos,
            params={
                "per_page": 100,
                "page": pagina,
                "type": "public"
            }
        )

        if not dados:
            break

        repositorios.extend(dados)

        if len(dados) < 100:
            break

        pagina += 1

    print("\nRepositórios públicos:")

    total_estrelas = 0

    lista_repos = []

    for repo in repositorios:
        estrelas = repo["stargazers_count"]
        forks = repo["forks_count"]

        total_estrelas += estrelas

        print(
            f"- {repo['name']} | "
            f"Estrelas: {estrelas} | "
            f"Forks: {forks}"
        )

        lista_repos.append({
            "nome": repo["name"],
            "url": repo["html_url"],
            "linguagem": repo["language"],
            "estrelas": estrelas,
            "forks": forks,
            "issues_abertas": repo["open_issues_count"]
        })

    print(f"\nTotal de estrelas acumuladas: {total_estrelas}")

    return {
        "usuario": usuario["login"],
        "nome": usuario.get("name"),
        "seguidores": usuario["followers"],
        "seguindo": usuario["following"],
        "repositorios_publicos": usuario["public_repos"],
        "total_estrelas": total_estrelas,
        "repositorios": lista_repos
    }


# 2. ANALISAR PROJETO OPEN-SOURCE

def analisar_projeto(repo_name):
    print("\n" + "=" * 60)
    print("2. ANÁLISE DO PROJETO OPEN-SOURCE")
    print("=" * 60)

    url = f"{BASE_URL}/repos/{repo_name}"

    projeto = requisicao(url)

    if not projeto:
        return None

    print(f"Projeto: {projeto['full_name']}")
    print(f"Descrição: {projeto.get('description')}")
    print(f"Estrelas: {projeto['stargazers_count']}")
    print(f"Forks: {projeto['forks_count']}")
    print(f"Issues abertas: {projeto['open_issues_count']}")
    print(f"Linguagem principal: {projeto.get('language')}")

    # --------------------------------------------------------
    # Identificar linguagens utilizadas
    # --------------------------------------------------------

    url_linguagens = f"{BASE_URL}/repos/{repo_name}/languages"

    linguagens = requisicao(url_linguagens)

    if linguagens:
        print("\nLinguagens utilizadas:")

        for linguagem, quantidade in linguagens.items():
            print(f"- {linguagem}: {quantidade} bytes")

    return {
        "repositorio": projeto["full_name"],
        "descricao": projeto.get("description"),
        "url": projeto["html_url"],
        "estrelas": projeto["stargazers_count"],
        "forks": projeto["forks_count"],
        "issues_abertas": projeto["open_issues_count"],
        "linguagem_principal": projeto.get("language"),
        "linguagens": linguagens
    }



# 3. CRIAR DATASET PERSONALIZADO

def criar_dataset():
    print("\n" + "=" * 60)
    print("3. CRIANDO DATASET")
    print("=" * 60)

    dataset = []

    for repo_name in REPOSITORIES:

        print(f"\nColetando: {repo_name}")

        url = f"{BASE_URL}/repos/{repo_name}"

        repo = requisicao(url)

        if not repo:
            continue

        # Buscar linguagens
        url_linguagens = f"{BASE_URL}/repos/{repo_name}/languages"

        linguagens = requisicao(url_linguagens)

        dados = {
            "nome": repo["name"],
            "nome_completo": repo["full_name"],
            "proprietario": repo["owner"]["login"],
            "descricao": repo.get("description"),
            "url": repo["html_url"],
            "estrelas": repo["stargazers_count"],
            "forks": repo["forks_count"],
            "issues_abertas": repo["open_issues_count"],
            "linguagem_principal": repo.get("language"),
            "linguagens": list(linguagens.keys()) if linguagens else [],
            "data_criacao": repo["created_at"],
            "ultima_atualizacao": repo["updated_at"]
        }

        dataset.append(dados)

    
    # Salvar JSON
    

    with open(
        "dataset_github.json",
        "w",
        encoding="utf-8"
    ) as arquivo:

        json.dump(
            dataset,
            arquivo,
            indent=4,
            ensure_ascii=False
        )

    print("\nDataset salvo em: dataset_github.json")

    return dataset


# 4. ESTATÍSTICAS BÁSICAS


def gerar_estatisticas(dataset):

    print("\n" + "=" * 60)
    print("4. ESTATÍSTICAS DO DATASET")
    print("=" * 60)

    if not dataset:
        print("Nenhum dado disponível.")
        return

    total_repos = len(dataset)

    total_estrelas = sum(
        repo["estrelas"]
        for repo in dataset
    )

    total_forks = sum(
        repo["forks"]
        for repo in dataset
    )

    total_issues = sum(
        repo["issues_abertas"]
        for repo in dataset
    )

    media_estrelas = mean(
        repo["estrelas"]
        for repo in dataset
    )

    media_forks = mean(
        repo["forks"]
        for repo in dataset
    )

    # Contar linguagens
    contador_linguagens = {}

    for repo in dataset:
        for linguagem in repo["linguagens"]:

            if linguagem not in contador_linguagens:
                contador_linguagens[linguagem] = 0

            contador_linguagens[linguagem] += 1

    print(f"Quantidade de repositórios: {total_repos}")
    print(f"Total de estrelas: {total_estrelas}")
    print(f"Total de forks: {total_forks}")
    print(f"Total de issues abertas: {total_issues}")
    print(f"Média de estrelas: {media_estrelas:.2f}")
    print(f"Média de forks: {media_forks:.2f}")

    print("\nLinguagens encontradas:")

    for linguagem, quantidade in contador_linguagens.items():
        print(f"- {linguagem}: {quantidade} repositório(s)")

    # Salvar estatísticas
    estatisticas = {
        "quantidade_repositorios": total_repos,
        "total_estrelas": total_estrelas,
        "total_forks": total_forks,
        "total_issues_abertas": total_issues,
        "media_estrelas": round(media_estrelas, 2),
        "media_forks": round(media_forks, 2),
        "linguagens": contador_linguagens
    }

    with open(
        "estatisticas.json",
        "w",
        encoding="utf-8"
    ) as arquivo:

        json.dump(
            estatisticas,
            arquivo,
            indent=4,
            ensure_ascii=False
        )

    print("\nEstatísticas salvas em: estatisticas.json")



# PROGRAMA PRINCIPAL


def main():

    print("=" * 60)
    print("ANÁLISE DE REPOSITÓRIOS DO GITHUB")
    print("=" * 60)

    # 1 - Minha conta
    dados_usuario = analisar_usuario(USERNAME)

    # 2 - Projeto open-source
    dados_projeto = analisar_projeto(OPEN_SOURCE_REPO)

    # 3 - Dataset
    dataset = criar_dataset()

    # 4 - Estatísticas
    gerar_estatisticas(dataset)

    print("\n" + "=" * 60)
    print("PROGRAMA FINALIZADO!")
    print("=" * 60)


if __name__ == "__main__":
    main()
