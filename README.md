# Cursos

> ## Menu
>
> - [Docker](Docker.md)
> - [Git](Git.md)
> - [Integração Continua](integracao-continua.md)
> - [Comunicação entre sistemas](comunicacao-entre-sistemas.md)

---


C4Component
    title Componentes — Cadastro de Beneficiário

    Container_Boundary(primary, "Primary Adapters") {
        Component(pba_consumer, "PersonBusinessAssociationConsumer", "Kafka Listener", "Tópico: person-business-association-event-v2")
        Component(internal_consumer, "InternalRegisterCommandConsumer", "Kafka Listener", "Tópico: bankslip-recipients-internal-command-register (retry/agendado)")
    }

    Container_Boundary(domain, "Domain") {
        Component(register_uc, "RegisterRecipientUseCase", "Use Case", "Idempotência, janela Nuclea, busca conta, gera ControlNumber, chama JD")
        Component(register_svc, "RecipientRegisterService", "Domain Service", "Autentica com JD (cache de token) e delega chamadas de registro")
        Component(event_publisher, "EventPublisher", "Domain Service", "Persiste evento no outbox e publica no Kafka")
    }

    Container_Boundary(secondary, "Secondary Adapters") {
        Component(pg_recipient, "RecipientPostgresAdapter", "JPA", "CRUD na tabela recipients")
        Component(pg_bank, "BankAccountPostgresqlAdapter", "JPA", "Cache de contas bancárias")
        Component(pg_token, "TokenPostgresAdapter", "JPA", "Cache de token JD (tabela tokens)")
        Component(pg_event, "EventPostgresAdapter", "JPA", "Outbox — tabela event")
        Component(jd_adapter, "RecipientRegisterJDAdapter", "OkHttp", "POST /bnf/v1/reqcip/auth e /bnf/v1/reqcip/inc")
        Component(funding_adapter, "FundingHttpAdapter", "OkHttp", "GET /bank-account/admin/data (fallback)")
        Component(check_producer, "RegisterCheckCommandAdapter", "Kafka Producer", "Tópico: bankslip-recipients-command-check")
        Component(scheduler_producer, "KafkaMessageSchedulerCommandKafkaAdapter", "Kafka Producer", "Tópico: kafka-message-scheduler-command-schedule")
    }

    Rel(pba_consumer, register_uc, "register(business)")
    Rel(internal_consumer, register_uc, "register(business)")
    Rel(register_uc, register_svc, "register(recipient)")
    Rel(register_svc, jd_adapter, "authenticate() / register()")
    Rel(register_svc, pg_token, "fetchCurrent() / save()")
    Rel(register_uc, pg_recipient, "findByBusinessId / save / nextSequenceId")
    Rel(register_uc, pg_bank, "findByBusinessId")
    Rel(register_uc, funding_adapter, "findByBusinessId (fallback)")
    Rel(register_uc, event_publisher, "publish(CheckCommand | ScheduleRegisterCommand)")
    Rel(event_publisher, pg_event, "save / delete")
    Rel(event_publisher, check_producer, "publishCheck()")
    Rel(event_publisher, scheduler_producer, "schedule()")
