---
title: common logger log4j
author: wiloon
type: post
date: 2011-11-19T03:03:48+00:00
url: /?p=1529
bot_views:
  - 6
views:
  - 4
categories:
  - Java

---
我们在做项目时，日志的记录是必不可少的一项任务，而我们通常是使用 apache 的 log4j 日志管理工具。然而，在项目中，我们经常会看到两个 jar 包：commons-logging.jar 和 log4j.rar。为什么我们在使用 log4j 的同时还要引入 commons-logging.jar 呢，或者说不用 commons-logging.jar 可不可以，这两者之间到底是怎么的一种关系呢？

作为记录日志的工具，它至少应该包含如下几个组成部分(组件)：
  
1. Logger
  
记录器组件负责产生日志，并能够对日志信息进行分类筛选，控制什么样的日志应该被输出，什么样的日志应该被忽略。它还有一个重要的属性 － 日志级别。不管何种日志记录工具，大概包含了如下几种日志级别：DEBUG, INFO, WARN, ERROR 和 FATAL。
  
2. Level
  
日志级别组件。
  
3. Appender
  
日志记录工具基本上通过 Appender 组件来输出到目的地的，一个 Appender 实例就表示了一个输出的目的地。
  
4. Layout
  
Layout 组件负责格式化输出的日志信息，一个 Appender 只能有一个 Layout。

我们再来看看 log4j.jar，打开 jar 包，我们可以看到 Logger.class(Logger)，Level.class(Level), FileAppender.class(Appender)， HTMLLayout.class(Layout)。其它的我们先忽略不看，这几个字节码文件正好是记录日志必不可少的几个组件。

接下来看看 commons-logging 中的 org.apache.commons.logging.Log.java 源码：
  
Java代码
  
package org.apache.commons.logging;
  
public interface Log {
  
public boolean isDebugEnabled();
  
public boolean isErrorEnabled();
  
public boolean isFatalEnabled();
  
public boolean isInfoEnabled();
  
public boolean isTraceEnabled();
  
public boolean isWarnEnabled();
  
public void trace(Object message);
  
public void trace(Object message, Throwable t);
  
public void debug(Object message);
  
public void debug(Object message, Throwable t);
  
public void info(Object message);
  
public void info(Object message, Throwable t);
  
public void warn(Object message);
  
public void warn(Object message, Throwable t);
  
public void error(Object message);
  
public void error(Object message, Throwable t);
  
public void fatal(Object message);
  
public void fatal(Object message, Throwable t);
  
}

很显然，只要实现了 Log 接口，它就是一个名副其实的 Logger 组件，也验证了 Logger 组件具有日志级别的属性。继续看 commons-logging org.apache.commons.logging.impl 包下的几个类的源码片段：
  
Java代码
  
package org.apache.commons.logging.impl;

import org.apache.commons.logging.Log;
  
import org.apache.log4j.Logger;
  
import org.apache.log4j.Priority;
  
import org.apache.log4j.Level;
  
import &#8230;&#8230;

public class Log4JLogger implements Log, Serializable {
  
// 对 org.apache.commons.logging.Log 的实现
  
&#8230;&#8230;
  
}

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

package org.apache.commons.logging.impl;

import org.apache.commons.logging.Log;
  
import java.io.Serializable;
  
import java.util.logging.Level;
  
import java.util.logging.Logger;

public class Jdk14Logger implements Log, Serializable {
  
// 对 org.apache.commons.logging.Log 的实现
  
&#8230;&#8230;
  
}

好了，分析到这里，我们应该知道，真正的记录日志的工具是 log4j 和 sun 公司提供的日志工具。而 commons-logging 把这两个(实际上，在 org.apache.commons.logging.impl 包下，commons-logging 仅仅为我们封装了 log4j 和 sun logger)记录日志的工具重新封装了一遍(Log4JLogger.java 和 Jdk14Logger.java)，可以认为 org.apache.commons.logging.Log 是个傀儡，它只是提供了对外的统一接口。因此我们只要能拿到 org.apache.commons.logging.Log，而不用关注到底使用的是 log4j 还是 sun logger。正如我们经常在项目中这样写：
  
Java代码
  
// Run 是我们自己写的类，LogFactory 是一个专为提供 Log 的工厂(abstract class)
  
private static final Log logger = LogFactory.getLog(Run.class);

既然如此，我们向构建路径加了 commons-logging.jar 和 log4j.jar 两个 jar 包，那我们的应用程序到底使用的 log4j 还是 sun logger 呢？我们能不能认为由于加了 log4j.jar 包，就认为系统使用的就是 log4j 呢？事实上当然不是这样的，那我还认为我正在使用 jdk 而认为系统使用的是 sun logger 呢。使用 Spring 的朋友可以在 web.xml 中看到如下 listener 片段：
  
Xml代码
  
org.springframework.web.util.Log4jConfigListener

这是由 Spring 为我们提供的实现了标准的 servlet api 中的 javax.servlet.ServletContextListener 接口，用于在 web 容器启动时做一些初始化操作。我们逐层进入 Spring 的源码，可以看到如下代码：
  
Java代码
  
Log4jConfigurer.initLogging(location, refreshInterval);

终于找到了 org.springframework.util.Log4jConfigurer，这正是 log4j 提供给我们的初始化日志的类。至此，我们终于明白了我们系统的的确确使用的是 log4j 的日志工具。

可是问题又来了，org.apache.commons.logging.Log 和 org.apache.log4j.Logger 这两个类，通过包名我们可以发现它们都是 apache 的项目，既然如下，为何要动如此大的动作搞两个东西(指的是 commons-logging 和 log4j)出来呢？事实上，在 sun 开发 logger 前，apache 项目已经开发了功能强大的 log4j 日志工具，并向 sun 推荐将其纳入到 jdk 的一部分，可是 sun 拒绝了 apache 的提议，sun 后来自己开发了一套记录日志的工具。可是现在的开源项目都使用的是 log4j，log4j 已经成了事实上的标准，但由于又有一部分开发者在使用 sun logger，因此 apache 才推出 commons-logging，使得我们不必关注我们正在使用何种日志工具。