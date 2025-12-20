---
uuid: 6694df00-dd50-11f0-964a-1f59d40845e3
title: lombok_tutorial
date: 2025-12-20 04:03:03
tags:
---

# Lombok tutorial 

## Introducción

Lombok es una biblioteca de Java que ayuda a reducir el código repetitivo mediante anotaciones. Permite generar automáticamente métodos comunes como getters, setters, constructores, y más, lo que facilita la escritura y el mantenimiento del código.

## Configuración del proyecto

Para usar Lombok en tu proyecto, primero debes agregar la dependencia de Lombok en tu archivo `pom.xml` si estás utilizando Maven:

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <scope>provided</scope>
</dependency>
```


Una vez agregado, asegúrate de que tu IDE esté configurado para reconocer las anotaciones de Lombok. Por ejemplo, en IntelliJ IDEA, puedes instalar el plugin de Lombok desde el repositorio de plugins.

## Uso de Lombok

Aquí hay algunos ejemplos comunes de cómo usar Lombok:

### 1. @Getter y @Setter
```java
import lombok.Getter;
import lombok.Setter;
@Getter
@Setter
public class Persona {
    private String nombre;
    private int edad;
}
```

Con las anotaciones `@Getter` y `@Setter`, Lombok generará automáticamente los métodos getter y setter para los campos `nombre` y `edad`. Hay que tener en cuenta que estas anotaciones pueden aplicarse a nivel de clase para generar getters y setters para todos los campos, o a nivel de atributo para generar solo para atributo específicos.


### 2. @ToString
```java
import lombok.ToString;
@Getter
@Setter
@ToString
public class Person {
    private String name;
    private int age;
}
```

La anotación `@ToString` genera automáticamente un método `toString()` que incluye todos los campos de la clase, facilitando la depuración y el registro de información.

### 3. @NoArgsConstructor y @AllArgsConstructor
```java
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
@Getter
@Setter
@ToString
@AllArgsConstructor
@NoArgsConstructor
public class Person {
    private String name;
    private int age;
}
```
Las anotaciones `@NoArgsConstructor` y `@AllArgsConstructor` generan constructores sin argumentos y con todos los argumentos, respectivamente, lo que simplifica la creación de instancias de la clase. 

Nota impoertante: si usas `@AllArgsConstructor`, Lombok no generará el constructor por defecto a menos que también uses `@NoArgsConstructor`. 

### 4. @Data
```java
import lombok.Data;
@Data // Equivalente a @Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor
public class Persona {
    private String nombre;
    private int edad;
}
``` 

Para la anotación `@Data`, Lombok generará automáticamente los métodos @Data = @Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor, lo que la hace muy útil para clases de datos simples.


### 5. @Builder
```java
import lombok.Builder;
@Builder
public class Persona {
    private String nombre;
    private int edad;
}
```
La anotación `@Builder` permite crear objetos de manera más legible y flexible utilizando el patrón de diseño Builder.

## Conclusión
Lombok es una herramienta poderosa que puede simplificar significativamente el código Java al reducir la cantidad de código repetitivo. Al utilizar las anotaciones proporcionadas por Lombok, los desarrolladores pueden centrarse más en la lógica de negocio y menos en la escritura de código boilerplate. Asegúrate de explorar más anotaciones y características que Lombok ofrece para aprovechar al máximo esta biblioteca.

## Referencias
- [Documentación oficial de Lombok](https://projectlombok.org/)
- [Guía de uso de Lombok](https://www.baeldung.com/intro-to-project-lombok)
- [Lombok en GitHub](https://github.com/projectlombok/lombok)


## Pruebas de código
```
package demo;


import org.junit.Test;

import static org.junit.Assert.assertEquals;

public class PersonTest {
    @Test
    public void personGetterSetterTest() {
        Person person = new Person();
        person.setName("Liu");
        person.setAge(23);
        assertEquals("Liu", person.getName());
        assertEquals(23, person.getAge());
    }

    @Test
    public void personDataTest() {
        Person person1 = new Person();
        person1.setName("Liu");
        person1.setAge(23);

        Person person2 = new Person();
        person2.setName("Liu");
        person2.setAge(23);

        assertEquals(person1.getName(), person2.getName());
        assertEquals(person1.getAge(), person2.getAge());
    }

    @Test
    public void personToStringTest() {
        Person person = new Person();
        person.setName("Liu");
        person.setAge(23);
        String expectedToString = "Person(name=Liu, age=23)";
        assertEquals(expectedToString, person.toString());
    }

    @Test
    public void personAllArgsConstructorTest() {
        Person person = new Person("Liu", 23);
        String expectedToString = "Person(name=Liu, age=23)";
        assertEquals("Liu", person.getName());
        assertEquals(23, person.getAge());
        assertEquals(expectedToString, person.toString());
    }
    @Test
    public void personBuilderTest() {
        Person person = Person.builder()
                .name("Liu")
                .age(23)
                .build();
        String expectedToString = "Person(name=Liu, age=23)";
        assertEquals("Liu", person.getName());
        assertEquals(23, person.getAge());
        assertEquals(expectedToString, person.toString());
    }
}
```
