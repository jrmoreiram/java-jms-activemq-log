# 📬 Sistema de Mensageria com JMS e ActiveMQ - Log

## 📋 Sobre o Projeto

Este projeto é uma aplicação Java que demonstra o uso de **JMS (Java Message Service)** com **Apache ActiveMQ** para implementar um sistema de mensageria assíncrona voltado para processamento de logs. O projeto foi desenvolvido com base no curso **"JMS e ActiveMQ: mensageria com Java"** da **Alura**, explorando conceitos fundamentais de mensageria, filas, produtores e consumidores.

## 🎯 Objetivo

Implementar um sistema de mensageria para processamento de logs utilizando o padrão JMS, demonstrando:

- Produção e consumo de mensagens assíncronas
- Configuração de prioridades em mensagens
- Controle de tempo de vida (TTL - Time To Live) das mensagens
- Modos de entrega (persistente e não-persistente)
- Processamento assíncrono com MessageListener
- Integração com Apache ActiveMQ via JNDI

## 🛠️ Tecnologias Utilizadas

### Core
- **Java** - Linguagem de programação principal
- **JMS 1.1** - API de mensageria Java (Java Message Service)
- **JNDI** - Java Naming and Directory Interface para lookup de recursos

### Message Broker
- **Apache ActiveMQ 5.12.0** - Broker de mensagens open-source
  - Console de administração web
  - Protocolo TCP na porta padrão 61616
  - Suporte a filas (Queues) e tópicos (Topics)

### Dependências
- **activemq-all-5.12.0.jar** - Cliente ActiveMQ completo com todas as dependências

## 🏗️ Arquitetura do Projeto

### Estrutura de Diretórios

```
java-jms-activemq-log-main/
│
├── src/                                    # Código-fonte principal
│   ├── br/com/caelum/jms/log/
│   │   ├── TesteProdutorFila.java         # Produtor de mensagens
│   │   └── TesteConsumidorFila.java       # Consumidor de mensagens
│   └── jndi.properties                     # Configuração JNDI
│
├── jms1.1/                                 # Especificação JMS 1.1
│   ├── doc/api/                            # Documentação JavaDoc
│   └── src/share/javax/jms/                # Classes da API JMS
│
├── bin/                                    # Classes compiladas
└── README.md                               # Documentação do projeto
```

## 🔑 Recursos e Funcionalidades Implementadas

### 1. **Produtor de Mensagens (Producer)**

#### Características Principais
- **Classe:** `TesteProdutorFila.java`
- **Fila Destino:** `fila.log`
- **Tipo de Mensagem:** TextMessage

#### Configurações Avançadas
```java
producer.send(message, DeliveryMode.NON_PERSISTENT, 9, 80000);
```

**Parâmetros:**
- **DeliveryMode.NON_PERSISTENT** - Mensagem não persistida em disco (maior performance)
- **Prioridade: 9** - Alta prioridade (escala de 0 a 9)
- **TTL: 80000ms** - Tempo de vida de 80 segundos

#### Funcionalidades
- ✅ Envio de mensagens de log para fila
- ✅ Configuração de prioridade de mensagens
- ✅ Definição de tempo de expiração (TTL)
- ✅ Modo de entrega configurável (persistente/não-persistente)

### 2. **Consumidor de Mensagens (Consumer)**

#### Características Principais
- **Classe:** `TesteConsumidorFila.java`
- **Padrão:** MessageListener (event-driven)
- **Modo de Reconhecimento:** AUTO_ACKNOWLEDGE

#### Processamento Assíncrono
```java
consumer.setMessageListener(new MessageListener() {
    @Override
    public void onMessage(Message message) {
        TextMessage textMessage = (TextMessage) message;
        System.out.println(textMessage.getText());
    }
});
```

**Benefícios:**
- ✅ Processamento não-bloqueante
- ✅ Callback automático ao receber mensagens
- ✅ Aplicação permanece escutando continuamente
- ✅ Reconhecimento automático de mensagens

### 3. **Configuração JNDI**

#### Arquivo: `jndi.properties`
```properties
# Factory do ActiveMQ
java.naming.factory.initial = org.apache.activemq.jndi.ActiveMQInitialContextFactory

# URL do broker ActiveMQ
java.naming.provider.url = tcp://192.168.0.208:61616

# Mapeamento de filas JNDI
queue.LOG = fila.log
```

**Funcionalidades:**
- Configuração centralizada de conexão
- Lookup de recursos via JNDI
- Mapeamento de nomes lógicos para filas físicas
- Fácil mudança de ambiente (dev/prod)

### 4. **Padrões JMS Implementados**

#### Point-to-Point (Queue)
- **Fila:** `fila.log`
- **Modelo:** Um produtor → Uma fila → Um ou mais consumidores
- **Garantia:** Cada mensagem é consumida por apenas um consumidor

#### Ciclo de Vida da Conexão
```java
InitialContext context = new InitialContext();
ConnectionFactory factory = (ConnectionFactory) context.lookup("ConnectionFactory");
Connection connection = factory.createConnection();
connection.start();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
```

**Boas Práticas:**
- ✅ Inicialização de contexto JNDI
- ✅ Criação de conexão via Factory
- ✅ Gerenciamento explícito de sessão
- ✅ Fechamento adequado de recursos

## 💡 Conceitos de JMS Demonstrados

### ✅ Componentes JMS
- [x] **ConnectionFactory** - Fábrica de conexões
- [x] **Connection** - Conexão com o broker
- [x] **Session** - Sessão de trabalho
- [x] **Destination** - Fila de destino
- [x] **MessageProducer** - Produtor de mensagens
- [x] **MessageConsumer** - Consumidor de mensagens
- [x] **MessageListener** - Listener assíncrono

### ✅ Tipos de Mensagens
- [x] **TextMessage** - Mensagens de texto (usado para logs)

### ✅ Configurações de Mensagem
- [x] **DeliveryMode** - PERSISTENT vs NON_PERSISTENT
- [x] **Priority** - Prioridade de 0 a 9
- [x] **Time To Live (TTL)** - Tempo de expiração

### ✅ Modos de Reconhecimento
- [x] **AUTO_ACKNOWLEDGE** - Reconhecimento automático
- [ ] **CLIENT_ACKNOWLEDGE** - Reconhecimento manual (comentado no código)

### ✅ Processamento
- [x] **Síncrono** - `consumer.receive()`
- [x] **Assíncrono** - `setMessageListener()`

## 🚀 Como Executar o Projeto

### Pré-requisitos

1. **JDK 8 ou superior** instalado
2. **Apache ActiveMQ 5.12.0+** instalado e rodando
3. **activemq-all-5.12.0.jar** no classpath do projeto

### Instalação e Configuração do ActiveMQ

#### 1. Baixar e Instalar ActiveMQ
```bash
# Download do ActiveMQ
wget https://archive.apache.org/dist/activemq/5.12.0/apache-activemq-5.12.0-bin.tar.gz

# Extrair
tar -xzf apache-activemq-5.12.0-bin.tar.gz
cd apache-activemq-5.12.0
```

#### 2. Iniciar o ActiveMQ
```bash
# Linux/Mac
./bin/activemq start

# Windows
bin\activemq.bat start
```

#### 3. Acessar Console de Administração
- **URL:** http://localhost:8161/admin
- **Usuário padrão:** admin
- **Senha padrão:** admin

#### 4. Criar a Fila
No console de administração:
1. Navegue até "Queues"
2. Crie uma nova fila com o nome: **fila.log**

### Configuração do Projeto

#### 1. Ajustar IP do ActiveMQ
Edite o arquivo `src/jndi.properties`:
```properties
# Altere para o IP do seu servidor ActiveMQ
java.naming.provider.url = tcp://localhost:61616
```

#### 2. Adicionar JAR ao Classpath
- Baixe `activemq-all-5.12.0.jar`
- Adicione ao classpath do projeto (no Eclipse: Build Path → Add External JARs)

### Execução

#### 1. Compilar o Projeto
```bash
javac -cp ".:activemq-all-5.12.0.jar" src/br/com/caelum/jms/log/*.java
```

#### 2. Executar o Consumidor (em um terminal)
```bash
java -cp ".:src:activemq-all-5.12.0.jar" br.com.caelum.jms.log.TesteConsumidorFila
```

#### 3. Executar o Produtor (em outro terminal)
```bash
java -cp ".:src:activemq-all-5.12.0.jar" br.com.caelum.jms.log.TesteProdutorFila
```

## 📊 Fluxo de Mensageria

### Arquitetura de Componentes

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│   Produtor      │  send   │  Apache ActiveMQ │ receive │   Consumidor    │
│  TesteProdut... │ ──────> │   (Broker)       │ ──────> │  TesteCons...   │
│                 │         │  Queue: fila.log │         │                 │
└─────────────────┘         └──────────────────┘         └─────────────────┘
      ⬆                              │                           ⬇
      │                              │                           │
   Envia logs                 Armazena/Roteia            Processa logs
   com prioridade            mensagens na fila          assincronamente
```

### Fluxo de Execução

1. **Produtor envia mensagem**
   ```
   Message: "WARN | Apache ActiveMQ 5.12.0 is starting"
   Priority: 9 (alta)
   TTL: 80000ms
   Mode: NON_PERSISTENT
   ```

2. **ActiveMQ recebe e armazena**
   - Mensagem entra na fila `fila.log`
   - Broker gerencia prioridade e TTL
   - Visível no console de administração

3. **Consumidor processa**
   - MessageListener dispara callback
   - Mensagem é exibida no console
   - Reconhecimento automático (AUTO_ACKNOWLEDGE)

## 🔧 Detalhes Técnicos

### Modos de Entrega (Delivery Mode)

| Modo | Descrição | Performance | Confiabilidade |
|------|-----------|-------------|----------------|
| **NON_PERSISTENT** | Mensagem em memória | Alta ⚡ | Baixa (perde se broker cair) |
| **PERSISTENT** | Mensagem gravada em disco | Média | Alta (sobrevive a falhas) |

**Uso no projeto:** `NON_PERSISTENT` - ideal para logs que não são críticos

### Prioridade de Mensagens

- **Escala:** 0 (baixa) a 9 (alta)
- **Padrão:** 4 (média)
- **No projeto:** Prioridade 9 (alta) para mensagens de log importantes

### Time To Live (TTL)

- **Configurado:** 80.000ms (80 segundos)
- **Comportamento:** Mensagem expira se não for consumida dentro do prazo
- **Uso:** Evita acúmulo de logs antigos na fila

### Modo de Reconhecimento (Acknowledge Mode)

```java
session.createSession(false, Session.AUTO_ACKNOWLEDGE);
```

- **AUTO_ACKNOWLEDGE** - JMS reconhece automaticamente após `onMessage()`
- **Transacional:** `false` - sem controle transacional

## 📝 Exemplos de Uso

### Envio de Mensagem com Configurações Customizadas

```java
// Criar mensagem de log
Message message = session.createTextMessage(
    "WARN | Apache ActiveMQ 5.12.0 is starting"
);

// Enviar com configurações específicas
producer.send(
    message,
    DeliveryMode.NON_PERSISTENT,  // Não persistir
    9,                             // Prioridade máxima
    80000                          // Expira em 80 segundos
);
```

### Consumo Assíncrono com Listener

```java
// Configurar listener para processar mensagens
consumer.setMessageListener(new MessageListener() {
    @Override
    public void onMessage(Message message) {
        TextMessage textMessage = (TextMessage) message;
        try {
            // Processar log recebido
            System.out.println(textMessage.getText());
            
            // Reconhecimento manual (opcional)
            // message.acknowledge();
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }
});

// Manter aplicação rodando
new Scanner(System.in).nextLine();
```

## 🎓 Casos de Uso

### Sistema de Log Distribuído

Este projeto demonstra um padrão comum em arquiteturas distribuídas:

#### Cenário 1: Múltiplas Aplicações Gerando Logs
```
[App 1] ──┐
[App 2] ──┼──> [fila.log] ──> [Processador de Logs]
[App 3] ──┘
```

#### Cenário 2: Priorização de Logs
- **Prioridade 9:** Logs CRITICAL/ERROR
- **Prioridade 5:** Logs WARN
- **Prioridade 1:** Logs INFO/DEBUG

#### Cenário 3: Expiração de Logs
- Logs antigos expiram automaticamente
- Evita sobrecarga do sistema
- Foco em eventos recentes

## 🔍 Vantagens da Arquitetura de Mensageria

### 1. **Desacoplamento**
- Produtor e consumidor não se conhecem
- Aplicações podem ser desenvolvidas independentemente
- Facilita manutenção e evolução

### 2. **Escalabilidade**
- Múltiplos produtores podem enviar para a mesma fila
- Múltiplos consumidores podem processar em paralelo
- Load balancing automático entre consumidores

### 3. **Confiabilidade**
- Mensagens não se perdem se consumidor estiver offline
- Reprocessamento em caso de falha
- Garantia de entrega (com modo PERSISTENT)

### 4. **Assincronicidade**
- Produtor não bloqueia esperando processamento
- Melhor throughput
- Resposta rápida ao usuário

### 5. **Flexibilidade**
- Diferentes prioridades para mensagens urgentes
- TTL para mensagens temporárias
- Múltiplos consumidores com lógicas diferentes

## ⚙️ Configuração Detalhada

### Arquivo jndi.properties

```properties
# Factory de contexto inicial do ActiveMQ
java.naming.factory.initial = org.apache.activemq.jndi.ActiveMQInitialContextFactory

# URL de conexão com o broker ActiveMQ (protocolo TCP)
java.naming.provider.url = tcp://192.168.0.208:61616

# Mapeamento JNDI da fila
# Sintaxe: queue.[nomeLógico] = [nomefísicoNoActiveMQ]
queue.LOG = fila.log
```

**Configurações Importantes:**
- `tcp://` - Protocolo de comunicação
- `61616` - Porta padrão do ActiveMQ
- `queue.LOG` - Nome JNDI para lookup no código
- `fila.log` - Nome real da fila no broker

## 🔐 Boas Práticas Implementadas

### 1. Gerenciamento de Recursos
```java
try {
    // Uso dos recursos JMS
} finally {
    session.close();
    connection.close();
    context.close();
}
```

### 2. Tratamento de Exceções
- Try-catch para JMSException
- Logs de erro adequados
- Rollback em caso de falha (quando transacional)

### 3. Configuração Externalizada
- Propriedades JNDI em arquivo separado
- Fácil alteração sem recompilar
- Diferentes configurações por ambiente

## 📈 Monitoramento e Administração

### Console Web do ActiveMQ

**URL de Acesso:** http://localhost:8161/admin

**Funcionalidades:**
- 📊 Visualizar filas e tópicos
- 📨 Ver mensagens pendentes
- 👥 Monitorar consumidores conectados
- 📉 Estatísticas de throughput
- 🗑️ Purgar filas
- 📝 Enviar mensagens de teste

### Métricas Monitoradas
- **Number of Pending Messages** - Mensagens aguardando consumo
- **Number of Consumers** - Consumidores ativos
- **Messages Enqueued** - Total de mensagens recebidas
- **Messages Dequeued** - Total de mensagens consumidas

## 🧪 Cenários de Teste

### Teste 1: Envio e Consumo Básico
1. Iniciar ActiveMQ
2. Executar `TesteConsumidorFila` (fica aguardando)
3. Executar `TesteProdutorFila` (envia mensagem)
4. Verificar log no console do consumidor

### Teste 2: Mensagens com Expiração
1. Configurar TTL baixo (5000ms)
2. Enviar mensagem sem consumidor ativo
3. Aguardar mais de 5 segundos
4. Iniciar consumidor
5. Verificar que mensagem expirou (não aparece)

### Teste 3: Priorização
1. Enviar múltiplas mensagens com prioridades diferentes
2. Verificar ordem de consumo
3. Mensagens de maior prioridade são consumidas primeiro

### Teste 4: Múltiplos Consumidores
1. Iniciar 2 ou mais consumidores
2. Enviar várias mensagens
3. Observar distribuição round-robin entre consumidores

## 🔄 Fluxo Completo de Mensageria

```
┌──────────────────────────────────────────────────────────────┐
│                      PRODUTOR                                │
│  1. InitialContext → lookup ConnectionFactory                │
│  2. Criar Connection e iniciar                               │
│  3. Criar Session (não transacional, auto-ack)               │
│  4. Lookup da fila via JNDI                                  │
│  5. Criar MessageProducer                                    │
│  6. Criar TextMessage                                        │
│  7. Enviar com send(msg, deliveryMode, priority, ttl)        │
│  8. Fechar recursos                                          │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│                    APACHE ACTIVEMQ                           │
│  • Recebe mensagem na fila.log                               │
│  • Aplica prioridade                                         │
│  • Monitora TTL                                              │
│  • Armazena em memória (NON_PERSISTENT)                      │
│  • Disponibiliza para consumidores                           │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│                      CONSUMIDOR                              │
│  1. InitialContext → lookup ConnectionFactory                │
│  2. Criar Connection e iniciar                               │
│  3. Criar Session (não transacional, auto-ack)               │
│  4. Lookup da fila via JNDI                                  │
│  5. Criar MessageConsumer                                    │
│  6. Registrar MessageListener                                │
│  7. onMessage() disparado automaticamente                    │
│  8. Processar mensagem (exibir log)                          │
│  9. Reconhecimento automático                                │
└──────────────────────────────────────────────────────────────┘
```

## 📚 Referências e Documentação

### Curso Base
**"JMS e ActiveMQ: mensageria com Java"** - Alura

### Tópicos Abordados:
- ✅ Introdução ao JMS (Java Message Service)
- ✅ Instalação e configuração do Apache ActiveMQ
- ✅ Produtores e consumidores de mensagens
- ✅ Filas (Queues) vs Tópicos (Topics)
- ✅ Modos de entrega (PERSISTENT/NON_PERSISTENT)
- ✅ Prioridade de mensagens
- ✅ Time To Live (TTL)
- ✅ MessageListener para processamento assíncrono
- ✅ Configuração JNDI

### Especificação JMS
- **Versão:** JMS 1.1
- **Documentação:** Incluída em `jms1.1/doc/api/`
- **Pacote:** `javax.jms.*`

## 🌟 Possíveis Extensões

### Melhorias Sugeridas
- [ ] Implementar filtro de mensagens (Message Selector)
- [ ] Adicionar suporte a tópicos (Pub/Sub)
- [ ] Implementar processamento transacional
- [ ] Adicionar DLQ (Dead Letter Queue) para mensagens com erro
- [ ] Criar diferentes níveis de log (INFO, WARN, ERROR, CRITICAL)
- [ ] Implementar retry policy com redelivery
- [ ] Adicionar métricas e monitoramento
- [ ] Integrar com frameworks de log (Log4j, SLF4J)

## 🐛 Troubleshooting

### Problema: Conexão Recusada
```
javax.naming.NamingException: Connection refused
```
**Solução:** Verificar se ActiveMQ está rodando e se o IP/porta estão corretos

### Problema: Fila não Encontrada
```
javax.naming.NameNotFoundException: fila.log
```
**Solução:** Criar a fila no console de administração do ActiveMQ

### Problema: ClassNotFoundException
```
java.lang.ClassNotFoundException: org.apache.activemq.jndi.ActiveMQInitialContextFactory
```
**Solução:** Adicionar `activemq-all-5.12.0.jar` ao classpath

### Problema: Mensagens Expiradas
**Solução:** Aumentar TTL ou iniciar consumidor antes de enviar mensagens

## 📄 Licença

Este é um projeto educacional desenvolvido para fins de aprendizado.

## 👨‍💻 Autor

Projeto desenvolvido durante o curso de JMS e ActiveMQ da Alura.

## 🔗 Links Úteis

- [Apache ActiveMQ Documentation](https://activemq.apache.org/documentation)
- [JMS 1.1 Specification](https://jcp.org/en/jsr/detail?id=914)
- [ActiveMQ Getting Started](https://activemq.apache.org/getting-started)

---

**Nota:** Este README técnico foi criado para servir como documentação completa do projeto, detalhando arquitetura, configuração, conceitos de mensageria e guia prático de uso.
