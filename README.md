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
