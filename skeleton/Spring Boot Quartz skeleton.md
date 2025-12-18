Here’s a comprehensive **advanced guide** to using **Quartz with Spring Boot 4.0.0**—covering configuration, job definitions, persistence, clustering, customization, and monitoring.

---

## 🔧 1. Getting Started — Dependencies

Add the Quartz starter:

**Maven**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
```

This enables **auto-configuration** for Quartz via `QuartzAutoConfiguration` in Spring Boot 4.0.0. ([Home][1])

---

## 🧠 2. Basic Quartz Concepts Refresher

Quartz uses key components:

* **JobDetail** — definition of a job class
* **Trigger** — schedule (cron, simple interval)
* **Scheduler** — orchestrates triggers and jobs

Spring Boot will auto-configure a `Scheduler` if Quartz is on the classpath. ([Home][2])

---

## 📅 3. Configure Quartz in `application.yml`

### 🆗 In-Memory Scheduler (default)

Useful for simple scheduling or dev:

```yaml
spring:
  quartz:
    job-store-type: memory
    overwrite-existing-jobs: true
```

---

## 💾 4. Persisting Jobs — JDBC JobStore

To survive restarts and enable clustering:

### 💡 Properties

```yaml
spring:
  quartz:
    job-store-type: jdbc
    overwrite-existing-jobs: true
    jdbc:
      initialize-schema: always
    properties:
      org.quartz.threadPool.threadCount: 10
      org.quartz.scheduler.instanceName: MyScheduler
      org.quartz.scheduler.instanceId: AUTO
      org.quartz.jobStore.isClustered: true
```

* `job-store-type=jdbc` switches Quartz to a database job store.
* `initialize-schema=always` creates standard Quartz tables at startup.
* `isClustered=true` enables **clustered scheduling** (multiple app instances share the same schedule). ([Home][2])

❗ Quartz ships SQL scripts for many DBs; you can provide custom schema via `spring.quartz.jdbc.schema`.

---

## 🚀 5. Define Quartz Jobs Programmatically

### 👤 Job Class

Extend `QuartzJobBean` to let Spring inject services:

```java
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.springframework.scheduling.quartz.QuartzJobBean;

public class MyJob extends QuartzJobBean {

    private MyService myService;

    public void setMyService(MyService myService) { this.myService = myService; }

    @Override
    protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
        myService.perform();
    }
}
```

---

## 🧱 6. Register Jobs + Triggers with Scheduler

You can register jobs and triggers manually:

```java
@Configuration
public class QuartzConfig {

    @Bean
    public JobDetail myJobDetail() {
        return JobBuilder.newJob(MyJob.class)
                         .withIdentity("myJob")
                         .storeDurably()
                         .build();
    }

    @Bean
    public Trigger myTrigger(JobDetail myJobDetail) {
        return TriggerBuilder.newTrigger()
                             .forJob(myJobDetail)
                             .withSchedule(CronScheduleBuilder.cronSchedule("0 0/5 * ? * *"))
                             .build();
    }
}
```

Spring Boot auto-registers these beans with the Scheduler. ([Home][2])

---

## ⚙️ 7. Quartz Properties

Fine-tune Quartz using:

```yaml
spring:
  quartz:
    properties:
      org.quartz.scheduler.makeSchedulerThreadDaemon: true
      org.quartz.jobStore.misfireThreshold: 60000
      org.quartz.threadPool.threadCount: 20
```

These map directly to the underlying Quartz config – e.g., thread pool, misfire handling, clustering behavior. ([Home][2])

---

## 🔧 8. Customizing SchedulerFactoryBean

If you need lower-level customization (executors, job listeners, etc.), provide a `SchedulerFactoryBeanCustomizer`:

```java
@Bean
public SchedulerFactoryBeanCustomizer customizer() {
    return factory -> {
        factory.setStartupDelay(10);  // delay scheduler startup
        factory.setAutoStartup(true);
        // additional customizations…
    };
}
```

This hook lets you configure Quartz beyond what properties alone can do. ([Home][2])

---

## 📊 9. Monitoring with Actuator

Spring Boot Actuator exposes a **Quartz endpoint**:

```
GET /actuator/quartz
```

This returns job and trigger group info, and you can trigger jobs on demand:

```
POST /actuator/quartz/jobs/{group}/{jobName}
{"state":"running"}
```

This is great for introspection and dynamic control. ([Home][3])

---

## 🪢 10. Clustering Best Practices

For robust clustering:

1. Use **JDBC JobStore** with a shared DB.
2. Set `org.quartz.jobStore.isClustered=true`.
3. Use unique `instanceId=AUTO`.
4. Ensure all nodes use the same DB schema.

This ensures jobs run exactly once across the cluster. ([DEV Community][4])

---

## 🧠 11. When to Use Quartz vs Spring Scheduler

* **Spring `@Scheduled`** is lightweight and good for simple periodic tasks.
* **Quartz** excels when you need:

    * persistent schedules,
    * dynamic job creation,
    * clustering/multi-node coordination,
    * cron expressions with misfire handling,
    * external trigger control (REST/Actuator). ([DEV Community][4])

---

## 🧪 12. Troubleshooting Tips

✅ **Jobs not running?**

* Ensure job details and triggers are registered with the Scheduler.

✅ **Tables missing?**

* Set `spring.quartz.jdbc.initialize-schema=always` or import scripts manually.

✅ **Multiple execution on clustered nodes?**

* Verify `isClustered=true` and shared DB settings.

---

If you want, I can also provide a **full sample project layout** or a **ready-to-run GitHub template** for Spring Boot 4 + Quartz with clustering!

[1]: https://docs.spring.io/spring-boot/4.0/api/java/org/springframework/boot/quartz/autoconfigure/QuartzAutoConfiguration.html?utm_source=chatgpt.com "QuartzAutoConfiguration (Spring Boot 4.0.0 API)"
[2]: https://docs.spring.io/spring-boot/reference/io/quartz.html?utm_source=chatgpt.com "Quartz Scheduler :: Spring Boot"
[3]: https://docs.spring.io/spring-boot/api/rest/actuator/quartz.html?utm_source=chatgpt.com "Quartz (quartz) :: Spring Boot"
[4]: https://dev.to/dixitgurv/spring-boot-scheduling-best-practices-503h?utm_source=chatgpt.com "Spring Boot Scheduling Best Practices - DEV Community"
