# 🐍 Guía Experta: Programación Orientada a Objetos en Python

## 📑 Índice
1. [Fundamentos Avanzados](#fundamentos-avanzados)
2. [Patrones de Diseño y Mejores Prácticas](#patrones-de-diseño)
3. [Metaprogramación y Decoradores](#metaprogramación)
4. [Gestión Avanzada de Memoria](#gestión-de-memoria)
5. [Protocolos y Métodos Especiales](#protocolos)
6. [Concurrencia y POO](#concurrencia)

## <a name="fundamentos-avanzados"></a>🎯 Fundamentos Avanzados

### Descriptores y Control de Atributos

Los descriptores permiten un control fino sobre el acceso a atributos:

```python
class Validador:
    """Descriptor que valida el valor de un atributo"""
    def __init__(self, minimo=None, maximo=None):
        self.minimo = minimo
        self.maximo = maximo
        self._nombre = None

    def __set_name__(self, owner, name):
        self._nombre = name

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return instance.__dict__.get(self._nombre)

    def __set__(self, instance, value):
        if self.minimo is not None and value < self.minimo:
            raise ValueError(f"{self._nombre} no puede ser menor que {self.minimo}")
        if self.maximo is not None and value > self.maximo:
            raise ValueError(f"{self._nombre} no puede ser mayor que {self.maximo}")
        instance.__dict__[self._nombre] = value

class Coche:
    velocidad = Validador(0, 300)  # Velocidad entre 0 y 300 km/h
    año = Validador(1886, 2024)    # Año entre 1886 y 2024

    def __init__(self, velocidad, año):
        self.velocidad = velocidad
        self.año = año
```

### Slots y Optimización de Memoria

```python
class CocheOptimizado:
    __slots__ = ['marca', 'modelo', 'velocidad']  # Restringe y optimiza atributos
    
    def __init__(self, marca, modelo):
        self.marca = marca
        self.modelo = modelo
        self.velocidad = 0
```

## <a name="patrones-de-diseño"></a>🏗️ Patrones de Diseño y Mejores Prácticas

### Patrón Singleton con Metaclase

```python
class Singleton(type):
    _instances = {}
    
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class ConfiguracionGlobal(metaclass=Singleton):
    def __init__(self):
        self.debug = False
        self.host = 'localhost'
        self.puerto = 8000
```

### Patrón Observer

```python
from typing import List, Protocol
from abc import ABC, abstractmethod

class Observer(Protocol):
    def actualizar(self, mensaje: str) -> None: ...

class Sujeto:
    def __init__(self):
        self._observadores: List[Observer] = []
        self._estado: str = ""

    def agregar_observador(self, observador: Observer) -> None:
        self._observadores.append(observador)

    def notificar_observadores(self) -> None:
        for observador in self._observadores:
            observador.actualizar(self._estado)

    @property
    def estado(self) -> str:
        return self._estado

    @estado.setter
    def estado(self, valor: str) -> None:
        self._estado = valor
        self.notificar_observadores()

class MonitorSistema:
    def actualizar(self, mensaje: str) -> None:
        print(f"Monitor: Nuevo estado detectado - {mensaje}")

# Uso
monitor = MonitorSistema()
sistema = Sujeto()
sistema.agregar_observador(monitor)
sistema.estado = "¡Alerta! Temperatura alta"
```

## <a name="metaprogramación"></a>🔄 Metaprogramación y Decoradores

### Decorador de Clase con Parámetros

```python
from functools import wraps
import time
from typing import Type, TypeVar, Callable

T = TypeVar('T')

def registrar_tiempo_ejecucion(umbral: float = 0.0):
    def decorador(cls: Type[T]) -> Type[T]:
        class ClaseWrapper(cls):
            def __getattribute__(self, name):
                attr = super().__getattribute__(name)
                if callable(attr):
                    @wraps(attr)
                    def wrapper(*args, **kwargs):
                        inicio = time.time()
                        resultado = attr(*args, **kwargs)
                        tiempo_total = time.time() - inicio
                        if tiempo_total > umbral:
                            print(f"¡Advertencia! {cls.__name__}.{name} "
                                  f"tardó {tiempo_total:.2f} segundos")
                        return resultado
                    return wrapper
                return attr
        return ClaseWrapper
    return decorador

@registrar_tiempo_ejecucion(umbral=1.0)
class ProcesadorDatos:
    def procesar(self, datos: list) -> list:
        time.sleep(1.5)  # Simulación de procesamiento
        return sorted(datos)
```

## <a name="gestión-de-memoria"></a>🧮 Gestión Avanzada de Memoria

### Context Managers Personalizados

```python
from typing import Optional
import contextlib

class RecursoBase:
    def __init__(self, nombre: str):
        self.nombre = nombre
        self._recurso: Optional[str] = None

    def inicializar(self) -> None:
        print(f"Inicializando recurso {self.nombre}")
        self._recurso = f"Recurso_{self.nombre}"

    def liberar(self) -> None:
        print(f"Liberando recurso {self.nombre}")
        self._recurso = None

class AdministradorRecursos:
    def __init__(self, recurso: RecursoBase):
        self.recurso = recurso

    def __enter__(self) -> RecursoBase:
        self.recurso.inicializar()
        return self.recurso

    def __exit__(self, exc_type, exc_val, exc_tb) -> None:
        self.recurso.liberar()

@contextlib.contextmanager
def administrar_recurso(nombre: str):
    recurso = RecursoBase(nombre)
    try:
        recurso.inicializar()
        yield recurso
    finally:
        recurso.liberar()

# Uso
with AdministradorRecursos(RecursoBase("DB")):
    print("Trabajando con el recurso...")

with administrar_recurso("Cache"):
    print("Trabajando con el cache...")
```

## <a name="protocolos"></a>📜 Protocolos y Métodos Especiales

### Implementación de Protocolos

```python
from typing import Iterator, Iterable, Any
from collections.abc import Sequence

class ListaCircular(Sequence):
    def __init__(self, datos: Iterable[Any]):
        self._datos = list(datos)
        
    def __len__(self) -> int:
        return len(self._datos)
    
    def __getitem__(self, indice: int) -> Any:
        return self._datos[indice % len(self)]
    
    def __iter__(self) -> Iterator[Any]:
        return iter(self._datos)
    
    def __contains__(self, item: Any) -> bool:
        return item in self._datos
    
    def __reversed__(self) -> Iterator[Any]:
        return reversed(self._datos)

# Uso
lista = ListaCircular([1, 2, 3])
print(lista[4])  # Imprime 2 (índice 4 % 3 = 1)
```

## <a name="concurrencia"></a>🔄 Concurrencia y POO

### Patrones de Concurrencia

```python
from threading import Lock
from typing import Generic, TypeVar

T = TypeVar('T')

class Compartido(Generic[T]):
    """Wrapper thread-safe para valores compartidos"""
    def __init__(self, valor: T):
        self._valor = valor
        self._lock = Lock()

    def obtener(self) -> T:
        with self._lock:
            return self._valor

    def establecer(self, nuevo_valor: T) -> None:
        with self._lock:
            self._valor = nuevo_valor

    def modificar(self, func: Callable[[T], T]) -> None:
        with self._lock:
            self._valor = func(self._valor)

# Uso
contador = Compartido(0)
contador.modificar(lambda x: x + 1)
print(contador.obtener())  # Thread-safe
```

### Mixins para Funcionalidad Thread-Safe

```python
from threading import RLock
from typing import Any

class ThreadSafeMixin:
    def __init__(self):
        self._lock = RLock()

    def ejecutar_seguro(self, func: Callable[[], Any]) -> Any:
        with self._lock:
            return func()

class CacheThreadSafe(ThreadSafeMixin):
    def __init__(self):
        super().__init__()
        self._cache = {}

    def get(self, key: str) -> Any:
        return self.ejecutar_seguro(
            lambda: self._cache.get(key)
        )

    def set(self, key: str, value: Any) -> None:
        self.ejecutar_seguro(
            lambda: self._cache.update({key: value})
        )
```

## 🎓 Conceptos Avanzados Adicionales

### Type Hints y Genéricos

```python
from typing import TypeVar, Generic, List, Optional

T = TypeVar('T')

class Contenedor(Generic[T]):
    def __init__(self, valor: Optional[T] = None):
        self._valor = valor

    def obtener(self) -> Optional[T]:
        return self._valor

    def establecer(self, valor: T) -> None:
        self._valor = valor

# Uso con type hints
contenedor_str: Contenedor[str] = Contenedor("Hola")
contenedor_int: Contenedor[int] = Contenedor(42)
```

### Propiedades Calculadas y Caching

```python
from functools import cached_property
from datetime import datetime, timedelta

class Sensor:
    def __init__(self, intervalo_lectura: timedelta):
        self.intervalo_lectura = intervalo_lectura
        self._ultima_lectura = None
        self._valor_cache = None

    @cached_property
    def lectura(self) -> float:
        """Lectura cacheada del sensor"""
        ahora = datetime.now()
        if (self._ultima_lectura is None or 
            ahora - self._ultima_lectura > self.intervalo_lectura):
            self._ultima_lectura = ahora
            self._valor_cache = self._leer_sensor()
        return self._valor_cache

    def _leer_sensor(self) -> float:
        # Simulación de lectura de sensor
        return 42.0
```

## 📚 Recursos y Referencias Avanzadas

- [Python Data Model](https://docs.python.org/3/reference/datamodel.html)
- [Python Type Hints](https://docs.python.org/3/library/typing.html)
- [Python Design Patterns](https://python-patterns.guide/)
- [Python Descriptors](https://docs.python.org/3/howto/descriptor.html)
- [Python Metaclasses](https://docs.python.org/3/reference/datamodel.html#metaclasses)

## 🎯 Ejercicios de Nivel Experto

1. Implementa un sistema de caché thread-safe con expiración de elementos
2. Crea un ORM simple usando metaclases y descriptores
3. Desarrolla un sistema de plugins usando importación dinámica
4. Implementa un pool de conexiones con límite de recursos
5. Crea un decorador que implemente retry con backoff exponencial

