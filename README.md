# Задача №2 - Report Portal

## Краткое описание

1. Скачать [docker-compose.yml](https://github.com/reportportal/reportportal/blob/master/docker-compose.yml)

2. В зависимости от хоста (Windows/Linux) для контейнеров, необходимо изменить параметр ```volumes``` для postgres контейнера (строки под заголовками "For windows host"/"For unix host"). Вариант для Windows:
    ```
        volumes:
          # For windows host
          - postgres:/var/lib/postgresql/data
          # For unix host
          # - ./data/postgres:/var/lib/postgresql/data
            ...
            ...
        # Docker volume for Windows host
        volumes:
          postgres:
    ```
5. Запустить контейнеры Reportportal:
    ```
     docker-compose -p reportportal up -d --force-recreate
    ```
6. Открыть страницу Reportportal: [http://localhost:8080](http://localhost:8080/) 
    ```
	 login: superadmin 
	 password: erebus
   ```
   - Добавить проект [Add New Project](http://localhost:8080/ui/#administrate/projects)
   - Добавить пользователя [Add User](http://localhost:8080/ui/#administrate/users)

7. В проекте IDEA:
    - Создать папку ```src/test/resources```
    - Поместить в нее файл ```reportportal.properties```
    - Выполнить вход с учетными данными созданного пользователя [http://localhost:8080](http://localhost:8080/)
    - Перейти в профиль [USER PROFILE](http://localhost:8080/ui/#user-profile)
    - В разделе CONFIGURATION EXAMPLES скопировать параметры (пример):

	```
	 rp.endpoint = http://localhost:8080
     rp.uuid = 66e0d156-69cd-48db-8a8d-d5c95b160edd
     rp.launch = pava14_TEST_EXAMPLE
     rp.project = pava14_personal
	```
	
    - Добавить, скопированные параметры в файл ```reportportal.properties```
    - build.gradle:
    ```
	 repositories {
		mavenCentral()
		jcenter()
		mavenLocal()
		maven { url "http://dl.bintray.com/epam/reportportal" }
	 }

	 dependencies {
		testCompile 'com.epam.reportportal:agent-java-junit5:5.0.0-RC-1'
	 }

	 test {
		testLogging.showStandardStreams = true
		useJUnitPlatform()
		systemProperty 'junit.jupiter.extensions.autodetection.enabled', true
	 }
    ```   

	- В папку ```src/test/resources/META-INF/services```, поместить файл ```org.junit.jupiter.api.extension.Extension``` 
	с содержимым: ```com.epam.reportportal.junit5.ReportPortalExtension```
	
	- В папку ```src/test/resources``` поместить файл ```junit-platform.properties``` с содержимым: ```junit.jupiter.extensions.autodetection.enabled=true```
	
	- Для использования:
	```
	 import com.epam.reportportal.junit5.ReportPortalExtension;
	 import org.junit.jupiter.api.extension.ExtendWith;

	 @ExtendWith(ReportPortalExtension.class)
	 public class MyClassTest {
	 ...
	 ...
	 }

	``` 
	 
8. После запуска тестов, проверить результат передачи логов в разделе [LAUNCHES](http://localhost:8080/ui/#pava14_personal/launches/latest)
