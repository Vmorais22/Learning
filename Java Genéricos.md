# Introducción

Los tipos genéricos nos permiten reusar el mismo código para distintos tipos de objetos.
La idea básica es que el genérico permite establecer un tipo parametrizado de manera que el tipo de dato que se está tratando sea un
parámetro para métodos, clases e interfaces. Esto permite crear clases que funcionen con diferentes tipos de datos, en vez de tener que
crear una para cada uno.

```
class Test<T> {
  T obj;
  Test(T obj) {this.obj = obj; } // constructor
  public T getbject() { return this.obj; }
}

class Main {
  public static void main (String[] args) {
    //instance of Integer type
    Test<Integer> iObj = new Test<Integer>(15);
    System.out.pritln(iObj.getObject());

    // instance of String type
    Test <String> sObj = new Test<String>("GeeksForGeeks);
    System.out.println(sObj.getObject());
    }
}
```

Un método genérico de ejemplo:

````
static <T> void genericDisplay (T element)
{
  System.out.println(element.getClass().getName() + " = " + element);
}
````
