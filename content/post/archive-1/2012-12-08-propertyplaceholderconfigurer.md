---
title: PropertyPlaceholderConfigurer
author: wiloon
type: post
date: 2012-12-08T11:42:57+00:00
url: /?p=4865
categories:
  - Java

---
<http://callan.iteye.com/blog/161540>

关于PropertyPlaceholderConfigurer与PropertyOverrideConfigurer

PropertyPlaceholderConfigurer，允许在spring的配置文件中加入properties文件，可以将一些动态参数移到properties中．

<div id="">
  <p>
    [java]</pre><br /> </div><br /> <div><bean id="propertyConfigurer"<br /> class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer"><br /> <property name="location" value="classpath:config/jdoserver.properties"/><br /> </bean></div><br /> <div>[/java]
  </p>
</div>

但是好像在属性文件定义中却不支持多个属性文件的定义，比如不能这样用config/*.properties。

经过查看源码，发现可以使用locations属性定义多个配置文件：

<div id="">
  <div>
    <div>
      Java代码  <a title="收藏这段代码"><img src="http://callan.iteye.com/images/icon_star.png" alt="收藏代码" /></a>
    </div>
  </div>
  
  <ol start="1">
    <li>
      <property name=&#8221;locations&#8221;>
    </li>
    <li>
                  <list>
    </li>
    <li>
                      <value>classpath:config/maxid.properties</value>
    </li>
    <li>
                      <value>classpath:config/jdoserver.properties</value>
    </li>
    <li>
                  </list>
    </li>
    <li>
      </property>
    </li>
  </ol>
</div>

使用外部属性后如下：

<div id="">
  <div>
    <div>
      Java代码  <a title="收藏这段代码"><img src="http://callan.iteye.com/images/icon_star.png" alt="收藏代码" /></a>
    </div>
  </div>
  
  <ol start="1">
    <li>
      <bean id=&#8221;dataSource&#8221; class=&#8221;org.springframework.jdbc.datasource.DriverManagerDataSource&#8221;>
    </li>
    <li>
              <property name=&#8221;driverClassName&#8221; value=&#8221;${jdbc.agent.driver}&#8221;/>
    </li>
    <li>
              <property name=&#8221;url&#8221; value=&#8221;${jdbc.agent.main.url}&#8221;/>
    </li>
    <li>
          </bean>
    </li>
  </ol>
</div>

PropertyOverrideConfigurer：在spring所有的bean初使化以后，将bean的值强行改变

<div id="">
  <div>
    <div>
      Xml代码  <a title="收藏这段代码"><img src="http://callan.iteye.com/images/icon_star.png" alt="收藏代码" /></a>
    </div>
  </div>
  
  <ol start="1">
    <li>
      <bean id=&#8221;configBean&#8221;
    </li>
    <li>
         class=&#8221;org.springframework.beans.factory.config.PropertyOverrideConfigurer&#8221;>
    </li>
    <li>
               <property name=&#8221;location&#8221;>
    </li>
    <li>
                   <value>hello.properties</value>
    </li>
    <li>
               </property>
    </li>
    <li>
           </bean>
    </li>
    <li>
    </li>
    <li>
           <bean id=&#8221;helloBean&#8221; class=&#8221;com.HelloBean&#8221;>
    </li>
    <li>
               <property name=&#8221;word&#8221;>
    </li>
    <li>
                   <value>Hello!</value>
    </li>
    <li>
               </property>
    </li>
    <li>
           </bean>
    </li>
  </ol>
</div>

定义HelloBean,注入word的值为hello.

在hello.properties中

helloBean.word=Welcome!

word初使为hello后，当bean全加载完,PropertyOverrideConfigurer将helloBean.word的值改成为welcome.