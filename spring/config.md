server.error.include-message=always
server.error.include-binding-errors=always
server.error.include-stacktrace=ON_PARAM

spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.show-sql=true

spring.h2.console.enabled=true
spring.h2.console.path=/h2
#spring.jpa.hibernate.ddl-auto = update

spring.main.banner-mode=off

management.endpoints.web.exposure.include=info

# Custom info properties
info.app.name=YourAppName
info.app.version=1.0.0
info.app.description=Your application description

spring.security.user.name=scott
spring.security.user.password=scott
