---
title: spring boot部分源码解读
date: "2020-10-31"
spoiler: spring boot, 源码学习
---

## spring boot 启动

```java
/**
	 * Run the Spring application, creating and refreshing a new
	 * {@link ApplicationContext}.
	 * @param args the application arguments (usually passed from a Java main method)
	 * @return a running {@link ApplicationContext}
	 */
	public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
		configureHeadlessProperty();
		// 获取监听器
		// 监听顺序：starting->environmentPrepared->contextPrepared->contextLoaded->started
		SpringApplicationRunListeners listeners = getRunListeners(args);
		// 启动监听器
		listeners.starting();
		try {
			// 1. application 启动参数列表
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
			// 2. 读取要激活的配置文件
			ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
			configureIgnoreBeanInfo(environment);
			Banner printedBanner = printBanner(environment);

			// 3. 创建应用上下文
			context = createApplicationContext();
			exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
					new Class[] { ConfigurableApplicationContext.class }, context);
			// 4. 准备上下文，装配bean（完成单例对象的注册；绑定上下文到监听器）
			prepareContext(context, environment, listeners, applicationArguments, printedBanner);
			// 5. 刷新context
			refreshContext(context);
			// 6. 刷新context后做的事情（未实现）
			afterRefresh(context, applicationArguments);
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
			}
			// 6. 启动监听
			listeners.started(context);
			// 7. 唤醒（可能启动ApplicationRunner和CommandLineRunner）
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, listeners);
			throw new IllegalStateException(ex);
		}

		try {
			// 8. 运行监听
			listeners.running(context);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
```

## runListeners

```java
	private SpringApplicationRunListeners getRunListeners(String[] args) {
		Class<?>[] types = new Class<?>[] { SpringApplication.class, String[].class };
		// 创建springFactory实例
		return new SpringApplicationRunListeners(logger,
				getSpringFactoriesInstances(SpringApplicationRunListener.class, types, this, args));
	}

	private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
		ClassLoader classLoader = getClassLoader();
		// Use names and ensure unique to protect against duplicates
		// 读取springfactory名称，使用linkedhashset保证唯一性和顺序
		Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
		// 创建springFactory实例【按类名】
		List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
		// 排序
		AnnotationAwareOrderComparator.sort(instances);
		return instances;
	}
```
