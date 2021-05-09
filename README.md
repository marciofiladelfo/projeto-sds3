# ![DevSuperior logo](https://raw.githubusercontent.com/devsuperior/bds-assets/main/ds/devsuperior-logo-small.png) Semana Spring React

## Objetivos do projeto para esta parte

- Criar projetos backend e frontend
- Salvar os projeto no Github em monorepo
- Montar o visual estático do front end
- Publicar o front end no Netlify

## Checklist

- Criar pastas do projeto

![DevSuperior no Instagram](https://raw.githubusercontent.com/devsuperior/bds-assets/main/ds/pastas-sds3.png)

- Conferir Yarn

```bash
yarn -v
npm install --global yarn
```

### Passo 1: criar projetos

- Criar projeto ReactJS com `create-react-app`:

```bash
npx create-react-app frontend --template typescript
```

- Criar projeto Spring Boot no `Spring Initializr` com as seguintes dependências:
  - Web
  - JPA
  - H2
  - Postgres
  - Security
- Se tiver com erro no pom.xml, tentar:
  - Botão direito no projeto -> Maven -> Update project (force update)
  - Menu Project -> Clean
  - Apagar pasta .m2 e deixar o STS refazer o download
- **COMMIT: Project created**

- Salvar o projeto no seu Github

### Passo 2: "limpar" o projeto ReactJS

- Limpar projeto ReactJS / tsconfig.json
- Arquivo \_redirects

`/* /index.html 200`

- **COMMIT: Project clean**

### Passo 3: adicionar Bootstrap e CSS ao projeto

- Bootstrap

```
yarn add bootstrap
(index.tsx) import 'bootstrap/dist/css/bootstrap.css';
```

- Assets e CSS

```css
@import url("https://fonts.googleapis.com/css2?family=Ubuntu:wght@300;400;500;700&display=swap");
:root {
  --color-primary: #ff8400;
}
html,
body {
  height: 100%;
  font-family: "Ubuntu", sans-serif;
}
root {
  display: flex;
  flex-direction: column;
  height: 100%;
}
.content {
  flex: 1 0 auto;
}
.footer {
  flex-shrink: 0;
  text-align: center;
}
.bg-primary {
  background-color: var(--color-primary) !important;
}
.text-primary {
  color: var(--color-primary) !important;
}
```

```
(index.tsx) import 'assets/css/styles.css';
```

- **COMMIT: Bootstrap**

### Passo 4: adicionar componentes estáticos básicos

- Navbar

```html
<div
  className="d-flex flex-column flex-md-row align-items-center p-3 px-md-4 mb-3 bg-light border-bottom shadow-sm"
>
  <div className="container">
    <nav className="my-2 my-md-0 mr-md-3">
      <img src="{ImgDsDark}" alt="DevSuperior" width="120" />
    </nav>
  </div>
</div>
```

- Footer

```html
<footer className="footer mt-auto py-3 bg-dark">
  <div className="container">
    <p className="text-light">
      App desenvolvido por
      <a href="https://github.com/acenelio" target="_blank" rel="noreferrer"
        >Nelio Alves</a
      >
    </p>
    <p className="text-light">
      <small
        ><strong>Semana Spring React</strong><br />
        Evento promovido pela escola DevSuperior:
        <a
          href="https://instagram.com/devsuperior.ig"
          target="_blank"
          rel="noreferrer"
          >@devsuperior.ig</a
        ></small
      >
    </p>
  </div>
</footer>
```

- DataTable

```html
<div className="table-responsive">
  <table className="table table-striped table-sm">
    <thead>
      <tr>
        <th>Data</th>
        <th>Vendedor</th>
        <th>Clientes visitados</th>
        <th>Negócios fechados</th>
        <th>Valor</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>22/04/2021</td>
        <td>Barry Allen</td>
        <td>34</td>
        <td>25</td>
        <td>15017.00</td>
      </tr>
    </tbody>
  </table>
</div>
```

- **COMMIT: Basic static components**

### Passo 5: adicionar gráficos estáticos

- Apex Charts

```bash
yarn add apexcharts
yarn add react-apexcharts
```

- BarChart

```javascript
const options = {
  plotOptions: {
    bar: {
      horizontal: true,
    },
  },
};

const mockData = {
  labels: {
    categories: ["Anakin", "Barry Allen", "Kal-El", "Logan", "Padmé"],
  },
  series: [
    {
      name: "% Sucesso",
      data: [43.6, 67.1, 67.7, 45.6, 71.1],
    },
  ],
};
```

- DonutChart

```javascript
const mockData = {
  series: [477138, 499928, 444867, 220426, 473088],
  labels: ["Anakin", "Barry Allen", "Kal-El", "Logan", "Padmé"],
};

const options = {
  legend: {
    show: true,
  },
};
```

- **COMMIT: Static charts**

### Passo 6: implantação no Netlify

- Publicação no Netlify
  - Comando: yarn build
  - Diretório: build
  - Deploy! (vai quebrar)
  - Site settings -> Build & Deploy: (colocar o nome da sua subpasta do projeto frontend)
  - Site settings -> Domain Management: (colocar o nome que você quiser)
  - Deploys -> Trigger deploy

## Objetivos do projeto para esta parte

- Implementar o back end
  - Modelo de domínio
  - Estruturar o back end no padrão camadas
  - Consulta paginada de vendas
  - Consultas agrupadas para gráficos
  - Implantação na nuvem

## Checklist

### Entendendo o modelo de dados

https://github.com/devsuperior/sds3/raw/main/_assets/dados.xlsx

### Passo 1: configuração de segurança

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	private Environment env;

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		if (Arrays.asList(env.getActiveProfiles()).contains("test")) {
			http.headers().frameOptions().disable();
		}

		http.cors().and().csrf().disable();
		http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
		http.authorizeRequests().anyRequest().permitAll();
	}

	@Bean
	CorsConfigurationSource corsConfigurationSource() {
		CorsConfiguration configuration = new CorsConfiguration().applyPermitDefaultValues();
		configuration.setAllowedMethods(Arrays.asList("POST", "GET", "PUT", "DELETE", "OPTIONS"));
		final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		source.registerCorsConfiguration("/**", configuration);
		return source;
	}
}
```

- **COMMIT: Security config**

### Passo 2: criar as entidades e o seed do banco

#### Modelo conceitual

![Image](https://github.com/devsuperior/bds-assets/raw/main/sds/sds3-mc.png "Modelo conceitual")

#### application.properties

```
spring.jpa.open-in-view=false

spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

- **COMMIT: Domain model, database seed**

### Passo 3: Estruturar o projeto em camadas

### Padrão camadas adotado

![Image](https://github.com/devsuperior/bds-assets/raw/main/sds/camadas.png "Padrão camadas")

- Criar repositories
- Criar DTO's
- Criar service
- Criar controller
- **COMMIT: Layers**

### Passo 4: Busca paginada de vendas

- Pageable
- page, size, sort
- Evitando interações repetidas ao banco de dados
- **COMMIT: Pagination**

### Passo 5: Buscas agrupadas (GROUP BY)

- Total de vendas por vendedor
- Taxa de sucesso por vendedor
- **COMMIT: Group by search**

### Passo 6: Validação no Postgres local

- Criar três perfis de projeto: test, dev, prod
- Gerar script SQL no perfil dev
- Testar projeto no banco Postgres local

#### application.properties

```
spring.profiles.active=test

spring.jpa.open-in-view=false
```

#### application-dev.properties

```
#spring.jpa.properties.javax.persistence.schema-generation.create-source=metadata
#spring.jpa.properties.javax.persistence.schema-generation.scripts.action=create
#spring.jpa.properties.javax.persistence.schema-generation.scripts.create-target=create.sql
#spring.jpa.properties.hibernate.hbm2ddl.delimiter=;

spring.datasource.url=jdbc:postgresql://localhost:5432/dsvendas
spring.datasource.username=postgres
spring.datasource.password=1234567

spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

#### application-prod.properties

```
spring.datasource.url=${DATABASE_URL}
```

#### application.properties

```
spring.profiles.active=${APP_PROFILE:test}

spring.jpa.open-in-view=false
```

#### system.properties

```
java.runtime.version=11
```

- **COMMIT: First homolog**

### Passo 7: Implantação no Heroku

- Criar app no Heroku
- Provisionar banco Postgres
- Definir variável APP_PROFILE=prod
- Conectar ao banco via pgAdmin
- Criar seed do banco

```bash
heroku -v
heroku login
heroku git:remote -a <nome-do-app>
git remote -v
git subtree push --prefix backend heroku main
```

- **PARABÉNS!**

![Parabéns!](https://raw.githubusercontent.com/devsuperior/bds-assets/main/img/trophy.png)

# ![DevSuperior logo](https://raw.githubusercontent.com/devsuperior/bds-assets/main/ds/devsuperior-logo-small.png) Semana Spring React - Episódio 3

> Evento gratuito - 3 a 9 de maio/2021
>
> _Crie um app inédito para seu portfólio com as tecnologias mais demandadas do mercado_

## Realização

[DevSuperior - Escola de programação](https://devsuperior.com.br)

[![DevSuperior no Instagram](https://raw.githubusercontent.com/devsuperior/bds-assets/main/ds/ig-icon.png)](https://instagram.com/devsuperior.ig)
[![DevSuperior no Youtube](https://raw.githubusercontent.com/devsuperior/bds-assets/main/ds/yt-icon.png)](https://youtube.com/devsuperior)

## Objetivos do projeto para esta aula

- Integrar back end e front end
- Três pilares do React
  - Componentes
  - Props
  - Estado
- React Hooks
  - useState
  - useEffect
- Libs
  - React Router DOM
  - Axios

## Checklist

- **AVISO: as aulas ficarão disponíveis somente até dia 9 às 23h59**
- **AVISO: como obter o certificado?**

  - https://github.com/devsuperior/sds3/tree/main/_certificado

- **AVISO: entrar no Discord do evento**
  - https://discord.gg/Y8UZCX8Zmh
  - Apresente-se em `#papo-e-networking`
  - Fique de olho em `#duvidas-frequentes`

### Passo 1: Rotas

- Instalar React Router DOM

```bash
yarn add react-router-dom

yarn add @types/react-router-dom -D
```

- Criar páginas Home e Dashboard
- Criar arquivo de rotas `Routes.tsx`
- **COMMIT: Routes**

### Passo 2: Página Home e navegações

```html
<div className="container">
  <div className="jumbotron">
    <h1 className="display-4">DSVendas</h1>
    <p className="lead">
      Analise o desempenho das suas vendas por diferentes perspectivas
    </p>
    <hr />
    <p>
      Esta aplicação consiste em exibir um dashboard a partir de dados
      fornecidos por um back end construído com Spring Boot.
    </p>
  </div>
</div>
```

- Fazer um link na Home para Dashboard
- Fazer um link na NavBar para Home

- **COMMIT: Home, navigation**

### Passo 3: First request

- Instalar Axios

```bash
yarn add axios
```

- Definir BASE_URL
- Definir tipo SaleSum
- Definir tipo local ChartData em DonutChart
- Fazer a requisição e tratar os dados

- **COMMIT: First request**

### Passo 4: DonutChart integration

```
Hook: useState
Manter estado no componente
```

```
Hook: useEffect
Executar algo na instanciação ou destruição do componente, observar estado
```

- **COMMIT: DonutChart integration**

### Passo 5: BarChart integration

- Definir função auxiliar round:

```javascript
export const round = (value: number, precision: number) => {
  var multiplier = Math.pow(10, precision || 0);
  return Math.round(value * multiplier) / multiplier;
};
```

- Definir tipo SaleSuccess
- Definir tipo local ChartData em DonutChart

- **COMMIT: BarChart integration**

### Passo 6: DataTable integration

- Instalar date-fns ao projeto

```bash
yarn add date-fns
```

- Criar tipos Seller, Sale, SalePage
- Criar função auxiliar formatLocalDate

```javascript
export const formatLocalDate = (date: string, pattern: string) => {
  const dt = new Date(date);
  const dtDateOnly = new Date(
    dt.valueOf() + dt.getTimezoneOffset() * 60 * 1000
  );
  return format(dtDateOnly, pattern);
};
```

- **COMMIT: DataTable integration**

### Passo 7: Pagination

```
Props
Argumentos que um componente React pode receber
```

- Criar componente Pagination

```html
<div className="row d-flex justify-content-center">
  <nav>
    <ul className="pagination">
      <li className="page-item">
        <button className="page-link">Anterior</button>
      </li>
      <li className="page-item disabled">
        <span className="page-link">1</span>
      </li>
      <li className="page-item disabled">
        <button className="page-link">Próxima</button>
      </li>
    </ul>
  </nav>
</div>
```

- **COMMIT: Pagination**

## **PARABÉNS!**

![Parabéns!](https://raw.githubusercontent.com/devsuperior/bds-assets/main/img/trophy.png)

- Quero muito saber seu feedback
  - O que você está achando da nossa abordagem?
  - Você está conseguindo acompanhar?
  - O que você está achando do evento?
- Participe
  - Comente na página da Semana Spring React
  - Divulgue seu projeto no Linkedin e marque a DevSuperior
