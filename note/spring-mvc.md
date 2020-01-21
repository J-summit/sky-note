## 1.工作流程

![1579520891258](.\img\springmvc-工作流程.jpg)

## 2.九大组件

```
代码地址org.springframework.web.servlet.DispatcherServlet#initStrategies
	protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context);
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
```

