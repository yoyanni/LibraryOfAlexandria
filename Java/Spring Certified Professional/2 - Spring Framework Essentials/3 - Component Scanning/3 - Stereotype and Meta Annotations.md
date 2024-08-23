# Stereotype Annotations

Component scanning also checks for annotations that are themselves annotated with `@Component`

***NOTE:*** `@Service` annotation is part of the Spring framework

![[Pasted image 20240806165745.png]]


# Predefined Stereotype Annotations

The below annotations are available in Spring.

![[Pasted image 20240806170032.png]]

# Meta-annotations

An Annotation which can be used to annotate other annotations.

![[Pasted image 20240806170520.png]]

# Summary

Spring beans can be defined:
- Explicitly using `@Bean` methods inside a configuration class
- Implicitly using `@Component` and component-scanning

Can perform initialisation and clean-up:
- Use `@PostConstruct` and `@PreDestroy`

Use Spring's stereotypes and/or define your own meta annotations.