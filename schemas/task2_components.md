@startuml Data Processor Components
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

Container(processor, "Data Processor", "Java/Spring")

System_Boundary(processor_components, "Компоненты") {
    Component(consumer, "Kafka Consumer", "Spring Kafka", "Чтение из raw_sensors")
    Component(validator, "Data Validator", "Java", "Проверка корректности данных")
    Component(transformer, "Data Transformer", "Java", "Конвертация в бизнес-модель")
    Component(calculator, "Metrics Calculator", "Java", "Агрегация показаний")
    Component(repository, "DB Repository", "Spring Data JPA", "Сохранение в PostgreSQL")
}

Rel(consumer, validator, "Сырые данные", "DTO")
Rel(validator, transformer, "Валидные данные", "Domain Object")
Rel(transformer, calculator, "Нормализованные данные", "Business Model")
Rel(calculator, repository, "Сохранение в БД", "JDBC")

@enduml