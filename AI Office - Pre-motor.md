# AI Office · Captação de Iniciativas de IA — Pré-motor

Respostas do formulário "AI Office - Captação de Iniciativas de IA" para a iniciativa do **pré-motor** da
Plataforma de Autenticação Multicanal do Banco Carrefour.

---

## Descreva o problema que deseja resolver (ou oportunidade de gerar valor)

O Banco Carrefour está construindo a Plataforma de Autenticação Multicanal, que permite confirmar a
identidade do cliente em qualquer canal (loja, WhatsApp, site, totem). A Plataforma tem duas formas de
levar o cliente a essa confirmação: a jornada web, aberta dentro do próprio canal, e o aplicativo do
banco, acionado por notificação no celular. Nenhuma das duas é a melhor para todos os clientes: há quem
não tenha o aplicativo, há aparelho sem câmera e há quem ainda não seja cliente. A oportunidade é um
componente, o pré-motor, que decide para cada cliente qual caminho tem mais chance de dar certo. Ele
começa registrando o resultado de cada autenticação, passa a direcionar por regras e, por fim, aprende
com o histórico para escolher o caminho com maior chance de conclusão.

## Qual a consequência gerada pelo problema? (se possível, informar volumetria)

Quando o cliente é levado pelo caminho errado, ele pode desistir no meio da autenticação. A operação não
é concluída (contratação de cartão, aumento de limite, desbloqueio, atualização cadastral) e o
atendimento é perdido ou precisa recomeçar. Sem um critério de escolha, o caminho seria fixo e igual para
todos, sem aproveitar o aplicativo para quem já o usa nem evitar o aplicativo para quem não o tem.

**Volumetria:** ainda não há medição, porque a Plataforma está em construção. A primeira fase do
pré-motor existe justamente para medir taxa de conclusão, abandono e tempo de autenticação por canal e
por caminho, e assim estabelecer a linha de base.

## Descreva brevemente como o processo funciona hoje (AS-IS)

Hoje a confirmação de identidade do cliente funciona dentro do aplicativo do banco, pelo SDK de
autenticação do app, com o motor de risco (políticas do Serasa) decidindo qual desafio aplicar. Fora do
aplicativo não existe um equivalente: cada canal teria que construir a sua própria autenticação. A
Plataforma de Autenticação Multicanal está sendo construída para resolver isso, e a primeira entrega é o
Módulo Android, no atendimento em loja. Não existe hoje nenhum componente que escolha por onde o cliente
deve se autenticar.

**Envolvidos:** time de desenvolvimento da Plataforma (orquestrador / API de back-end), time do motor de
risco, time do aplicativo e times dos canais.

## Se aplicável, descreva a solução que considera factível

O pré-motor é um módulo dentro do orquestrador, a API de back-end da Plataforma. A cada pedido de
autenticação, o orquestrador pergunta ao pré-motor por onde levar o cliente, e ele responde com o caminho
(jornada web ou aplicativo), uma alternativa caso o primeiro não funcione e o motivo da escolha. O motor
de risco continua decidindo qual desafio aplicar; o pré-motor decide só o caminho. A solução cresce em
três fases, sob a mesma interface:

1. **Coleta.** Todo cliente segue pela jornada web, o caminho principal. Cada autenticação gera um
   registro de decisão e de desfecho (canal, tipo de operação, caminho usado, se concluiu, onde parou e
   quanto tempo levou), sem nenhum dado pessoal novo. É a base de dados para as fases seguintes.
2. **Direciona.** Com os dois caminhos funcionando, o pré-motor passa a escolher por regras de negócio
   explícitas e versionadas (por exemplo: o cliente tem o aplicativo ativo? o canal consegue exibir a
   jornada? é uma contratação de quem ainda não é cliente?), ajustáveis sem nova versão do sistema.
3. **Aprende.** Com volume real acumulado, um modelo treinado no histórico de decisões e desfechos
   escolhe, para cada perfil de cliente e de operação, o caminho com maior chance de conclusão. As regras
   da fase 2 ficam como rede de segurança.

**Garantias em qualquer fase:**

- O pré-motor nunca trava a autenticação: se ele falhar ou demorar, o cliente segue pelo caminho padrão,
  que é configuração.
- O canal não precisa decidir nada nem mudar a integração. A escolha acontece dentro da Plataforma.
- Dados de perfil só entram na fase 3, com base legal e finalidade definidas (LGPD).

**Como medir o resultado:** taxa de conclusão, abandono por etapa e tempo de autenticação, por canal e por
caminho, comparados com a linha de base da fase 1.

---

Material de apoio no mesmo repositório: `Pre-motor.pdf` (documento) e `Pre-motor.mp4` (vídeo).
