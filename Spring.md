Spring
======

Connexió a BDs
--------------

* Quan s'utilitza més d'una BD, cal especificar el <code>persistanceUnitName</code>. Es pot especificar el nom que es desitji.
- Utilitzant una classe Java:
<code>
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
		factory.setPackagesToScan("es.upcnet.efactura.repository.sql","es.upcnet.efactura.model.sql");
		factory.setDataSource(dataSource());
		factory.setJpaVendorAdapter(vendorAdapter);
		factory.setJpaProperties(additionalProperties());
		//Quan hi ha més d'una connexió a BD, cal especificar la persistenceUnitName:
		<b>factory.setPersistenceUnitName("postgresDB");</b>
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
</code>
- Utilitzant un fitxer de configuració XML:
<pre><code>
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
</code></pre>