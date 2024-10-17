
# **Closure en Python**

## **¿Qué es un Closure?**
Un **closure** en Python es una función que "recuerda" el entorno o contexto en el que fue creada, para esto accede a un alcance (scope) superior. Para entender closures, es importante conocer los tipos de **alcance** (scope) en Python:
- **Global**: Variables accesibles en todo el script.
- **Nonlocal**: Variables que se encuentran en un scope superior, pero no son globales.
- **Local**: Variables accesibles solo dentro de una función.

Los closures en Python usan variables con un alcance **nonlocal**, lo que permite que una función interna acceda a variables definidas en una función externa.

### **Ejemplo 1 de Closure**
En este ejemplo, la función interna `sumatoria()` utiliza la variable `suma` que tiene alcance **nonlocal**, accediendo al scope de suma en `sumatoria_de_lista()`:

```python
lista_numeros = [1, 2, 3, 4]

def sumatoria_de_lista(lista):
    suma = 0
    print(f"Suma antes del closure dentro, en el scope de sumatoria_de_lista(): {suma}")
    
    def sumatoria():
        nonlocal suma  # Usamos 'nonlocal' para modificar la variable 'suma' del scope superior de 'sumatoria de lista()'
        for numero in lista:
            suma += numero
        return suma
    
    return sumatoria()

resultado_sum = sumatoria_de_lista(lista_numeros)
print(f"Esta es mi sumatoria de la lista de números: {resultado_sum}")
```

**Resultado:**
```
Suma antes del closure dentro, en el scope de sumatoria_de_lista(): 0
Esta es mi sumatoria de la lista de números: 10
```

### **Ejemplo 2 de Closure**
En este ejemplo, la función interna `agregar_cadena()` utiliza la variable `cadena_sin_espacios` y `texto` que tienen alcance **nonlocal** aunque no se declare, ya que son solo de lectura, así el scope de esas esas variables es de `agregar_texto()`:

```python
x = "Hola "

def agrega_texto(cadena, texto):
    cadena_sin_espacios = cadena[:-1]
    def agregar_cadena():  
        # No usamos 'nonlocal' porque no se modifican las variables, solo se accesa a su valor
        return cadena_sin_espacios + " "+ texto + "!"
    return agregar_cadena()

cadena = agrega_texto(x, "Mundo")
print(cadena)
```
**Resultado:**
```
Hola Mundo!
```

---

## **Excepciones que no requieren `nonlocal`**
Al ser Python un lenguaje que maneja POO, posee algunas clases nativas las cuales tienen métodos que modifican el valor interno de la variable, pero no accesan a su dirección en memoria (por eso no necesiatan declarar `nonlocal` ni `global`), como por ejemplo los métodos `append()` y `extend()` que son nativos de las listas de Pyhton.

### **Ejemplo 1: Usando `append()`**
En este ejemplo, `append()` modifica la lista sin necesidad de declarar `nonlocal`:

```python
def agregar_e_imprimir_lista(numero):
    lista_numeros = [1, 2, 3, 4]  # Lista definida dentro de la función
    
    def agregar_numero(numero):
        lista_numeros.append(numero)  # 'append()' no requiere 'nonlocal' para modificar 'lista_numeros'
        print(f"Se ha agregado el {numero} a la lista")
        return lista_numeros
    
    return agregar_numero(numero)

lista_actualizada = agregar_e_imprimir_lista(5)
print(f"Tu lista es: {lista_actualizada}")
```

**Resultado:**
```
Se ha agregado el 5 a la lista
Tu lista es: [1, 2, 3, 4, 5]
```

### **Ejemplo 2: Usando `extend()`**
En este caso, `extend()` también modifica la lista sin necesidad de `nonlocal`:

```python
def extender_e_imprimir_lista(numeros):
    lista_numeros = [1, 2, 3, 4]  # Lista definida dentro de la función
    
    def extender_lista(numeros):
        lista_numeros.extend(numeros)  # 'extend()' no requiere 'nonlocal'
        print(f"Se han agregado los números {numeros} a la lista")
        return lista_numeros
    
    return extender_lista(numeros)

lista_extendida = extender_e_imprimir_lista([5, 6, 7, 8, 9, 10])
print(f"Tu lista es: {lista_extendida}")
```

**Resultado:**
```
Se han agregado los números [5, 6, 7, 8, 9, 10] a la lista
Tu lista es: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```
