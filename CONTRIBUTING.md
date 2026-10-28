# Guia de contribuição

Este guia descreve como trabalhar no Free Finance: o fluxo de trabalho, o padrão de codificação, as entidades do domínio, as estruturas de dados e a estimativa de volume que orienta as escolhas técnicas. O escopo do projeto está definido no Termo de Referência.

## Fluxo de trabalho

- A branch `main` guarda sempre uma versão que compila e roda. Não se faz commit direto nela.
- Cada tarefa tem sua branch, criada a partir da `main`: `feature/nome-da-tarefa` para funcionalidades e `fix/nome-do-problema` para correções.
- Os commits seguem o formato `tipo: descrição curta`, com os tipos `feat`, `fix`, `docs`, `refactor`, `test` e `chore`. Exemplo: `feat: add goal progress bar`.
- O código entra na `main` por Pull Request, com uma descrição do que mudou e de como testar.
- Cada módulo vem acompanhado de testes, e o projeto deve compilar antes do merge.

## Padrão de codificação

- Use nomes de variáveis, funções e classes em inglês.
- Mantenha a indentação consistente, com 4 espaços, como no guia de estilo do Kotlin.
- Siga as convenções do Kotlin: `PascalCase` para classes, `camelCase` para funções e variáveis, `UPPER_SNAKE_CASE` para constantes.
- Use comentários para explicar o motivo de trechos complexos, não para repetir o que o código já diz.
- Evite duplicação de código, criando funções reutilizáveis quando necessário.
- Guarde valores monetários em centavos, em `Long`, ou em `BigDecimal`. Nunca use `Double` para dinheiro.
- Não envie ao repositório arquivos de credenciais, como o `google-services.json`.

## Entidades

- **Usuário**: pessoa que usa o aplicativo, com nome e e-mail. A senha fica no serviço de autenticação e não é guardada no banco de dados.
- **Objetivo**: meta financeira do usuário, com descrição, valor alvo e prazo.
- **Contribuição**: valor que o usuário reserva para um objetivo, com valor e data.
- **Gasto diário**: despesa do dia a dia registrada pelo usuário, com valor, descrição e data.
- **Limite de gasto diário**: valor máximo que o usuário quer gastar em um dia.

## Estrutura de dados

Cada entidade é uma `data class` do Kotlin:

```kotlin
data class User(val id: String, val name: String, val email: String)

data class Goal(
    val id: String,
    val userId: String,
    val description: String,
    val targetAmountCents: Long,
    val deadline: LocalDate,
)

data class Contribution(
    val id: String,
    val goalId: String,
    val amountCents: Long,
    val date: LocalDate,
)

data class DailyExpense(
    val id: String,
    val userId: String,
    val amountCents: Long,
    val description: String,
    val date: LocalDate,
)

data class DailyLimit(val userId: String, val amountCents: Long)
```

Regras de modelagem:

- O progresso e o status de um objetivo não são armazenados. O progresso é a soma das contribuições dividida pelo valor alvo, e um objetivo novo começa em 0%.
- O tempo estimado de conclusão é calculado a partir do valor restante e da frequência das contribuições.
- O aviso de limite é emitido quando a soma dos gastos do dia passa do limite.
- No banco, uma organização possível é guardar objetivos e gastos de cada usuário em subcoleções do próprio usuário e as contribuições dentro de cada objetivo. Assim as consultas já saem filtradas por dono.

## Estimativa de volume de dados

As estimativas abaixo são hipóteses de dimensionamento, adotadas para orientar a escolha das estruturas de dados. O objetivo é definir a ordem de grandeza, e não prever números exatos.

Hipóteses:

- **10.000 usuários ativos em seis meses.** É uma meta de escala para testar se a solução aguenta uma base grande, não uma previsão de mercado.
- **3 objetivos por usuário**, uma média de metas ativas ao mesmo tempo.
- **8 contribuições por usuário por mês**, cerca de duas por semana.
- **60 gastos por usuário por mês**, cerca de dois por dia (um café, um lanche).
- **1 limite de gasto diário por usuário.**

| Coleção | Quantidade em 6 meses | Bytes por registro | Volume | Operação predominante |
|---|---|---|---|---|
| Usuários | 10.000 | 200 | 2 MB | Leitura única no login |
| Objetivos | 30.000 | 150 | 4,5 MB | Listagem e leitura do progresso |
| Contribuições | 480.000 | 60 | 28,8 MB | Inserção e soma por objetivo |
| Gastos diários | 3.600.000 | 120 | 432 MB | Inserção e soma do dia |
| Limites | 10.000 | 40 | 0,4 MB | Leitura a cada gasto registrado |
| **Total** | | | **cerca de 468 MB** | |

Por usuário, o volume é de cerca de 93 KB por ano, quase todo em gastos diários.

### Implicações

- Os dados de um usuário cabem inteiros na memória do dispositivo, então o volume total é uma preocupação apenas do banco de dados.
- O cálculo do progresso soma as contribuições de um objetivo (cerca de 16 em seis meses), e o total do dia soma cerca de dois gastos. Uma passada linear (O(n)) basta, sem estrutura sofisticada.
- As consultas mais frequentes são por usuário e por data, então a organização dos dados deve permitir filtrar por esses dois campos.
- O plano gratuito do Firebase (Spark) inclui 1 GiB de armazenamento, 50 mil leituras por dia e 20 mil escritas por dia. Confira os valores atuais em firebase.google.com/pricing, pois eles mudam, e note que valem para o Firestore (o Realtime Database tem cotas diferentes). Os 468 MB cabem no armazenamento, mas, no limite superior de 10.000 usuários, as escritas chegam a cerca de 22.700 por dia (20.000 de gastos e 2.700 de contribuições) e passam da cota. Como o projeto não tem orçamento para serviços pagos, 10.000 usuários é uma meta teórica, e a demonstração com poucos usuários cabe com folga.

### Limitações

Os valores de uso mensal assumem todos os usuários ativos desde o primeiro mês, o que os torna um limite superior. Os tamanhos por registro são aproximados e não incluem o espaço adicional do banco de dados (índices e metadados).
