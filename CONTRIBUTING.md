# Padrão de codificação

- Utilize nomes de variáveis e funções em inglês.
- Mantenha a indentação consistente.
- Use comentários para explicar trechos de código complexos.
- Evite duplicação de código, criando funções reutilizáveis quando necessário.


## Entidades

- **Usuário**: Representa o usuário do aplicativo, contendo informações como nome, email e senha.
- **Receita**: Representa uma entrada de receita, contendo informações como valor, categoria e data.
- **Despesa**: Representa uma entrada de despesa, contendo informações como valor, categoria e data.
- **Relatório**: Representa um relatório financeiro, contendo informações sobre receitas e despesas em um determinado período.

# Estimativa de volume de dados

As estimativas abaixo são hipóteses de dimensionamento, adotadas para orientar a escolha das estruturas de dados. O objetivo é definir a ordem de grandeza (centenas de MB no total, dezenas de KB por usuário), e não prever números exatos.

- **Usuários**: adota-se como meta de escala 10.000 usuários ativos nos primeiros seis meses. Esse valor é um cenário de dimensionamento, não uma previsão de mercado, e serve para testar se a solução continua adequada em uma base grande. Cada usuário ocupa cerca de 200 bytes (identificador, nome, e-mail e datas), totalizando aproximadamente 2 MB. A senha não entra nesse cálculo, pois fica sob responsabilidade do serviço de autenticação. A operação predominante é a leitura única no login.

- **Receitas e Despesas**: estima-se que cada usuário registre em média 20 transações por mês, o que representa o registro dos gastos e ganhos principais, e não de cada pequena compra. Com 10.000 usuários, isso resulta em aproximadamente 200.000 transações mensais. Assume-se que 85% sejam despesas (170.000/mês) e 15% receitas (30.000/mês), já que as despesas ocorrem com muito mais frequência que as entradas de renda. Considerando cerca de 150 bytes por transação (identificador, usuário, valor, categoria, data e descrição), o volume cresce cerca de 30 MB por mês, chegando a aproximadamente 1,2 milhão de registros e 180 MB em seis meses. Por usuário, o volume é de 240 transações por ano, ou cerca de 36 KB. A operação predominante é a inserção frequente e a consulta por período (mês).

- **Relatórios**: os relatórios não são armazenados, pois são calculados sob demanda a partir das receitas e despesas do período. Por isso, o volume armazenado dessa entidade é nulo, e a estimativa refere-se ao uso: espera-se que cerca de 50% dos usuários ativos acessem relatórios, resultando em aproximadamente 5.000 visualizações por mês. Cada relatório percorre em média 20 transações (as de um mês de um usuário), o que torna o cálculo barato.

**Limitações das estimativas**

Os valores de transações por mês assumem todos os usuários ativos desde o primeiro mês, o que os torna um limite superior. Os tamanhos por registro são aproximados e não incluem o espaço adicional usado pelo banco de dados (índices e metadados).

**Implicações para as estruturas de dados**

Como cada usuário possui apenas dezenas de KB, todos os dados de um usuário cabem na memória do dispositivo, e o volume global é uma preocupação apenas do banco de dados. Isso permite usar, no aplicativo, listas ou mapas agrupados por mês, e justifica a agregação linear (O(n)) para gerar os relatórios.

# Estrutura de dados

- **Usuário**: Estrutura contendo campos para nome, email e senha. Pode ser representada como uma classe ou struct.
- **Receita**: Estrutura contendo campos para valor, categoria e data. Pode ser representada como uma classe ou struct.
- **Despesa**: Estrutura contendo campos para valor, categoria e data.
- **Relatório**: Estrutura contendo campos para receitas e despesas em um determinado período. Pode ser representada como uma classe ou struct.
