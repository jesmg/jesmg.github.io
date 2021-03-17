---
layout: post
title: "Gestionando datos delicados en Java"
comments: true
categories: artículos
---
Cuando se desarrolla una aplicación que contiene **datos sensibles** (o, mejor dicho, delicados), el programador debe preguntarse si el entorno de ejecución es lo bastante seguro. La información a proteger, y la zona de memoria que ésta ocupa, se encontrarán a merced del nivel de aislamiento y del resto de aplicaciones ejecutándose en el sistema.

[Según OWASP][1], la exposición de datos delicados es la sexta vulnerabilidad más frecuente. 

A día de hoy y por regla general, no se duda de la necesidad de transmitir la información delicada a través de HTTPs. Sin embargo, **es fácil olvidarse de los otros dos lugares donde puede ser expuesta**: El servidor y el cliente. Especialmente al utilizar lenguajes administrados, donde el programador se despreocupa de la gestión de la memoria.

En el caso de Java, existen caractersticas que facilitan la solución del problema. En concreto, **las cadenas mutables y los objetos sellados**:

+ El tipo String es un objeto inmutable. Es decir, al modificar el valor se crea una nueva copia del objeto en memoria, y la anterior persiste hasta que el recolector de basura decide llevársela. 

   Por ejemplo: Se crea una variable de tipo String, la cual almacena una contraseña válida, y posteriormente se reemplaza por un valor nulo. Un malware hace un volcado de memoria en ese momento, y manda los resultados para ser examinados. El usuario malicioso podría ver tanto el valor nulo como la contraseña, ocupando distintas posiciones.
 
   **Un [StringBuffer][2] es una cadena de caracteres mutable**. Citando la documentación oficial, es cómo un String pero puede ser modificado. Es decir, los cambios afectan directamente a la zona de memoria reservada para esa variable. Esto otorga control total sobre la información almacenada, que puede sobrescribirse con datos no significativos (por ejemplo, una cadena de ceros) hasta que el recolector decida eliminarla.
   
   **También se podría utilizar el objeto [StringBuilder][3]**, cuyo funcionamiento es similar al del StringBuffer. Si bien las operaciones con el StringBuilder son más rápidas, no es apto para trabajar con múltiples hilos, al contrario que el StringBuffer.

###### Ejemplo: Crear y eliminar un StringBuffer

```java
//Crear nuevo StringBuffer
StringBuffer password = new StringBuffer();
password.append("contraseña del usuario");

//Eliminar el string buffer
password.delete(0, password.length());
```

+ Los [SealedObject][4] son una característica de javax.crypto, la librería de Java para operaciones criptográficas. Se crean pasándoles otro objeto como parámetro. **Una copia del objeto original se almacenará cifrada dentro del SealedObject**. 

   Se puede encapsular cualquier objeto dentro del SealedObject, bajo la condición de que sea serializable. Además, el desarrollador puede indicar el cifrado que desea utilizar, entre todos los disponibles en la librería.

###### Ejemplo: Cifrar en un SealedObject   
```java
//Crear un cifrador (AES con clave de 16 bytes)
Cipher cifrador = Cipher.getInstance("AES/CBC/PKCS5Padding");
SecureRandom random = new SecureRandom();
byte[] clave = new byte[16];
random.nextBytes(clave);
SecretKeySpec clavespec = new SecretKeySpec (clave, "AES");
cifrador.init(Cipher.ENCRYPT_MODE, clavespec);

//Crear un SealedObject y almacenar el StringBuffer usando el cifrador
SealedObject passwordCifrada = new SealedObject(password, cifrador);
```

###### Ejemplo: Descifrar el SealedObject
```java
//Recuperar la contraseña del SealedObject usando la clave del cifrador
autenticarUsuario(passwordCifrada.getObject(clavespec));
```
   
Las dos características que se han visto permiten gestionar la información delicada de manera sencilla y segura. En un posible caso de uso final, se almacenarían los datos en un StringBuffer, encapsulado en un SealedObject. A la hora de utilizar dichos datos en una operación, se descifraría el SealedObject obteniendo el StringBuffer original. Por último, se sobrescribiría el valor del StringBuffer para eliminarlo.

###### Ejemplo: Código final
```java
import java.io.IOException;
import java.security.*;

import javax.crypto.*;
import javax.crypto.spec.SecretKeySpec;

public class DatosDelicados {

    public static void main(String[] args) throws NoSuchAlgorithmException, NoSuchPaddingException, IllegalBlockSizeException, IOException, ClassNotFoundException, BadPaddingException, InvalidKeyException {

        //Crear nuevo StringBuffer
        StringBuffer password = new StringBuffer();
        password.append("contraseña del usuario");

        //Crear un cifrador (AES CBC con clave de 16 bytes)
        Cipher cifrador = Cipher.getInstance("AES/CBC/PKCS5Padding");
        SecureRandom random = new SecureRandom();
        byte[] clave = new byte[16];
        random.nextBytes(clave);
        SecretKeySpec clavespec = new SecretKeySpec (clave, "AES");
        cifrador.init(Cipher.ENCRYPT_MODE, clavespec);

        //Almacenar el StringBuffer en el SealedObject usando el cifrador
        SealedObject passwordCifrada = new SealedObject(password, cifrador);

        //Eliminar el string buffer
        password.delete(0, password.length());

        //Recuperar la contraseña del SealedObject usando la clave del cifrador
        autenticarUsuario(passwordCifrada.getObject(clavespec));
    }

    private static void autenticarUsuario(Object object) {
        System.out.println(object);
    }

}
```

[1]: https://www.owasp.org/index.php/Top_10_2013-A6-Sensitive_Data_Exposure
[2]: https://docs.oracle.com/javase/7/docs/api/java/lang/StringBuffer.html
[3]: https://docs.oracle.com/javase/7/docs/api/java/lang/StringBuilder.html
[4]: https://docs.oracle.com/javase/7/docs/api/javax/crypto/SealedObject.html
