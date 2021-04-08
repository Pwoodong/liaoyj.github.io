## SpringBoot 自动装配源代码追踪解析

### 自动装配

自动装配顾名思义就是自动进行配置，不需要人工处理，开箱即用；SpringBoot 自动装配过程就是读取META-INF/spring.factories文件，SpringFactoriesLoader加载配置中的对象。

### 重点注解

@SpringBootApplication

@EnableAutoConfiguration

### 重点类

```
AutoConfigurationImportSelector
DeferredImportSelector
ImportSelector
```

### 核心代码追踪解析

1、由@SpringBootApplication注解进入@EnableAutoConfiguration注解

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
...
}
```

@Import(AutoConfigurationImportSelector.class)导入自动装配的注解类

2、点进AutoConfigurationImportSelector类

```
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
		...
}
```

```
public interface DeferredImportSelector extends ImportSelector {
...
}
```

```
public interface ImportSelector {

	String[] selectImports(AnnotationMetadata importingClassMetadata);

	@Nullable
	default Predicate<String> getExclusionFilter() {
		return null;
	}

}
```

实现接口是DeferredImportSelector,接口是继承的ImportSelector类，类ImportSelector中有实现方法selectImports；

3、进入AutoConfigurationImportSelector类找到具体实现方法selectImports

```
@Override
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return NO_IMPORTS;
		}
		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```

isEnabled判断自动装配的开关；getAutoConfigurationEntry是获取需要装配的Bean；

4、点进getAutoConfigurationEntry方法

```
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
		configurations = removeDuplicates(configurations);
		Set<String> exclusions = getExclusions(annotationMetadata, attributes);
		checkExcludedClasses(configurations, exclusions);
		configurations.removeAll(exclusions);
		configurations = getConfigurationClassFilter().filter(configurations);
		fireAutoConfigurationImportEvents(configurations, exclusions);
		return new AutoConfigurationEntry(configurations, exclusions);
}
```

```
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
}
```

getCandidateConfigurations是读取META-INF/spring.factories；通过SpringFactoriesLoader类的loadFactoryNames方法读取`spring.factories`文档，对给定的type及`factoryClass`加载和实例化其工厂类。

