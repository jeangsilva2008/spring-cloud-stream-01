Spring cloud stream poc

./gradlew build	Compila e empacota o projeto
./gradlew bootRun	Roda a aplicação Spring Boot
./gradlew test	Executa os testes
./gradlew clean	Limpa o diretório build/
./gradlew dependencies	Mostra as dependências resolvidas
./gradlew tasks	Lista todas as tarefas disponíveis

./gradlew tasks	Lista as tarefas principais do projeto
./gradlew tasks --all	Mostra todas as tarefas (inclusive internas)
./gradlew help --task <nome>	Explica uma tarefa específica
./gradlew <tarefa>	Executa a tarefa desejada

./gradlew bootRun        # executa o app Spring Boot
./gradlew build          # compila e empacota
./gradlew test           # roda os testes
./gradlew clean build    # limpa e recompila do zero

- Docker run Kafka em modo KRaft (sem Zookeeper)

docker run -d --name kafka \
  -p 9092:9092 \
  -e KAFKA_ENABLE_KRAFT=yes \
  -e KAFKA_CFG_PROCESS_ROLES=broker,controller \
  -e KAFKA_CFG_NODE_ID=1 \
  -e KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@localhost:9093 \
  -e KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093 \
  -e KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT \
  -e KAFKA_CFG_INTER_BROKER_LISTENER_NAME=PLAINTEXT \
  -e KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER \
  -e ALLOW_PLAINTEXT_LISTENER=yes \
  bitnami/kafka:3.6


Variável	Equivalente no server.properties	Função
KAFKA_ENABLE_KRAFT=yes	–	Habilita modo KRaft (sem Zookeeper)
KAFKA_CFG_PROCESS_ROLES=broker,controller	process.roles	Este nó é broker e controller
KAFKA_CFG_NODE_ID=1	node.id	ID do broker
KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@localhost:9093	controller.quorum.voters	Define o quorum para o controller
KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093	listeners	Portas de comunicação
KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT	listener.security.protocol.map	Protocolos dos listeners
KAFKA_CFG_INTER_BROKER_LISTENER_NAME=PLAINTEXT	inter.broker.listener.name	Comunicação entre brokers
KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER	controller.listener.names	Listener do controller
ALLOW_PLAINTEXT_LISTENER=yes	–	Permite plaintext, útil para dev/local


- cria um topico:
docker exec -it kafka kafka-topics.sh --create \
  --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1

- lista os topicos
docker exec -it kafka kafka-topics.sh --list --bootstrap-server localhost:9092


Serviço	Porta	Função
kafka	9092	Broker Kafka em modo KRaft (sem Zookeeper)
kafka	9093	Controller listener interno (KRaft)
kafdrop	9000	UI web para visualizar tópicos, mensagens e consumers

- Testar o projeto:

curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -d '{"id":"101","product":"Camiseta","quantity":2}'






