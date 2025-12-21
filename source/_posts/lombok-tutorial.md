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
    <version>1.18.42</version>
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

```java
package demo;



public class Person {


    private String name;
    private int age;
    private String address;
    private String phoneNumber;
    private String email;
    private String occupation;

    public Person(){

    }
    public Person(String name, int age, String address, String phoneNumber, String email, String occupation) {
        this.name = name;
        this.age = age;
        this.address = address;
        this.phoneNumber = phoneNumber;
        this.email = email;
        this.occupation = occupation;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", email='" + email + '\'' +
                ", occupation='" + occupation + '\'' +
                '}';
    }



    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String getAddress() {
        return address;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public String getEmail() {
        return email;
    }

    public String getOccupation() {
        return occupation;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public void setOccupation(String occupation) {
        this.occupation = occupation;
    }
}

```

```
package demo;


import lombok.*;
@ToString
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Getter
@Setter
public class Person_lombok {

    private String name;
    private int age;
    private String address;
    private String phoneNumber;
    private String email;
    private String occupation;
}

```

```
package demo;


import org.junit.Test;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertTrue;

public class PersonComparisonTest {

    @Test
    public void compareName() {
        Person p = new Person();
        Person_lombok pl = new Person_lombok();

        p.setName("Liu");
        pl.setName("Liu");

        assertEquals(p.getName(), pl.getName());
    }

    @Test
    public void compareAge() {
        Person p = new Person();
        Person_lombok pl = new Person_lombok();

        p.setAge(25);
        pl.setAge(25);

        assertEquals(p.getAge(), pl.getAge());
    }

    @Test
    public void compareAddress() {
        Person p = new Person();
        Person_lombok pl = new Person_lombok();

        p.setAddress("Alicante");
        pl.setAddress("Alicante");

        assertEquals(p.getAddress(), pl.getAddress());
    }

    @Test
    public void comparePhoneNumber() {
        Person p = new Person();
        Person_lombok pl = new Person_lombok();

        p.setPhoneNumber("123456");
        pl.setPhoneNumber("123456");

        assertEquals(p.getPhoneNumber(), pl.getPhoneNumber());
    }

    @Test
    public void compareEmail() {
        Person p = new Person();
        Person_lombok pl = new Person_lombok();

        p.setEmail("test@test.com");
        pl.setEmail("test@test.com");

        assertEquals(p.getEmail(), pl.getEmail());
    }

    @Test
    public void compareOccupation() {
        Person p = new Person();
        Person_lombok pl = new Person_lombok();

        p.setOccupation("Engineer");
        pl.setOccupation("Engineer");

        assertEquals(p.getOccupation(), pl.getOccupation());
    }

    @Test
    public void compareFullConstructor() {

        Person p = new Person("Liu", 23, "Alicante", "999", "li@test.com", "Engineer");

        Person_lombok pl = Person_lombok.builder()
                .name("Liu").age(23).address("Alicante")
                .phoneNumber("999").email("li@test.com").occupation("Engineer")
                .build();

        assertEquals(p.getName(), pl.getName());
        assertEquals(p.getAge(), pl.getAge());
        assertEquals(p.getOccupation(), pl.getOccupation());
    }

    @Test
    public void compareToStringBehavior() {
        String name = "liu";
        Person p = new Person();
        p.setName(name);

        Person_lombok pl = new Person_lombok();
        pl.setName(name);

        assertTrue(p.toString().contains(name));
        assertTrue(pl.toString().contains(name));
    }
}

```
