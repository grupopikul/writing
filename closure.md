# **Alcance (Scope)**
En Python existe una regla llamada LEGB, por sus siglas: Local, Enclosing (Envolvente), Global y Built-in (Integrado).
### **Ejemplo de Alcance**
```python
x = "global"
def local():
    z = "local"
    print(f"Imprimir dentro de un alcance {z}")

def envolvente():
    y = "envolvente"
    def interno():
        print(f"Imprimir dentro de un alcance {y} o nonlocal")

print(f"Imprimir dentro de un alcance {x}")
local()
envolvente()
```
**Resultado:**
```
Imprimir dentro de un alcance global
Imprimir dentro de un alcance local
Imprimir dentro de un alcance envolvente o nonlocal
```

Sobre los Integrados, son palabras reservadas del sistema, tales como: `True`, `False`, `None`, `and`, `as`, `assert`, `break`, `class`, `contine`, etc.

# **Closure en Python**

## **¿Qué es un Closure?**
Un **closure** en Python es una función que "recuerda" el entorno o contexto en el que fue creada, para esto accede a un alcance (scope) superior. El programador puede modificar una variable accediendo directamente a su espacio en memoria con un **nonlocal** o puede solo hacer una lectura del valor de la variable, sin tener que usar esa declaración.

### **Ejemplo 1 de Closure**
En este ejemplo, la función interna `agregar_cadena()` modifica la variable `cadena_sin_espacios` empleando **nonlocal** , así el scope de esa variable es de `agregar_texto()`, sumando un `" "` y el valor de `texto`:

```python
x = "Hola "

def agrega_texto(cadena, texto):
    cadena_sin_espacios = cadena[:-1]
    print(f"Texto antes del closure dentro, en el scope de agrega_texto(): {cadena_sin_espacios}")

    def agregar_cadena():
        nonlocal cadena_sin_espacios  # Usamos 'nonlocal' para modificar la variable 'cadena_sin_espacios'
        cadena_sin_espacios = cadena_sin_espacios + " " + texto
    agregar_cadena()

    return cadena_sin_espacios


cadena = agrega_texto(x, "Mundo!")
print(f"Texto después del closure: {cadena}")
```

**Resultado:**
```
Texto antes del closure dentro, en el scope de agrega_texto(): Hola
Texto después del closure: Hola Mundo!
```

### **Ejemplo 2 de Closure**
En este ejemplo, la función interna `agregar_cadena()` utiliza la variable `cadena_sin_espacios` y `texto` ya que son solo de lectura:

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
Al ser Python un lenguaje que maneja POO, posee algunas clases nativas las cuales tienen métodos que modifican el valor interno de la variable, pero no accesan a su dirección en memoria (por eso no necesiatan declarar `nonlocal` ni `global`), por esto no necesitan emplear **nonlocal** para modificar una variable, como por ejemplo los métodos `append()` y `extend()` que son nativos de las listas de Pyhton.

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
