<p align="center">
  <img src="./docs/assets/baixaFidelidade/PitStop.png" alt="Logo do Projeto PitStop" width="250">
</p>

# 📄 Documentação do Projeto PitStop

Bem-vindo ao repositório de documentação do Projeto PitStop. Este site contém todos os artefatos de planejamento, arquitetura e design que guiam o desenvolvimento do sistema.

O site é construído utilizando [MkDocs](https://www.mkdocs.org/) com o tema [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/), permitindo a criação de uma documentação bonita e responsiva a partir de arquivos Markdown.

---

## 🚀 Rodando o Projeto Localmente

Para visualizar e editar o site de documentação em sua máquina, siga os passos abaixo.

### Pré-requisitos

* **Python 3** e **Pip** instalados no seu sistema.

### Instalação

1.  **Clone o repositório:**
    ```bash
    git clone [https://github.com/pitsttop/pitstop-doc.git](https://github.com/pitsttop/pitstop-doc.git)
    cd pitstop-doc
    ```

2.  **Crie e ative um ambiente virtual (Recomendado):**
    ```bash
    # Crie o ambiente (só precisa fazer uma vez)
    python3 -m venv venv

    # Ative o ambiente (precisa fazer toda vez que for trabalhar no projeto)
    source venv/bin/activate
    ```

3.  **Instale as dependências:**
    ```bash
    pip install -r requirements.txt
    ```
    *(Este comando lê o arquivo `requirements.txt` e instala o MkDocs e o tema Material na versão exata que estamos usando).*

### Executando o Servidor de Desenvolvimento

Com o ambiente virtual ativo e as dependências instaladas, inicie o servidor local:

```bash
mkdocs serve
