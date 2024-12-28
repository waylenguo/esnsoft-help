## WebService

|日期|作者|备注|
|------|------|------|
|2019-10-30|吕希兵|初稿|
|2019-12-19|吴小海|重构|

### 指南
#### 概念说明

N9B平台支持WebService功能，可以通过三种方式对外发布WebService服务：

1. 基于Spring-WS框架发布WebService服务
1. 基于平台自主研发的注解方式提供WebService服务

#### 使用说明

##### 服务发布

###### Spring-WS

1. POM依赖

 ```xml
    <dependency>
        <groupId>org.springframework.ws</groupId>
        <artifactId>spring-ws-core</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-oxm</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.ws</groupId>
        <artifactId>spring-xml</artifactId>
    </dependency>
 ``` 
 
1. XSD编写

    根据接口需求编写参数对象和响应对象XSD，根据XSD生产Java实体对象
    
1. 编写JavaConfig
    
    使编写的XSD/WSDL暴露在服务上供调用方使用。
    
      ```java
      @EnableWs
      @Configuration
      public class WebServiceConfig extends WsConfigurerAdapter
      {
          @Bean(name = "wsorder")
          public DefaultWsdl11Definition defaultWsdl11DefinitionCountry()
          {
              DefaultWsdl11Definition wsdl11Definition = new DefaultWsdl11Definition();
              wsdl11Definition.setPortTypeName("WsorderPort");
              wsdl11Definition.setLocationUri("/ws");
              wsdl11Definition.setTargetNamespace("http://www.snsoft.com.cn/ws/order");
              wsdl11Definition.setSchema(wsorderSchema());
              return wsdl11Definition;
          }
          
          @Bean
          public XsdSchema wsorderSchema()
          {
              //XSD/WSDL在项目中的位置。
              return new SimpleXsdSchema(new ClassPathResource("snsoft/help/webservice/order.xsd"));
          }
      }
      ```
    
    **示例结果为：http[s]://[ip]:[port]/[ContextName]/ws/wsorder.wsdl**
    
1. 注入Spring封装的WS-Servlet
  
      ```java
      @Bean
      public ServletRegistrationBean messageDispatcherServlet(ApplicationContext applicationContext)
      {
          MessageDispatcherServlet servlet = new MessageDispatcherServlet();
          servlet.setApplicationContext(applicationContext);
          servlet.setTransformWsdlLocations(true);
          return new ServletRegistrationBean(servlet, "/ws/*");
      }
      ```
1. 编写EndPointer服务
    * 示例：
    
    ```java
    @Endpoint("SN-HELP.WSOrderEndPointer")
    public class WSOrderEndPointer implements SpringWSOrderService
    {
        private static final String          NAMESPACE_URI = "http://www.snsoft.com.cn/ws/order";
    
        @Override
        @PayloadRoot(namespace = NAMESPACE_URI, localPart = "WSOrderQueryRequest")
        @ResponsePayload
        public WSOrderQueryResponse payloadQuery(@RequestPayload WSOrderQueryRequest request)
        {
            return new WSOrderQueryResponse();
        }
    
        @PayloadRoot(namespace = NAMESPACE_URI, localPart = "WSOrderSaveRequest")
        @ResponsePayload
        public WSOrderSaveResponse payloadSave(@RequestPayload WSOrderSaveRequest request)
        {
            return new WSOrderSaveResponse();
        }
    }
    ```
    * 注解说明：
    
    ````properties
    @Endpoint：声明一个Endpoint节点服务，同@Service等Spring注解有同样的作用，既注解对应的类会被Spring实例化并注入IOC容器，通过此服务可建立对外访问的服务接口。
    @PayloadRoot：声明对外方法的对应关系，namespace和localPart分别就是wsdl中的targetNamespace和soap方法名称。
    @RequestPayload：声明注解类为请求参数，对应请求XSD/WSDL。
    @ResponsePayload：声明注解类为响应参数，对应响应XSD/WSDL。
    ````


###### Snsoft

1. XSD编写

    根据接口需求编写接口参数对象和响应对象XSD，使用编写的XSD生成对应的java类，并引入项目工程供接口使用。

1. 接口编写

    根据需求编写服务接口，使用@javax.jws.WebService(targetNamespace = "http://www.snsoft.com.cn/ws/XXX")注解声明为WebService接口；
    ```java
    @javax.jws.WebService(targetNamespace = "http://www.snsoft.com.cn/ws/order")
    @snsoft.webservice.annotation.WebService(serverSOAPBuildListener = { "snsoft.webservice.soap.impl.SignatureSOAPBuilderListener.new?SignKeyStoreURI=classpath:snsoft/key/webservice.rsa.key&SignKeyStorePass=123456&SignKeyAlias=webservice&SignKeyPassword=123456" })
    public interface SNWSOrderService{}
    ```
    编写接口方法，示例：
    ```java
    @javax.jws.WebService(targetNamespace = "http://www.snsoft.com.cn/ws/order")
    @snsoft.webservice.annotation.WebService(serverSOAPBuildListener = { "snsoft.webservice.soap.impl.SignatureSOAPBuilderListener.new?SignKeyStoreURI=classpath:snsoft/key/webservice.rsa.key&SignKeyStorePass=123456&SignKeyAlias=webservice&SignKeyPassword=123456" })
    public interface SNWSOrderService
    {
        @WebMethod
        WSOrderQueryResponse queryOrders(@WebParam(name = "request") WSOrderQueryRequest request);
        @WebMethod
        WSOrderSaveResponse saveOrders(@WebParam(name = "request") WSOrderSaveRequest request);
        @WebMethod(exclude = true)
        WSOrderSaveResponse saveOrdersxx(@WebParam(name = "request") WSOrderSaveRequest request);
    }
    //exclude表示从WebService中排除此方法
    ```
   **注解说明**
   ```properties
   @snsoft.webservice.annotation.WebService：南北封装的WebService注解，在使用南北底层提供的WebService服务提供编写方式（即：WSDL地址为http[s]://[ip]:[port]/[ContextName]/uiinvoke/ws-[BeanID]?wsdl）时起作用。
   注解中的属性：
       serverSOAPBuildListener[]：WebService服务调用监听，一般服务端使用此属性配置，底层默认提供了签名验证监听snsoft.webservice.soap.impl.SignatureSOAPBuilderListener.new
       clientSOAPBuildListener[]：WebService服务调用监听，一般服务端使用此属性配置，底层默认提供了签名验证监听snsoft.webservice.soap.impl.SignatureSOAPBuilderListener.new
   签名验证监听参数说明：
       SignKeyStoreURI：签名证书存放位置；
       SignKeyStoreType：证书库类型：X.509、PEM、DER、PKCS7、JKS等
       SignKeyStorePass：证书密码
       SignKeyAlias：别名
       SignKeyPassword：别名密码
       SignMode：签名类型：
                       0：Cert:issuerName, BigInteger serialNumber
                       1：Cert Data
                       2：PublicKey
       SignFlags：签名方向：
                       1：Client：signatureInputSOAP
                       2：Client：verifyOutputSOAP
                       4：服务器端：signatureOutputSOAP
                       8：服务器端：varifyInputSOAP
   ```

1. 编写服务实现

    根据需求编写接口实现。

1. WSDL获取
    ```properties
    WSDL请求地址：http[s]://[ip]:[port]/[ContextName]/uiinvoke/ws-[BeanID]?wsdl
        根据wsdl生成java代码，命令：wsimport -s . http[s]://[ip]:[port]/[ContextName]/uiinvoke/ws-[BeanID]?wsdl
        -s 指生成的java类存放目录
    ```
   
    注意：生成的java代码需要删除没有用的类，标准service里没有用到的类去掉否则可能会引起XmlType重复：
    <img src='help/SN-CMC/WebService/img/sn.png' />
   

##### 服务调用

根据其他系统提供的wsdl地址获取ws客户端代码。

###### Spring调用

1. 注入WebServiceTemplate

    ```java
    @Bean
    public SaajSoapMessageFactory messageFactory()
    {
        return new SaajSoapMessageFactory();
    }
    
    @Bean(name = "webServiceTemplate")
    public WebServiceTemplate webServiceTemplate()
    {
        Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller();
        jaxb2Marshaller.setPackagesToScan("snsoft.**.vo", "snsoft.**.model", "snsoft.**.entity", "snsoft.**.wspoint");
        WebServiceTemplate wsTemplate = new WebServiceTemplate(messageFactory());
        wsTemplate.setMarshaller(jaxb2Marshaller);
        wsTemplate.setUnmarshaller(jaxb2Marshaller);
        return wsTemplate;
    }
    ```

1. 使用WebServiceTemplate调用WebService服务

    示例：
    ```java
    static final String             URL           = "http://127.0.0.1:2020/N9BWS/ws";
    static final String             NAMESPACE_URI = "http://www.snsoft.com.cn/ws/order";
    static final String             SOAPACTION_Q  = NAMESPACE_URI + "/WSOrderQueryRequest";
    
    Object result = wsTemplate.marshalSendAndReceive(URL, request, new SoapActionCallback(SOAPACTION_Q));
    ```

1. 示例<a href="uiinvoke/00/zh_CN/theme0/SN-HELP.WS.PayloadOrder.html" target="_blank">Spring方式调用WebService服务</a>

###### Snsoft调用

1. 使用底层工具类获取服务：snsoft.webservice.client.WSClientHelper.getService(url, clazz)，clazz为获取的客户端代码中的服务类。
    **结果实体类中的集合需要给出Set方法**
    注意：生成的java代码需要删除没有用的类，标准service里没有用到的类去掉否则可能会引起XmlType重复：
    <img src='help/SN-CMC/WebService/img/sn.png' />

1. 注入南北调用监听：
    ````java
    @WebService(name = "SNWSOrderService", targetNamespace = "http://www.snsoft.com.cn/ws/order")
    @XmlSeeAlso({ ObjectFactory.class })
    @snsoft.webservice.annotation.WebService(clientSOAPBuildListener = { "snsoft.webservice.soap.impl.SignatureSOAPBuilderListener.new?SignKeyStoreURI=classpath:snsoft/key/webservice.rsa.key&SignKeyStorePass=123456&SignKeyAlias=webservice&SignKeyPassword=123456" })
    public interface SNWSOrderService
    {
    ...
    }
    ````
    **snsoft.webservice.annotation.WebService注解说明参见<a href="#Snsoft">Snsoft</a>**
    
1. 调用方式
    示例1：
    ````java
    @Service("SN-HELP.WSOrderUIService")
    public class WSOrderUIServiceImpl implements WSOrderUIService
    {
        public WSOrderQueryResponse queryOrder(WSOrderQueryRequest request)
        {
            SNWSOrderService service = WSClientHelper.getService(request.getWsuri(), SNWSOrderService.class);
            response = service.queryOrders(request);
            return response;
        }
    }
    ````
    示例2：在不使用参数对象及接口服务对象进行调用时，提供如下两种方。
    ````java
    //方式一
    WebServiceTemplate wsTemplate = SpringBeanUtils.getBeanByName("webServiceTemplate");
    snsoft.help.webservice.order.wspoint.WSOrderQueryRequest request = new snsoft.help.webservice.order.wspoint.WSOrderQueryRequest();
    request.setOdatefm(new Date());
    request.setSalordcode("NNNNN");
    StringWriter prepareData = new StringWriter();
    wsTemplate.getMarshaller().marshal(request, new StreamResult(prepareData));
    String requestString = prepareData.toString();
    Source requestPayload = new StringSource(requestString);
    StringWriter sw = new StringWriter();
    StreamResult result = new StreamResult(sw);
    wsTemplate.sendSourceAndReceiveToResult(URL, requestPayload, new SoapActionCallback(SOAPACTION_Q), result);
    System.out.println(sw.toString());
    //方式二
    Object qResult = WSCall.invoke("http://127.0.0.1:2020/N9BWS/ws/wsorder.wsdl", "WSOrderQuery", null, 0, JSONObject.fromObject(request));
    System.out.println(qResult);
    ````
    

### 手册	

1. 本文档示例涉及启动工程有：help-run-ui (或help-run-single)和 help-run-webservice
2. 平台支持Webservice服务调用支持加密 可根据实际情况确定是否启用加密相关功能 
2. Webservice服务调用 可通过生成的*客户端代码*、平台工具类、以及WebServiceTemplate多种方式调用,可根据实际情况选择


### 实践

#### DEMO

1. <a href="uiinvoke/00/zh_CN/theme0/SN-HELP.WS.PayloadOrder.html" target="_blank">Spring方式提供WebService服务</a>
1. <a href="uiinvoke/00/zh_CN/theme0/SN-HELP.WS.SnsoftOrder.html" target="_blank">Snsoft方式提供WebService服务</a>

#### 测试工具

1. <a href="uiinvoke/00/zh_CN/theme0/90.WebService.WSDLURLDEF.html" target="_blank">调用地址定义</a>
1. <a href="uiinvoke/00/zh_CN/theme0/90.WebService.WSClientExcute.html" target="_blank">WS-客户端调用</a>

### 设计


### 功能说明








