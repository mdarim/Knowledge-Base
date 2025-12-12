Here’s a **Spring Boot + Quartz distributed scheduler skeleton** (clustered Quartz) 
you can use as a starting point. This 
setup ensures **multiple application instances share the same Quartz job store** 
and coordinate jobs so they aren’t executed multiple times across the cluster.

---

## 📌 1) Add Dependencies (Maven)

```xml
<dependencies>
    <!-- Spring Boot + Web (optional) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Quartz Scheduler -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-quartz</artifactId>
    </dependency>

    <!-- Your JDBC driver (e.g., MySQL) -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

Quartz will auto-configure the scheduler for you. ([Home][1])

---

## 📌 2) Database Schema

Quartz requires its tables for persistent job storage. Find the SQL scripts in the Quartz distribution under `quartz-core/src/main/resources/org/quartz/impl/jdbcjobstore`.

Run the appropriate script for your database (e.g., MySQL). This creates tables like `QRTZ_JOB_DETAILS`, `QRTZ_TRIGGERS`, etc.

*Tip:* For `spring.quartz.jdbc.initialize-schema=always`, Spring Boot will initialize this automatically on startup for an embedded DB — for MySQL or production DB, typically use `never` and run scripts manually. ([Home][1])

---

## 📌 3) Application Configuration (application.yml)

Enable **JDBC job store + clustering** so multiple instances can run without duplicating job execution:

```yaml
spring:
  quartz:
    job-store-type: jdbc
    jdbc:
      initialize-schema: never      # or "always" only on first run
    properties:
      org:
        quartz:
          scheduler:
            instanceName: ClusteredScheduler
            instanceId: AUTO          # AUTO lets Quartz generate unique ID per instance
          jobStore:
            class: org.quartz.impl.jdbcjobstore.JobStoreTX
            driverDelegateClass: org.quartz.impl.jdbcjobstore.StdJDBCDelegate
            tablePrefix: QRTZ_
            isClustered: true          # enable clustering
            clusterCheckinInterval: 20000
          threadPool:
            class: org.quartz.simpl.SimpleThreadPool
            threadCount: 10
```

**Explanation**

* `spring.quartz.job-store-type=jdbc`: uses DB instead of in-memory. ([Home][1])
* `instanceId=AUTO`: each instance gets a unique ID for cluster coordination. ([CodeHome][2])
* `isClustered=true`: enables clustering. ([CodeHome][2])
* `clusterCheckinInterval`: how often each node checks in with others. ([CodeHome][2])

---

## 📌 4) Define a Quartz Job

Create a job class that Quartz will execute:

```java
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.springframework.stereotype.Component;

@Component
public class SampleJob implements Job {
    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        System.out.println("Quartz Job executed by " +
            Thread.currentThread().getName() + " at " + System.currentTimeMillis());
    }
}
```

Spring will manage this job as a bean, so you can inject other services if needed. ([علي云开发者社区][3])

---

## 📌 5) Schedule the Job

You can schedule jobs programmatically or declaratively via Spring:

### A) **Programmatic Scheduling on Startup**

```java
import org.quartz.*;
import org.springframework.boot.context.event.ApplicationReadyEvent;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class QuartzJobScheduler {

    private final Scheduler scheduler;

    public QuartzJobScheduler(Scheduler scheduler) {
        this.scheduler = scheduler;
    }

    @EventListener(ApplicationReadyEvent.class)
    public void scheduleJobs() throws SchedulerException {
        JobDetail jobDetail = JobBuilder.newJob(SampleJob.class)
            .withIdentity("clusteredSampleJob")
            .storeDurably()
            .build();

        Trigger trigger = TriggerBuilder.newTrigger()
            .forJob(jobDetail)
            .withSchedule(CronScheduleBuilder.cronSchedule("0/30 * * * * ?")) // every 30 seconds
            .build();

        scheduler.scheduleJob(jobDetail, trigger);
    }
}
```

---

## 🧠 How It Works (Clustered Quartz)

Quartz Cluster mode with JDBC:

* All app instances point to the **same Quartz DB**.
* Quartz uses the database to coordinate job triggers.
* Only one node executes a given trigger at a time.
* If one node fails, another can pick up the scheduled jobs. ([CodeHome][2])

---

## 📌 Notes & Best Practices

✅ **Unique instance IDs:** `AUTO` avoids manual ID clashes across pods/services. ([CodeHome][2])
✅ **DB choice:** Use a highly available relational DB (MySQL/PostgreSQL).
✅ **Schema initialization:** Typically run only once or manually, not on every startup. ([Home][1])
✅ **Monitoring:** Consider adding logs/metrics to Quartz jobs for observability.

---

## 🧾 Summary Config Recap

| Setting                  | Purpose                                  |
|--------------------------|------------------------------------------|
| `job-store-type=jdbc`    | Persist scheduler state                  |
| `isClustered=true`       | Enable cluster coordination              |
| `instanceId=AUTO`        | Distinct scheduler identity per instance |
| `clusterCheckinInterval` | Node heartbeat interval                  |

[1]: https://docs.spring.io/spring-boot/reference/io/quartz.html?utm_source=chatgpt.com "Quartz Scheduler :: Spring Boot"
[2]: https://www.codehome.vip/archives/springboot-quartz?utm_source=chatgpt.com "SpringBoot2.x基础教程: 集成Quartz分布式任务调度 | 编程之家"
[3]: https://developer.aliyun.com/article/1561949?utm_source=chatgpt.com "使用Spring Boot与Quartz实现分布式任务调度-开发者社区-阿里云"
