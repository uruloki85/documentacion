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

###Generació del client de WS
Creació automàtica del client d'un web service a partir del WSDL en el <code>pom.xml</code>:
```xml
<plugin>
    <groupId>org.jvnet.jax-ws-commons</groupId>
	<artifactId>jaxws-maven-plugin</artifactId>
	<version>2.2</version>
	<executions>
		<execution>
			<id>persones-identitat</id>
			<goals>
				<goal>wsimport</goal>
			</goals>
			<configuration>
				<packageName>es.upcnet.efactura.ws.client.identitat.persones</packageName>
				<target>2.0</target>
				<wsdlUrls>
				    <wsdlUrl>https://bus-soades.upc.edu/GestioIdentitat/Personesv4?wsdl</wsdlUrl>
				</wsdlUrls>
				<keep>true</keep>
				<xnocompile>false</xnocompile>
			</configuration>
		</execution>
	</executions>
</plugin>
```

###Utilitzar un client de WS generat amb JAX-WS

Creem el **bean** que s'injectarà al codi amb la configuració corresponent del web service:
```java
@Configuration
public class WSConfiguration {
	
	@Autowired
	private PersonaIdentitatWSProperties personaIdentitatWSProperties;
	
	@Bean(name = "serveiWebPersonesIdentitat")
	public Personesv4 serveiWebPersonesIdentitat() throws MalformedURLException {
		
		JaxWsPortProxyFactoryBean proxy = new JaxWsPortProxyFactoryBean();
		proxy.setServiceInterface(Personesv4.class);
		proxy.setWsdlDocumentUrl(new URL(personaIdentitatWSProperties.getWsdlUrl()));
		proxy.setNamespaceUri(PersonaIdentitatWSProperties.NAMESPACE_URI);
		proxy.setServiceName(PersonaIdentitatWSProperties.SERVICE_NAME);
		proxy.setPortName(PersonaIdentitatWSProperties.PORT_NAME);
		proxy.setHandlerResolver(getHandlerResolver());
		proxy.setLookupServiceOnStartup(false);
		
		return proxy.createJaxWsService().getPort(Personesv4.class);
	}
	
	private WsSecurityHandlerResolver getHandlerResolver() {
		WsSecurityHandlerResolver handlerResolver = new WsSecurityHandlerResolver();
		handlerResolver.setSecurityHandler(getSecurityHandler());
		
		return handlerResolver;
	}

	private WsSecurityHandler getSecurityHandler() {
		WsSecurityHandler securityHandler = new WsSecurityHandler();
		securityHandler.setUsername(personaIdentitatWSProperties.getUsername());
		securityHandler.setPassword(personaIdentitatWSProperties.getPassword());
		
		return securityHandler;
	}
}
```
Si utilitzem XMLs per configurar:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:aop="http://www.springframework.org/schema/aop" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jee="http://www.springframework.org/schema/jee" 
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.2.xsd         
	http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd         
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd         
	http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-3.2.xsd         
	http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
	http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

	<context:property-placeholder location="classpath*:META-INF/spring/*.properties"/>

	<bean id="serveiWebPersonesIdentitat" class="org.springframework.remoting.jaxws.JaxWsPortProxyFactoryBean">
		<property name="serviceInterface" value="es.upcnet.tarifes.ws.client.identitat.persones.Personesv4" />
		<property name="wsdlDocumentUrl" value="${ws.identitat.persones}" />
		<property name="namespaceUri" value="http://soa.identitatdigital.upc.edu/Personesv4" />
		<property name="serviceName" value="PersonaServiceV4Service" />
		<property name="portName" value="Personesv4Port" />
		<property name="handlerResolver" ref="wsSecurityHandlerResolverTarifes" />
		<property name="lookupServiceOnStartup" value="false" />
	</bean>	

	<bean id="wsSecurityHandlerResolverTarifes" class="es.upcnet.ws.handler.WsSecurityHandlerResolver">
		<property name="securityHandler" ref="securityHandlerTarifes" />
	</bean>

	<bean id="securityHandlerTarifes" class="es.upcnet.ws.handler.WsSecurityHandler">
		<property name="username" value="${ws.sap.tarifes.username}" />
		<property name="password" value="${ws.sap.tarifes.password}" />
	</bean>
 
</beans>
```
Creem el servei on s'injectarà el **bean** que acabem de crear. Es necessari utilitzar l'anotació <code>@Resource</code> per tal d'especificar el nom del bean.

També és recomanable indicar el nom del servei (per convenció, el nom de la classe amb la primera lletra en minúscula).
```java
@Service("personaIdentitatHelperWS")
public class PersonaIdentitatHelperWS implements PersonaIdentitatHelper {

	@Resource(name = "serveiWebPersonesIdentitat")
	private Personesv4 personesv4;

	@Override
	public Integer obtenirIdPersona(String commonName) {
		// Invocació del servei
		ObtenirDadesPersonaRespostaV6 dadesPersona = personesv4.obtenirDadesPersona(null, commonName, null);
		
		return dadesPersona.getIdGauss();
	}
	
}
```

###Gestionar diversos idiomes
Primer cal configurar Spring per a que sigui possible canviar el <code>locale</code> de l'aplicació:
```java
/**
 * Configuració per a que l'aplicació gestioni més d'un idioma. 
 *
 */
@Configuration
public class LocaleConfiguration extends WebMvcConfigurerAdapter {
	
	/**
	 * Permet a l'aplicació determinar quin és el locale actual.
	 * 
	 * @return
	 */
	@Bean
	public LocaleResolver localeResolver() {
	    SessionLocaleResolver slr = new SessionLocaleResolver();
	    slr.setDefaultLocale(new Locale("ca", "ES"));
	    return slr;
	}
	
	/**
	 * Interceptor responsable de canviar el locale actual.
	 * 
	 * @return
	 */
	@Bean
	public LocaleChangeInterceptor localeChangeInterceptor() {
	    LocaleChangeInterceptor lci = new LocaleChangeInterceptor();
	    lci.setParamName("lang");
	    return lci;
	}
	
	/**
	 * Per a que l'interceptor tingui efecte, l'afegim al registre d'interceptor
	 * de l'aplicació.
	 * 
	 * @param registry
	 */
	@Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(localeChangeInterceptor());
    }
}
```
Fet això, cal crear els fitxers <code>message_es.properties</code> i <code>message_ca.properties</code> (pels locales castellà i català) i si concatenem **lang=ca** a la URL, p.e. http://localhost:8080/visor/3?lang=ca, l'aplicació ja canvia automàticament d'un fitxer a l'altre.

Ara, per a que l'usuari pugui canviar l'idioma a voluntat, cal fer el següent:

