### spring 4.3 lombok 없이 log 찍기

```java
@Bean
@Scope("prototype")
public Logger logger(InjectionPoint injectionPoint){
  return LoggerFactory.getLogger(injectionPoint.getMember().getDeclaringClass().getName());
}

@Component
public static class LoggingComponent {
  private final Logger logger;

  public LoggingComponent(Logger logger) {
    this.logger = logger;
    this.logger.info("test");
  }
}
```
