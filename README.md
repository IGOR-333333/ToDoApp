# App README

- [ ] TODO Replace or update this README with instructions relevant to your application

## Project Structure

The sources of your App have the following structure:

```
src
├── main/frontend
│   └── themes
│       └── default
│           ├── styles.css
│           └── theme.json
├── main/java
│   └── [application package]
│       ├── base
│       │   └── ui
│       │       ├── component
│       │       │   └── ViewToolbar.java
│       │       └── MainLayout.java
│       ├── examplefeature
│       │   ├── ui
│       │   │   └── TaskListView.java
│       │   ├── Task.java
│       │   ├── TaskRepository.java
│       │   └── TaskService.java                
│       └── Application.java       
└── test/java
    └── [application package]
        └── examplefeature
           └── TaskServiceTest.java                 
```

The main entry point into the application is `Application.java`. This class contains the `main()` method that start up 
the Spring Boot application.

The skeleton follows a *feature-based package structure*, organizing code by *functional units* rather than traditional 
architectural layers. It includes two feature packages: `base` and `examplefeature`.

* The `base` package contains classes meant for reuse across different features, either through composition or 
  inheritance. You can use them as-is, tweak them to your needs, or remove them.
* The `examplefeature` package is an example feature package that demonstrates the structure. It represents a 
  *self-contained unit of functionality*, including UI components, business logic, data access, and an integration test.
  Once you create your own features, *you'll remove this package*.

The `src/main/frontend` directory contains an empty theme called `default`, based on the Lumo theme. It is activated in
the `Application` class, using the `@Theme` annotation.

## Starting in Development Mode

To start the application in development mode, import it into your IDE and run the `Application` class. 
You can also start the application from the command line by running: 

```bash
./mvnw
```

## Building for Production

To build the application in production mode, run:

```bash
./mvnw -Pproduction package
```

To build a Docker image, run:

```bash
docker build -t my-application:latest .
```

If you use commercial components, pass the license key as a build secret:

```bash
docker build --secret id=proKey,src=$HOME/.vaadin/proKey .
```

## Getting Started

The [Getting Started](https://vaadin.com/docs/latest/getting-started) guide will quickly familiarize you with your new
App implementation. You'll learn how to set up your development environment, understand the project 
structure, and find resources to help you add muscles to your skeleton — transforming it into a fully-featured 
application.





## Pipeline de CI/CD

Este projeto possui uma pipeline de integração contínua (CI) e entrega contínua (CD) configurada com **GitHub Actions**.

### Funcionalidade da pipeline

- Dispara automaticamente sempre que há um **push na branch `main`**.
- Configura o **Java 21** no runner.
- Executa o comando `mvn clean package` para **compilar o projeto e gerar o arquivo `.jar`**.
- Publica o `.jar` como **artefacto do workflow**, disponível para download na aba Actions.
- Opcionalmente, copia o `.jar` para a raiz do runner durante a execução do workflow.

### Excerto do `build.yml`

```yaml
name: Pipeline de CI/CD

on:
  push:
    branches:
      - main   # Gatilho: push na branch principal

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # 1️⃣ Fazer checkout do repositório
      - name: Checkout repository
        uses: actions/checkout@v3
        with:
          persist-credentials: true  # permite que o workflow use o GITHUB_TOKEN para push

      # 2️⃣ Configurar Java 21
      - name: Set up Java
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '21'

      # 3️⃣ Executar build Maven (gera o .jar)
      - name: Build with Maven
        run: mvn clean package

      # 4️⃣ Publicar o .jar como artefacto do workflow
      - name: Upload JAR artifact
        uses: actions/upload-artifact@v4
        with:
          name: my-app-jar
          path: target/*.jar

