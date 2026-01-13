# Spring MVC

Spring MVC (Model-View-Controller) is a framework for building web applications in Java.

It follows the MVC pattern, which separates concerns:
- Model: Holds the application data (Java objects, often entities or DTOs).
- View: Handles how data is presented to the user (HTML pages, templates).
- Controller: Handles user requests, interacts with the model, and returns a view.

Spring MVC makes it easy to map HTTP requests to controller methods using annotations like @Controller and @RequestMapping.

# Thymeleaf

Thymeleaf is a server-side Java template engine for rendering HTML pages. It integrates very well with Spring Boot and allows you to:

Render dynamic data (like user.name) into HTML.

Use loops (th:each) and conditionals (th:if/th:unless) in HTML.

Generate forms that bind directly to your Java objects.

## Coding Example

- Browser requests /hello.
- Spring MVC calls DemoController.sayHello().
- The controller adds current date/time to the model (theDate).
- Controller returns the view helloworld.html.
- Thymeleaf replaces ${theDate} in HTML with the actual server time.
- CSS styling is applied (.funny → green italic text).
- Browser sees a dynamic, styled message

```java

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import java.time.LocalDateTime;

@Controller
public class DemoController {
    @GetMapping("/hello")
    public String sayHello(Model theModel){
        theModel.addAttribute("theDate", LocalDateTime.now());
        return "helloworld";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf Demo</title>
    <link rel="stylesheet" th:href="@{/css/demo.css}" />
</head>
    <body>
        <p th:text="'Time on the server is ' + ${theDate}" class="funny"></p>
    </body>
</html>
```

```css
.funny {
    font-style: italic;
    color: green;
}
```
## How it works

- DispatcherServlet = Front Controller. Receives all requests first.
- HandlerMapping → Finds which controller method handles request.
- Controller → Prepares Model data and returns logical view name.
- ViewResolver + Thymeleaf → Converts view name + model into rendered HTML.
- Browser → Displays HTML.

Key point: DispatcherServlet centralizes request handling; controllers don’t deal with HTTP directly, just with business logic and data.

# Form and Process Form

```java
@Controller
public class HelloWorldController {

    @RequestMapping("/showForm")
    public String showForm(){
        return "helloworld-form";
    }

    @RequestMapping("/processForm")
    public String processForm(){
        return "helloworld-form-process";
    }
}

```

```html
    <body>
    <form th:action="@{/processForm}" method="GET">
        <input type="text" name="studentName" placeholder="What's your name?" />
        <input type="submit"/>
    </form>
    </body>
```

```html
    <body>
        Hello World of Spring! <br>
        Student name: <span th:text="${param.studentName}"></span>
    </body>
```
