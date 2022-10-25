# Curso de Integração contínua ou CI - Continuous Integration

> ## Menu
>
>
> - [Home](README.md)
> - [Introdução](#introdução)

---

### Introdução

#### **O que é**

É o processo de integrar modificações do codebase de forma contínua e automatizada, evitando assim erros humanos de verificação, garantindo mais agilidade e segurança no processo de desenvolvimento de um software.

#### **Principais processos**

- Execução de testes
- Linter
- Verificações de qualidade de código
- Verificações de segurança
- Geração de artefatos prontos para o processo de deploy
- Identificação da próxima versão a ser gerada no software
- Geração de tags e releases

#### **Ferramentas populares**

- Jenkins
- Github Actions
- Circle CI
- AWS Code Build
- Azure DevOps
- Google Cloud Build
- Gitlab Pipelines / CI

#### **Para o nosso estudo vamos utilizar o github actions**

O github actions e baseado em um ou mais workflows.

> #### **Wokflows**
>
> - São conjuntos de processos definidos por você. Ex: Fazer o build + rodar os testes da aplicação
> - É possível ter mais do que um workflow por repositório
> - Definidos em arquivo ".yml" em: .github/workflows
> - Possui um ou mais "Jobs"
> - É iniciado baseado em eventos do Github ou através de agendamento
  
![eventos](images/eventos-workflow.png)

#### **Actions**

É a ação que de fato será executada em um dos Steps de um Job em um Workflow.
Ela pode ser criada do zero ou ser reutilizada de actions pre-existentes. Uma action pode ser desenvolvida em Javascript ou podemos utilizar uma docker image.
Temos o Github Marketplace onde você pode encontrar várias actions prontas para utilizar.
