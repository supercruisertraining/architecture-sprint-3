@startuml Kafka-Based System - C4 Containers (Updated)
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

Person(клиент, "Клиент", "Пользователь системы")
Person(админ, "Администратор", "Управление системой")

System_Boundary(система, "Система умного дома") {
    Container(web_gateway, "Web API Gateway", "Spring Cloud Gateway", "Маршрутизация HTTP-запросов, аутентификация")
    Container(kafka_gateway, "Kafka API Gateway", "Kafka REST Proxy", "Приём данных датчиков в Kafka через HTTP")
    Container(kafka, "Kafka Cluster", "Apache Kafka", "Топики: raw_sensors, processed_data")
    Container(api_server, "API Server", "Java/Spring", "REST API для клиентов")
    Container(processor, "Data Processor", "Java/Spring", "Обработка данных из Kafka")
    ContainerDb(postgres, "PostgreSQL", "СУБД", "Хранение данных")
    Container(аутентификация, "Auth Service", "Keycloak", "Аутентификация клиентов")
}

Rel(клиент, web_gateway, "Управление умным домом", "HTTPS")
Rel(админ, web_gateway, "Администрирование системы", "HTTPS")
Rel(web_gateway, аутентификация, "Проверка токена", "gRPC")
Rel(web_gateway, api_server, "Внутренние запросы", "HTTP/2")

Rel(датчики, kafka_gateway, "Отправка показаний", "HTTP REST")
Rel(kafka_gateway, kafka, "Запись в топик raw_sensors", "Kafka Protocol")
Rel(processor, kafka, "Чтение из raw_sensors", "Kafka Consumer")
Rel(processor, kafka, "Запись в processed_data", "Kafka Producer")
Rel(processor, postgres, "Сохранение результатов", "JDBC")
Rel(api_server, postgres, "Чтение данных", "JPA")

@enduml