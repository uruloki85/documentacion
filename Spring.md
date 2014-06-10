#Spring

###Fitxer de propietats YAML
Es poden crear tants fitxers com es vulgui, p.e., un per entorn: <code>application-dsv.yml</code>, <code>application-pre.yml</code> i <code>application-pro.yml</code>. 

Després, al fitxer <code>application.properties</code> s'especifica l'entorn a utilitzar: <code>spring.profiles.active=dsv</code>.


###Connexió a BDs
* Quan s'utilitza més d'una BD, cal especificar el <code>persistanceUnitName</code>. Es pot especificar el nom que es desitji.

Si s'utilitza una classe Java per configurar:
```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(basePackages="es.upcnet.efactura.repository.sql")
public class PostgreSQLConfiguration {
	
	@Autowired
	private PostgreSQLProperties postgreSQLProperties;

	public @Bean DataSource dataSource() {
		DriverManagerDataSource dataSource = new DriverManagerDataSource();
		dataSource.setUrl(postgreSQLProperties.getDatabaseUrl());
		dataSource.setUsername(postgreSQLProperties.getUsername());
		dataSource.setPassword(postgreSQLProperties.getPassword());
		dataSource.setDriverClassName(postgreSQLProperties.getDirverClassName());

		return dataSource;
	}

	public @Bean LocalContainerEntityManagerFactoryBean entityManagerFactory() {

		HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
		vendorAdapter.setGenerateDdl(true);

		LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
		//Package on es troben les entitats
		factory.setPackagesToScan("es.upcnet.efactura.model.sql");
		factory.setDataSource(dataSource());
		factory.setJpaVendorAdapter(vendorAdapter);
		factory.setJpaProperties(additionalProperties());
		//Quan hi ha més d'una connexió a BD, cal especificar la persistenceUnitName:
		factory.setPersistenceUnitName("postgresDB");
		return factory;
	}
	
	public @Bean PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
		
		JpaTransactionManager transactionManager = new JpaTransactionManager();
		transactionManager.setEntityManagerFactory(emf);

		return transactionManager;
	}

	public @Bean PersistenceExceptionTranslationPostProcessor exceptionTranslation() {
		return new PersistenceExceptionTranslationPostProcessor();
	}

	private Properties additionalProperties() {
		Properties properties = new Properties();
		properties.setProperty("hibernate.hbm2ddl.auto", "validate");
		properties.setProperty("hibernate.dialect", "org.hibernate.dialect.PostgreSQLDialect");
		properties.setProperty("hibernate.ejb.naming_strategy", "org.hibernate.cfg.ImprovedNamingStrategy");
		properties.setProperty("hibernate.connection.charSet", "UTF-8");
		return properties;
	}
	
}
```
    
Si s'utilitza un fitxer de configuració XML:
    
```XML
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.0" xsi:schemaLocation="http://java.sun.com/xml/ns/persistence ">http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">
	<persistence-unit name="persistenceUnit" transaction-type="RESOURCE_LOCAL">
        <provider>org.hibernate.ejb.HibernatePersistence</provider>
        <properties>
            <property name="hibernate.dialect" value="org.hibernate.dialect.PostgreSQLDialect"/>
            <!-- value="create" to build a new database on each run; value="update" to modify an existing database; value="create-drop" means the same as "create" but also drops tables when Hibernate closes; value="validate" makes no changes to the database -->
            <property name="hibernate.hbm2ddl.auto" value="validate"/>
            <property name="hibernate.ejb.naming_strategy" value="org.hibernate.cfg.ImprovedNamingStrategy"/>
            <property name="hibernate.connection.charSet" value="UTF-8"/>

            <!-- Auditing -->
            <property name="org.hibernate.envers.audit_strategy" value="org.hibernate.envers.strategy.ValidityAuditStrategy"/>
            <property name="org.hibernate.envers.audit_strategy_validity_store_revend_timestamp" value="true" />
        </properties>
    </persistence-unit>
</persistence>
```

En el cas de treballar amb MongDB, no sé com s'estableix aquest nom! Si conviuen una mongo i una SQL, establint només el ```persistanceUnitName``` de la SQL ja n'hi ha prou per a que funcioni correctament.

* També cal especificar aquest nom a les entitats (```@Entity```) mitjançant l'anotació ```@PersistenceUnit```:
```java
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name="factura", schema = "public")
@PersistenceUnit(name="postgresDB")
public class Factura {

    @Id
    @SequenceGenerator(name = "facturaGen", sequenceName = "seq_factura")
    @GeneratedValue(strategy = GenerationType.AUTO, generator = "facturaGen")
    @Column(name = "id")
	private Long id;

	@NotNull
	@Column(length = 1, nullable = false)
	private String validada;
	
	@Temporal(TemporalType.TIMESTAMP)
	private Date dataValidacio;
	
	@Version
	private Integer version;
	
}
```

###[Protección contra ataques CSRF](http://docs.spring.io/spring-security/site/docs/3.2.4.RELEASE/reference/htmlsingle/#csrf)

1. La protección por CSRF está activa por defecto cuando se configura Spring Security con clases Java.
2. Si no se quiere usar, desactivar mediante (en nuestro caso ese método esta en la clase que configura el CAS):
```java
@EnableWebSecurity
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http.csrf().disable();
	}
}
```
3. Para hacer submits del tag <code>&lt;form></code>, si se usa Spring MVC o Thymeleaf y se sustituye la anotación <code>@EnableWebSecurity</code> por <code>@EnableWebMvcSecurity</code>, el token CSRF se incluye automáticamente.

	Pero, si se hace esto y el submit es por GET (método por defecto) el token CSRF se verá en la URL. Para evitar esto, hay que especificar que se use el método POST:
```html
<form method="POST" id="cercar-factura-form" action="cercador.html" data-th-action="@{/}"></form>
```
4. Para peticiones Ajax y Json que sean PATCH, POST, PUT o DELETE
Añadir en la cabecera Html:
```html
<html>
	<head>
		<meta name="_csrf" data-th-content="${_csrf.token}"/>
		<!-- default header name is X-CSRF-TOKEN -->
		<meta name="_csrf_header" data-th-content="${_csrf.headerName}"/>
    		<!-- ... -->
  	</head>
</html>
```
	Hay que añadir el token CSRF en todas las llamadas Ajax:
```javascript
var token = $("meta[name='_csrf']").attr("content");
var header = $("meta[name='_csrf_header']").attr("content");
$.ajax({
	url: ...,
	cache: false,
	dataType: "json",
	type: "POST",
	data: { ... },
	beforeSend: function(jqXHR, settings) {
		jqXHR.setRequestHeader(header, token);
	}
})
```
	Si no se incluye el token, el servidor devolverá el error *403 Forbidden*.
