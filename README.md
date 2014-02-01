# 1. Introduction
---
With advancements in HTML, CSS, JavaScript, web browser capabilities and Internet speeds,
more and more enterprise functionality is being migrated to the web.  This has led to more
and more enterprise data being made available to users over the web and therefore increased
need to enable rich reporting capabilities in web applications.

[Spring MVC](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-web)
is a popular Java framework for building web applications.  It runs well across Java application
servers and provides tight integration with loads of popular Java tools such as source code
editors (Eclipse, IDEA, Netbeans), build managers (Maven, ANT, Ivy, Gradle) and usually works
with most languages that work on the Java (for example, Scala and Groovy).

[Jasper Reports](http://community.jaspersoft.com/) is a Java based reporting library that
supports a fairly comprehensive set of reporting features, a visual report design tool, a
flexible reporting generation framework that allows development teams to choose the right
balance between design-time and runtime flexibility, support for multiple reporting formats
such as *HTML*, *XHTML*, *PDF*, *Microsoft Excel 97 (.xls)*, *Microsoft Excel 2000 (.xlsx)*,
*CSV*,*Rich Text*, *Open Data Format (.odf)* and more.

This sample application demonstrates one possible way to add Jasper Reports to a Spring MVC
application.  Other integration possibilities exist (see the last section below) but this
application demonstrates only one possible approach (described below).

# 2. Overview
---
This sample application contains two types of reports:

1. Tabular
1. Chart/Graph

### 2.1. Context
---
The sample application has been developed for a fictitious company called Coffee Inc. a
high-street shop that sells coffee and related items.

### 2.2. Tabular report - Order History
---
This report shows all orders received by Coffee Inc.  It contains the following table:

<table>
  <thead>
    <tr>
      <th>S. No.</th>
      <th>Order Date</th>
      <th>Customer Name</th>
      <th>Order Total</th>
    </tr>
  </thead>
</table>

### 2.3. Chart report - Order Summary
---
This report shows a summary of all orders received by Coffee Inc., grouped by month.
For every month, it shows the total amount received by Coffee Inc.

# 3. Running the application
---
The following pre-requisites are required to run this application:

1. JDK 6.0+
2. Apache Maven 3.0.4+

Once these are installed and the application code has been checked out or downloaded
from Git, the application can be run by issuing the following command:

    mvn clean package tomcat7:run

This will compile the application (after downloading all necessary libraries) and run
an embedded Tomcat instance on port 8080.  The application can then be accessed on
<http://localhost:8080> using any web browser.

The reports can be accessed from the navigation menu on the left-hand-side of the page.
Each report can be accessed in *HTML*, *PDF* and *Microsoft Excel '97* formats.

# 4. Report files
---
The report designs can be found in files under the folder
`src/main/resources/org/example/report/order/history.jrxml`.

# 5. Explanation of the code
---
This application uses *Scala*, a programming language that runs on the JVM.  Scala
was primarily chosen due to its brevity that allows relatively small prototypes like
this application to be developed quickly.  It reduces the amount of repetitive code
typically required to write a Spring MVC based web application.

The application is a regular Spring MVC, with the following important additions:

1. A helper class for generating a report from a report template and report data;
1. A helper servlet for displaying charts and graphics in an HTML report;
1. `web.xml` configuration for the helper servlet.

The reports are generated by Spring MVC *Controller* classes that can be found under
`src/main/scala/org/example/web/controller/report`.  Please refer to Spring MVC
documentation in case you are new to MVC and need more explanation on what a
*Controller* class is.

The Controller classes in turn call a class called `JasperReportGenerator` that can
be found under `src/main/scala/org/example/web/report/jasper`.  This class is responsible
for generating reports in the required format (*HTML*, *PDF* or *Microsoft Excel*) from
a report template and report data.  It has been written in a Java-like manner to
make it easy for readers to understand the flow and get familiar with Jasper Reports
API.

Controller classes pass data to `JasperReportGenerator` as a `java.util.Collection`
of objects, which is then passed on to the Jasper Reports API to be merged with the
report template.  This strategy allows the reports to be completely decoupled from
the source of the data used to generate them.  Data can be pulled out of a relational
database, local files, external systems, email systems, and so on and be passed on to
the reports in a consistent and reliable way, without the reports having to manage
the connectivity with the data source(s).

There is also a small bit of configuration in the `web.xml` file for the application:

    <web-app>
      <servlet>
        <servlet-name>jasper-report-image</servlet-name>
        <servlet-class>org.example.web.servlet.jasper.GraphicsServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
      </servlet>
      <servlet-mapping>
        <servlet-name>jasper-report-image</servlet-name>
        <url-pattern>/report/graphics</url-pattern>
      </servlet-mapping>
    </web-app>

This part of the configuration maps the URL `/report/graphics` to the servlet
`org.example.web.servlet.jasper.GraphicsServlet`.  This servlet is responsible for
adding charts and graphics to HTML reports.  Internally, it uses the data passed to
a report to generate images for the charts embedded inside a report.
