# Cursos

> ## Menu
>
> - [Docker](Docker.md)
> - [Git](Git.md)
> - [Integração Continua](integracao-continua.md)
> - [Comunicação entre sistemas](comunicacao-entre-sistemas.md)

---

flowchart TB
    ops(["👤 Time de Operações\nIntervenções manuais"])

    subgraph cora ["Cora (sistemas internos)"]
        smb["SMB\nconta-digital/smb\nGestão de empresas"]
        funding["Funding\nconta-digital/funding\nContas bancárias"]
        scheduler["kafka-message-scheduler\nAgenda mensagens com delay"]
        bsr["⭐ bankslip-recipients\nCadastro de beneficiários\nde boleto na Nuclea"]
    end

    jd["JD / Nuclea\nBanco Central\nHomologa beneficiários"]
    newrelic["New Relic\nObservabilidade / APM"]

    smb -->|"Kafka: person-business-association-event-v2\n(empresa ativa criada)"| bsr
    funding -->|"Kafka: funding-event-account-created\n(conta bancária criada)"| bsr
    bsr -->|"HTTPS REST\n(autentica, cadastra, verifica)"| jd
    bsr -->|"HTTP REST (fallback)\n(dados de conta bancária)"| funding
    bsr -->|"HTTP REST (backoffice)\n(dados de empresa)"| smb
    bsr -->|"Kafka: kafka-message-scheduler-command-schedule\n(agenda retry / janela Nuclea)"| scheduler
    scheduler -->|"Kafka: bankslip-recipients-internal-command-register\nbankslip-recipients-command-check"| bsr
    ops -->|"HTTP REST\n/backoffice/recipients"| bsr
    bsr -->|"New Relic Agent\n(traces, logs, métricas)"| newrelic
