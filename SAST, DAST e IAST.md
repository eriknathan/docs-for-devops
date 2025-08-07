# SAST, DAST e IAST

Entenda a diferença entre esses testes de vulnerabilidade de aplicativos.
## O que são testes de segurança de aplicativos?

Testes de Segurança de Aplicativos servem para ajudar a descartar a possibilidade do código possuir algum erro ou defeito. Além disso, eles garantem que o software ou o aplicativo sejam executados sem problemas após o seu desenvolvimento.

Muitos incidentes de segurança são causados por invasores que exploram bugs de softwares. Por essa razão, os desenvolvedores de aplicativos precisam sempre estar atentos aos bugs desde o início do projeto. Isso, para que os riscos à segurança da informação que muitas empresas enfrentam atualmente sejam reduzidos.  

Para que o nível de segurança dos sistemas sejam mantidos, os desenvolvedores precisam testar de tempos em tempos o código, para que qualquer indício de bug seja resolvido com tempo hábil, evitando prejuízos financeiros ou uma má reputação da empresa, afinal um bug pode gerar mau desempenho do aplicativo, além de gerar avaliações ruins por parte dos usuários, comprometendo a marca da empresa no mercado.  

Calma, existem algumas ferramentas que ajudam o desenvolvedor a detectar falhas de segurança no código antes mesmo de serem incorporadas no lançamento final do software.  

Existem três tipos de testes que são os mais conhecidos, e são eles DAST, SAST e IAST.

## Explicando melhor sobre SAST, DAST e IAST e qual a diferença entre eles

A escolha entre adotar ferramentas de análise estática ou dinâmica depende principalmente do que o desenvolvedor quer, pois cada teste tem um propósito diferente e, por essa razão, deve ser realizado para ações e momentos específicos.  
### **Teste de segurança estático (SAST):**

Esse tipo de teste trabalha diretamente com o código de uma ferramenta. Os componentes de uma ferramenta são verificados sem que o produto seja executado e pode acontecer por meio de uma ferramenta automatizada ou através de testes manuais, o principal objetivo desse teste é identificar erros de programação, como, práticas ruins, erros de sintaxe e falhas de segurança.

A análise estática auxilia gestores de TI a identificar todas as linhas de código que foram mal escritas durante a criação de um software. Todos os caminhos de execução, processamento e exibição de valores são examinados. Como consequência, erros mais comuns são descobertos mais rapidamente.  
### **Teste de segurança dinâmico (DAST):** 

O teste dinâmico pode ser empregado de forma complementar a análise estática. Esse tipo de teste trabalha, principalmente, com as informações que são inseridas nas rotinas de entrada e saída de dados. Além disso, são verificados itens como, o tempo de resposta, a performance da aplicação, a capacidade do software se adaptar a diferentes ambientes e o comportamento funcional.

Muitas empresas adotam a análise dinâmica por ela permitir que problemas mais sutis sejam identificados. Não importa o grau de complexidade, as chances de um bug passar por uma análise estática e uma análise dinâmica sem ser rastreado é consideravelmente baixa. Dessa forma, o teste dinâmico consegue dar mais segurança e confiabilidade ao produto final.  
### **Teste de segurança interativo (IAST):**

O IAST usa instrumentação de software para avaliar o desempenho de um aplicativo e detectar vulnerabilidades. Agentes e sensores são executados para analisar de maneira contínua o funcionamento do aplicativo durante o teste automatizado, o teste manual ou uma combinação dos dois.

O processo e o feedback são feitos em tempo real em seu ambiente de desenvolvimento integrado (IDE), ambiente de integração contínua (CI) ou garantia de qualidade ou durante a produção. Os sensores têm acesso a código inteiro, fluxo de dados e fluxo de controle, dados de configuração do sistema, componentes da web e dados de conexão de back-end.
## Quais as vantagens e desvantagens de utilizar SAST?

As ferramentas SAST são uma tecnologia muito valiosa, mas não substituem outros métodos. Os desenvolvedores utilizaram uma combinação de técnicas em todo o processo para conduzir avaliações e detectar falhas antes de entrar em produção.  
  
**Vantagens:** As ferramentas SAST descobrem vulnerabilidades altamente complexas durante os primeiros estágios de desenvolvimento;  
As especificações de um problema são estabelecidas, inclusive a linha de código, tornando simples a correção da falha;

SAST pode ser integrado ao ambiente existente em diferentes pontos do ciclo de desenvolvimento de software ou aplicativo;

Fácil de examinar o código se comparado às auditorias manuais.  
  
**Desvantagens:** A implantação da tecnologia em escala pode ser um desafio para as empresas;

Modela o comportamento do código de maneira não muito precisa, dessa maneira, os desenvolvedores precisam estar atentos com alguns falsos positivos e falsos negativos;

Linguagens digitadas dinamicamente apresentam desafios;  
A ferramenta SAST precisa entender semanticamente muitas partes móveis do código que podem ser escritas em diferentes linguagens de programação;

Ele não pode testar o aplicativo no ambiente real, portanto, as vulnerabilidades na lógica do aplicativo ou na configuração insegura não são detectáveis.
## **Quais as vantagens e desvantagens de utilizar DAST?** 

Vale lembrar que a ferramenta SAST fornece feedback educacional aos desenvolvedores, enquanto o DAST oferece às equipes de segurança melhorias fornecidas rapidamente. Em alguns casos, ambos podem ser executados juntos, pois são ferramentas que se conectam ao processo de desenvolvimento em locais diferentes. O DAST deve ser usado com menos frequência e apenas por uma equipe de garantia de qualidade dedicada.  
  
**Vantagens:** Através da análise os desenvolvedores identificam os problemas de tempo de execução (falhas de autenticação, configuração de rede ou problemas que surgem após o login);

Caso de falso positivo são menos frequentes;

Suporte a linguagens de programação e estruturas personalizadas prontas para uso;

Alternativa com custo benefício melhor e menos complexo se comparado ao SAST.

**Desvantagens:** DAST não fornece informações sobre as causas subjacentes das vulnerabilidades e pode apresentar algumas dificuldades para manter os padrões de codificação;

Pela análise só poder ser feita em um aplicativo em execução, a ferramenta é considerada inadequada para os estágios anteriores de desenvolvimento;
## **Quais as vantagens e desvantagens de utilizar IAST?** 

A principal diferença do IAST  para SAST e DAST é que ele opera dentro do aplicativo. O acesso a uma ampla gama de dados torna a cobertura IAST maior, em comparação com o código-fonte ou a varredura HTTP. Além disso, permite uma saída mais precisa.  
  
**Vantagens:** Problemas são detectados mais cedo, logo IAST minimiza custos e atrasos devido a uma abordagem chamada Shift-left, o que significa que é realizada durante os estágios iniciais do ciclo de vida do projeto;

Análise IAST fornece linhas de código completas contendo dados, para que as equipes de segurança possam prestar atenção imediata a uma falha específica;

Identifica com precisão a origem dos pontos fracos devido a gama de informações que a ferramenta tem acesso;

IAST pode ser integrado em pipelines de CI / CD (integração e implantação contínuas) com facilidade, diferente dos outros testes.  
  
**Desvantagens:** A única desvantagem de IAST até o momento é que ela pode retardar a operação do aplicativo, isso porque os agentes servem essencialmente como instrumentação adicional, fazendo com que o código não tenha um bom desempenho algumas vezes;
## **Qual a importância dos testes de segurança em aplicativos?**

Um teste de segurança em aplicativos é importante para o seu negócio e para toda a empresa. São os testes que vão apontar se não há falhas no código do aplicativo, se está seguro e se tudo está funcionando corretamente.  

Os testes de segurança em aplicativos garantem que o sistema de informações seja protegido, bem como os dados. Além de manter sua funcionalidade em dia livre de vulnerabilidades. Todo o processo dos testes de desenvolvimento envolve desde a análise do aplicativo em busca das falhas técnicas, pontos fracos e vulneráveis. Até a parte de design, pois o objetivo é identificar os riscos e corrigi-los antes mesmo da sua implantação e lançamento final.  

Um aplicativo que não é testado e validado desde o início do seu desenvolvimento, pode ter vulnerabilidades existentes em seu código. Assim, pode falhar ao proteger os dados da empresa e de usuários contra os ataques maliciosos. Para que um aplicativo seja desenvolvido de forma segura, é imprescindível respeitar o ciclo de vida de desenvolvimento. E a segurança é um dos elementos mais importantes e que deve ser considerada durante todo o processo cíclico de desenvolvimento do aplicativo. Principalmente quando o aplicativo é desenvolvido para lidar com processos e informações críticas. Como por exemplo, um aplicativo que tem como foco vender ações de uma determinada empresa na bolsa, ou então, um simples aplicativo de e-commerce onde todos os dados de usuários são registrados.

A segurança de aplicativos e até mesmo de softwares são cada vez mais um dos pontos mais sensíveis da cibersegurança para as empresas. Para que os riscos sejam mitigados, as empresas precisam identificar as vulnerabilidades de forma ágil e eficiente.