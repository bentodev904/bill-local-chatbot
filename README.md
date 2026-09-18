# Bill — Chatbot com execução local

Chatbot web com backend em Python/FastAPI e modelo Qwen3 executado pelo Ollama em uma VM ARM da Oracle Cloud.

Projeto de aprendizado sobre integração com modelos de linguagem, APIs, interface web e implantação de serviços Linux.

## Arquitetura

```text
Navegador → FastAPI → Ollama → Qwen3
```

O navegador envia a mensagem e o histórico recente ao FastAPI. O backend consulta o Ollama dentro da VM e devolve a resposta à interface.

## Tecnologias

- Python, FastAPI, Uvicorn e HTTPX
- HTML, CSS e JavaScript
- Ollama e Qwen3 1.7B
- Ubuntu 22.04 ARM64
- Tailscale para acesso privado
- systemd para execução contínua

## Funcionalidades

- Interface web para envio de mensagens.
- Histórico das últimas três trocas, mantido na página.
- Instrução de sistema para definir nome, idioma e estilo.
- Tratamento de falhas de conexão e tempo de espera do modelo.
- Execução como serviço independente da sessão SSH.

## Como executar

Requisitos: Python 3.10 ou superior e Ollama instalado e em execução.

Baixe o modelo:

```bash
ollama pull qwen3:1.7b
```

Na pasta do projeto, prepare o ambiente:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Inicie a aplicação:

```bash
python -m uvicorn main:app --host 127.0.0.1 --port 8001
```

No mesmo computador, acesse:

- Interface: http://127.0.0.1:8001
- Documentação da API: http://127.0.0.1:8001/docs

O backend espera encontrar o Ollama em `127.0.0.1:11434`.

Para acesso remoto pelo Tailscale, execute o backend usando o IP Tailscale do servidor no parâmetro `--host`. A API do Ollama permanece acessível apenas dentro do servidor.

## Resultados iniciais

Ambiente: VM com quatro núcleos ARM Neoverse-N1, aproximadamente 24 GB de RAM e execução em CPU.

| Medida | Resultado observado |
|---|---|
| Modelo | Qwen3 1.7B |
| Arquivo do modelo | Aproximadamente 1,4 GB |
| Tamanho carregado informado pelo Ollama | Aproximadamente 1,9 GB |
| Velocidade de geração em dois testes curtos | 21–23 tokens/s |

Uma função Python gerada pelo modelo passou em verificações de cálculo, lista vazia e preservação dos dados de entrada. Também foram observadas imprecisões em explicações e dificuldades para seguir algumas instruções de estilo.

Esses resultados são exploratórios e não representam um benchmark completo.

## Limitações

- O histórico é apagado ao atualizar ou fechar a página.
- Apenas as últimas seis mensagens anteriores são enviadas ao modelo.
- Limitar a quantidade de mensagens não garante que todo o conteúdo caiba na janela de contexto.
- As respostas aparecem depois que a geração termina.
- A geração está limitada a 512 tokens e pode ser interrompida nesse limite.
- O modelo pode inventar informações ou ignorar instruções.
- A aplicação não possui autenticação própria e foi configurada para acesso privado pelo Tailscale.

## Custos

A inferência ocorre na própria VM, sem chamadas a uma API paga por token. Os custos de infraestrutura dependem da configuração e do contrato da conta Oracle Cloud.

## Próximos passos

- Exibir respostas progressivamente.
- Adicionar botão para iniciar uma nova conversa.
- Melhorar o controle do tamanho do histórico.
- Comparar modelos com um conjunto fixo de perguntas.
- Ampliar os testes do backend.
