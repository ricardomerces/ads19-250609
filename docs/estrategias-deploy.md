---
## Estratégias de Deploy no Kubernetes

O Kubernetes oferece diversas estratégias para implantar aplicações, cada uma com suas **vantagens e desvantagens**. A escolha da estratégia ideal depende de fatores como a criticidade da aplicação, a tolerância a tempo de inatividade, a complexidade do rollback e a necessidade de testes A/B.

---
### 1. Recreate (Recriar)

A estratégia **Recreate** é a mais simples. Ela consiste em **derrubar todas as instâncias existentes** da aplicação e, em seguida, iniciar as **novas instâncias** com a nova versão.

* **Vantagens:**
    * **Simplicidade:** Fácil de implementar e entender.
    * **Consumo de recursos:** Não exige recursos extras durante o deploy.
* **Desvantagens:**
    * **Tempo de inatividade (Downtime):** Causa interrupção total do serviço durante o deploy, tornando-a inadequada para aplicações críticas.
    * **Risco:** Se algo der errado com a nova versão, o serviço permanecerá indisponível.

---
### 2. Rolling Update (Atualização Contínua)

Esta é a estratégia de deploy **padrão no Kubernetes** e a mais comumente utilizada. Ela atualiza as instâncias da aplicação **gradualmente**, uma ou algumas por vez, garantindo que sempre haja instâncias da versão antiga em execução enquanto as novas estão sendo implantadas. Isso minimiza o tempo de inatividade.

* **Vantagens:**
    * **Zero Downtime:** Não há interrupção no serviço, pois as instâncias antigas continuam a atender requisições enquanto as novas são implantadas.
    * **Rollback fácil:** Se algo der errado, é fácil reverter para a versão anterior.
    * **Recursos eficientes:** Não exige muitos recursos adicionais, pois as novas instâncias substituem as antigas.
* **Desvantagens:**
    * **Versões misturadas:** Por um período, ambas as versões (antiga e nova) podem estar em execução simultaneamente, o que pode causar problemas de compatibilidade se as versões forem muito diferentes.
    * **Detecção de erros:** Problemas na nova versão podem não ser detectados imediatamente, pois o deploy é gradual.

---
### 3. Blue/Green (Azul/Verde)

Nesta estratégia, **duas versões completas da infraestrutura** são mantidas em paralelo: a versão "**azul**" (atual em produção) e a versão "**verde**" (nova versão). Uma vez que a versão verde é totalmente implantada e testada, o tráfego é então comutado da versão azul para a verde.

* **Vantagens:**
    * **Zero Downtime:** A transição é instantânea, sem interrupção do serviço.
    * **Rollback instantâneo:** Se houver problemas na versão verde, basta direcionar o tráfego de volta para a versão azul.
    * **Testes completos:** A nova versão pode ser totalmente testada em um ambiente de produção antes de ser ativada.
* **Desvantagens:**
    * **Alto custo de recursos:** Exige o dobro dos recursos de infraestrutura para manter as duas versões ativas.
    * **Complexidade:** Requer mais orquestração para gerenciar a comutação de tráfego.

---
### 4. Canary (Canário)

A estratégia **Canary** envolve a implantação da nova versão para um **pequeno subconjunto de usuários ou servidores**. Se a nova versão se comportar conforme o esperado, o rollout é expandido gradualmente para mais usuários até que todas as instâncias sejam atualizadas.

* **Vantagens:**
    * **Detecção precoce de erros:** Problemas na nova versão são expostos a um grupo limitado de usuários, minimizando o impacto.
    * **Menor risco:** Reduz o risco de um deploy problemático afetar todos os usuários.
    * **Feedback real:** Permite coletar feedback em tempo real sobre a nova versão.
* **Desvantagens:**
    * **Complexidade:** Exige ferramentas e configurações adicionais para roteamento de tráfego e monitoramento.
    * **Demorado:** O processo de rollout é mais lento, pois é gradual.
    * **Gerenciamento de estados:** Pode ser complexo se a nova versão exigir alterações no esquema de banco de dados ou outros serviços dependentes.

---
### 5. A/B Testing (Teste A/B)

Similar ao Canary, mas com foco em testar diferentes versões de um recurso ou interface de usuário com base em critérios específicos (por exemplo, geolocalização, perfil do usuário). O tráfego é dividido entre as versões A e B para avaliar qual delas tem melhor desempenho em relação a métricas definidas.

* **Vantagens:**
    * **Otimização de recursos:** Permite testar e comparar o desempenho de diferentes versões.
    * **Tomada de decisão baseada em dados:** Ajuda a tomar decisões sobre qual versão implantar para todos os usuários com base em dados concretos.
* **Desvantagens:**
    * **Complexidade de implementação:** Requer um controle preciso sobre o roteamento de tráfego e coleta de métricas.
    * **Problemas de compatibilidade:** As diferentes versões podem exigir compatibilidade com os dados existentes.

A escolha da estratégia de deploy mais adequada dependerá das necessidades específicas de sua aplicação e do nível de risco que você está disposto a aceitar.