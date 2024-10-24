
# 🐍 Explicación Avanzada: Clases y Objetos en Python

## 📖 ¿Qué es una Clase?

Una **clase** es una estructura que define un conjunto de atributos y métodos que luego pueden ser utilizados para crear objetos. En Python, todo es un objeto, y las clases permiten crear nuevos tipos de objetos personalizados.

### Propiedades y Métodos

- **Atributos de instancia**: Son atributos que pertenecen a una instancia (objeto) específica de una clase.
- **Atributos de clase**: Son atributos que pertenecen a la clase y son compartidos por todas las instancias.
- **Métodos**: Son funciones definidas dentro de la clase que operan sobre los objetos.

```python
class Coche:
    ruedas = 4  # Atributo de clase (compartido por todas las instancias)

    def __init__(self, marca, modelo):
        self.marca = marca  # Atributo de instancia
        self.modelo = modelo  # Atributo de instancia

    def acelerar(self):
        print(f"El {self.marca} {self.modelo} está acelerando.")

    @classmethod
    def cambiar_ruedas(cls, cantidad):
        cls.ruedas = cantidad  # Modifica el atributo de clase

    @staticmethod
    def info_coche():
        print("Los coches son un medio de transporte con ruedas.")
```

- `ruedas` es un **atributo de clase**.
- `marca` y `modelo` son **atributos de instancia**.
- `acelerar` es un **método de instancia** (requiere una instancia para ser usado).
- `cambiar_ruedas` es un **método de clase** que afecta a la clase en sí, no a las instancias.
- `info_coche` es un **método estático**, no necesita ni instancia ni clase para ser llamado.

---

## 🚗 Instanciación de Objetos

Cuando instanciamos una clase, creamos un objeto (instancia) de esa clase.

```python
mi_coche = Coche("Toyota", "Corolla")
mi_coche.acelerar()  # El Toyota Corolla está acelerando.

Coche.cambiar_ruedas(6)  # Cambiamos el atributo de clase 'ruedas' a 6 para todas las instancias.
print(f"El coche tiene {mi_coche.ruedas} ruedas.")  # El coche tiene 6 ruedas.
```

---

## 🔄 Herencia

La **herencia** permite crear nuevas clases basadas en clases existentes. La clase hija hereda los atributos y métodos de la clase padre, pero también puede tener sus propios atributos y métodos, o sobrescribir los heredados.

```python
class Deportivo(Coche):
    def __init__(self, marca, modelo, velocidad_max):
        super().__init__(marca, modelo)
        self.velocidad_max = velocidad_max  # Atributo específico de la clase hija

    def acelerar(self):
        print(f"El {self.marca} {self.modelo} acelera hasta {self.velocidad_max} km/h.")
```

- `Deportivo` hereda de `Coche` y extiende su funcionalidad.
- `super().__init__(marca, modelo)` llama al constructor de la clase padre.

---

## 🌐 Encapsulamiento

El **encapsulamiento** es la ocultación de detalles internos de una clase. En Python, se utiliza el guión bajo (`_`) para señalar que un atributo o método es privado (aunque no hay una privacidad real estricta).

```python
class CocheSeguro:
    def __init__(self, marca, modelo):
        self._marca = marca  # Atributo "protegido"
        self.__modelo = modelo  # Atributo "privado"

    def obtener_modelo(self):
        return self.__modelo  # Método público para acceder al atributo privado
```

- `_marca` es una **convención** para un atributo protegido.
- `__modelo` está "privado" usando la convención de doble guión bajo.

---

## 🔄 Polimorfismo

El **polimorfismo** se refiere a la capacidad de diferentes clases de usar un método del mismo nombre, pero implementarlo de formas distintas.

```python
class Vehiculo:
    def moverse(self):
        print("El vehículo se mueve.")

class Barco(Vehiculo):
    def moverse(self):
        print("El barco navega.")

class Avion(Vehiculo):
    def moverse(self):
        print("El avión vuela.")

# Uso de polimorfismo
vehiculos = [Barco(), Avion(), Vehiculo()]

for vehiculo in vehiculos:
    vehiculo.moverse()
```

---

## ⚡ Métodos Especiales

En Python, las clases pueden tener **métodos especiales** que empiezan y terminan con doble guión bajo (`__`). Estos permiten modificar el comportamiento de operadores y otras funciones.

- `__str__(self)` se utiliza para definir cómo se representa una instancia como cadena.
- `__eq__(self, otro)` permite la comparación con el operador `==`.

```python
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.edad = edad

    def __str__(self):
        return f"{self.nombre}, {self.edad} años."

    def __eq__(self, otra_persona):
        return self.nombre == otra_persona.nombre and self.edad == otra_persona.edad

p1 = Persona("Juan", 30)
p2 = Persona("Juan", 30)
print(str(p1))  # Juan, 30 años.
print(p1 == p2)  # True (compara nombre y edad)
```

---

## 🎯 Resumen Avanzado

- Las **clases** en Python pueden tener atributos y métodos de instancia, clase y estáticos.
- **Herencia** permite reutilizar y extender funcionalidad.
- **Encapsulamiento** oculta detalles internos, aunque en Python es más una convención que una regla estricta.
- **Polimorfismo** permite que diferentes clases usen un método del mismo nombre pero con comportamiento distinto.
- Los **métodos especiales** permiten personalizar operadores y funciones clave en Python.
