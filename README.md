# Free Finance

Aplicativo Android para ajudar jovens a organizar o dinheiro: o usuário cria objetivos financeiros, acompanha a evolução de cada um conforme suas contribuições e controla os gastos do dia a dia com um limite diário.

Projeto de TCC do curso de Engenharia de Software da UNIASSELVI, desenvolvido por Lucas Ortiz.

## Sobre o projeto

Muitos jovens entre 18 e 25 anos têm dificuldade de se organizar financeiramente. Anotar gastos em cadernos ou planilhas ajuda, mas não impede gastos por impulso. O Free Finance transforma o ato de contribuir para uma meta em algo mais interativo, em que o usuário vê o progresso crescer a cada contribuição.

## Funcionalidades

- Login com conta Google ou criação de conta com e-mail.
- Cadastro de objetivo financeiro, com descrição, valor alvo e prazo.
- Registro de contribuição a um objetivo, com valor e data.
- Acompanhamento do progresso de cada objetivo em uma barra de percentual (começa em 0%), com o tempo estimado de conclusão conforme a frequência das contribuições.
- Listagem dos objetivos do usuário e de seus status.
- Limite de gasto diário, com aviso quando o valor gasto passar do limite.
- Listagem dos gastos diários e da soma do valor total.

### Fora do escopo

- Cálculo de financiamentos.
- Acompanhamento de investimentos.
- Versões para iOS e web (o projeto é apenas para Android).

## Tecnologias

- Kotlin
- Android Studio
- Firebase (autenticação e banco de dados)

## Como utilizar o Free Finance

1. Instale o pacote do aplicativo (APK) em um dispositivo Android.
2. Abra o aplicativo e entre com sua conta Google ou crie uma conta com e-mail.
3. Crie um objetivo informando a descrição, o valor alvo e o prazo.
4. Registre suas contribuições e acompanhe o progresso tocando no objetivo.
5. Defina seu limite de gasto diário e registre os gastos do dia. O aplicativo avisa se você passar do limite.

As contribuições são lançadas manualmente. O aplicativo não acessa dados bancários.

## Como executar o projeto (desenvolvedores)

Pré-requisitos:

- Android Studio atualizado.
- Um dispositivo Android ou emulador.
- Um projeto Firebase próprio, com autenticação (Google e e-mail/senha) e banco de dados habilitados.

Passos:

1. Clone o repositório: `git clone <url-do-repositorio>`.
2. Abra a pasta do projeto no Android Studio e aguarde a sincronização do Gradle.
3. Baixe o `google-services.json` do seu projeto Firebase e coloque-o na pasta do módulo `app` (ajuste se a estrutura do projeto for diferente).
4. Execute o aplicativo no dispositivo ou no emulador.

O arquivo `google-services.json` não deve ser enviado ao repositório público. Adicione-o ao `.gitignore`.

## Como contribuir

Leia o [CONTRIBUTING.md](CONTRIBUTING.md) para conhecer o fluxo de trabalho, o padrão de codificação, as entidades e a estimativa de volume de dados.

## Status

Em desenvolvimento. Conclusão prevista para 10 de dezembro de 2026.

## Autor

Lucas Ortiz, TCC de Engenharia de Software, UNIASSELVI.
