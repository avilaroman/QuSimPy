## MapaTuc WebView App (Android)

Aplicación Android nativa (Kotlin) que carga `https://mapatuc.pages.dev` dentro de un WebView con soporte de JavaScript, DOM Storage y aceleración por hardware.

### Compilación rápida (APK debug)

1. Abre el proyecto en Android Studio (Giraffe o superior) y deja que sincronice Gradle.
2. Selecciona un dispositivo/emulador y ejecuta "Run" para `app` (build `debug`).
   - APK de debug quedará en `app/build/outputs/apk/debug/`.
   - Alternativa CLI: instala Gradle 8.x y JDK 17 y ejecuta:
     ```bash
     gradle assembleDebug
     ```

### Firma y Play Store (AAB recomendado)

1. Genera un keystore (una sola vez):

```bash
keytool -genkeypair -v -keystore keystore.jks -alias mapatuc -keyalg RSA -keysize 2048 -validity 36500
```

2. Crea `keystore.properties` en la raíz del proyecto con:

```properties
storeFile=keystore.jks
storePassword=TU_PASSWORD
keyAlias=mapatuc
keyPassword=TU_PASSWORD
```

3. Construye artefactos:

```bash
# AAB para Play Store
gradle bundleRelease

# APK release (opcional)
gradle assembleRelease
```

4. Sube el `.aab` generado en `app/build/outputs/bundle/release/` a Google Play Console.

### Permisos extra (opcional)

Si el sitio solicita geolocalización/cámara/micrófono, agrega permisos y maneja prompts:

```xml
<!-- AndroidManifest.xml -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.CAMERA" />
```

Y maneja los permisos en `MainActivity` según sea necesario.

### Notas

- Requiere conexión HTTPS (ya provista por `mapatuc.pages.dev`).
- Back físico navega al historial del WebView.
- Para permisos extra (p. ej. geolocalización), se pueden añadir en `AndroidManifest.xml` y manejar en `MainActivity`.

# QuSim.py

[![Estado de la compilación](https://travis-ci.org/adamisntdead/QuSimPy.svg?branch=master)](https://travis-ci.org/adamisntdead/QuSimPy)

Qusim.py es un simulador de computadora cuántica multi-cúbit de juguete, escrito en 150 líneas de Python.

Este código te permite ver fácilmente cómo funciona una computadora cuántica siguiendo el álgebra lineal.

```python
from QuSim import QuantumRegister

################################################
# Introducción #
################################################
# Aquí te presentamos algunos ejemplos de diferentes estados/algoritmos cuánticos para que puedas
# familiarizarte con el funcionamiento del módulo y
# algunas ideas algorítmicas

###################################################
# Medición cuántica #
###############################################
# Este experimento preparará dos estados: uno de un cúbit y otro de 5 cúbits, y los medirá.

OneQubit = QuantumRegister(1) # Nuevo registro cuántico de 1 cúbit
print('One Qubit: ' + OneQubit.measure()) # Debe imprimir 'One Qubit: 0'

FiveQubits = QuantumRegister(5) # Nuevo registro cuántico de 5 cúbits
# Debe imprimir 'Five Qubits: 00000'
print('Five Qubits: ' + FiveQubits.measure())

##############################################
# Intercambio de 2 cúbits #
#################################################
# Aquí, aplicaremos una puerta Pauli-X/puerta NOT
# al primer cúbit y, después del algoritmo,
# se intercambiará con el segundo cúbit.

Swap = QuantumRegister(2) # Nuevo registro cuántico de 2 cúbits
Swap.applyGate('X', 1) # Aplicar la puerta NOT. Si se mide ahora, debería ser 10

# Iniciar el algoritmo de intercambio
Swap.applyGate('CNOT', 1, 2)
Swap.applyGate('H', 1)
Swap.applyGate('H', 2)
Swap.applyGate('CNOT', 1, 2)
Swap.applyGate('H', 1)
Swap.applyGate('H', 2)
Swap.applyGate('CNOT', 1, 2)
# Finalizar el algoritmo de intercambio

print('SWAP: |' + Swap.measure() + ">') # Medir el estado, debería ser 01

####################################################
# Lanzamiento de moneda justo #
################################################
# En este experimento se muestra un lanzamiento de moneda justo.
# En este experimento se preparará un estado con la misma probabilidad de lanzarse a cada estado posible. Para ello, se utilizará la Puerta de Hadamard.

# Nuevo Registro Cuántico de 1 Qubit (Como una moneda solo tiene 2 estados)
FairCoinFlip = QuantumRegister(1)
# Si se mide en este punto, debería ser |0>

# Aplicando la puerta Hadamard, ahora hay una probabilidad equitativa de medir 0 o 1
FairCoinFlip.applyGate('H', 1)

# Ahora, se medirá el estado, invirtiéndolo a 0 o 1. Si es 0, diremos "Cara", y si es 1, diremos "Cruz".
FairCoinFlipAnswer = FairCoinFlip.measure() # Ahora está invertido, así que podemos probar
if FairCoinFlipAnswer == '0':
print('FairCoinFlip: Cara')
elif FairCoinFlipAnswer == '1':
print('FairCoinFlip: Tails)

###############################################
# Puerta CNOT #
###############################################
# En este experimento, se prepararán 4 estados: {00, 01, 10, 11}
# Y luego se ejecutará la misma Puerta CNOT sobre ellos.
# Para mostrar los efectos del CNOT. El cúbit objetivo será 2 y el control 1.

# Nuevo registro cuántico de 2 cúbits, realizado 4 veces.
# Si se mide alguno en este momento, el resultado será 00
ZeroZero = QuantumRegister(2)
ZeroOne = QuantumRegister(2)
OneZero = QuantumRegister(2)
OneOne = QuantumRegister(2)

# Ahora, prepare cada uno en el estado según su nombre.
# ZeroZero se dejará, ya que ese es el primer estado.
ZeroOne.applyGate('X', 2)
OneZero.applyGate('X', 1)
OneOne.applyGate('X', 1)
OneOne.applyGate('X', 2)

# Ahora, se aplicará una CNOT a cada uno.
ZeroZero.applyGate('CNOT', 1, 2)
ZeroOne.applyGate('CNOT', 1, 2)
OneZero.applyGate('CNOT', 1, 2)
OneOne.applyGate('CNOT', 1, 2)

# Imprimir los resultados.
print('CNOT en 00: |' + ZeroZero.measure() + ">)
print('CNOT en 01: |' + ZeroOne.measure() + ">)
print('CNOT en 10: |' + OneZero.measure() + ">)
print('CNOT en 11: |' + OneOne.measure() + ">)
```

Basado principalmente en el código de [corbett/QuantumComputing](https://github.com/corbett/QuantumComputing). Si te interesa un simulador de computación cuántica eficiente, de alto rendimiento y acelerado por hardware, escrito en Rust, consulta [QCGPU](https://github.com/qcgpu/qcgpu-rust).
