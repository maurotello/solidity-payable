# Unidad 3

## Compilar e interactuar con un smart contract local

### 1 - utilizando truffle develop

Truffle: herramientas y ambiente de desarrollo y también un testing framework

- Compilamos el contrato
```
$ truffle compile
```

- Arrancamos Truffle Develop, esto generará una Blockchain de desarrollo localmente en el puerto 9545
```
$ truffle develop
```

- Se creará un total de 10 cuentas virtuales, cada una comenzando con 100 Ether. Y sus respectivas Private Keys. Recomiendo copiar estas cuentas y Private Keys para usar luego.

- Migramos los smart contracts compilados, esto sería hacer el deploy en nuestra blockchain local de desarrollo
```
truffle(develop)> migrate --reset
```

- Creamos una instancia del smart contract Storage desplegado para interactuar con el:
```
truffle(develop)> const box = await Storage.deployed();
truffle(develop)> await box.store(42)
truffle(develop)> await box.retrieve()
truffle(develop)> (await box.retrieve()).toString()
```

- Creamos una instancia del smart contract Payable desplegado para interactuar con el:
```
truffle(develop)> const pay = await Payable.deployed();
truffle(develop)> (await pay.getBalance()).toString()
truffle(develop)> await pay.transfer ('0x6582B10ce3169CA742ACfC92f6548d10949A788C', '5000000000000000000')
```

### 2 - Vinculamos Ganache cli con Remix y Metamask

- Arrancamos Ganache_cli
```
$ ganache_cli
```

- ganache_cli esto generará una Blockchain de desarrollo localmente en el puerto 8545
- Conectamos Remix con Ganache desde Deploy & Run: Environment: Web3 Provider: http://127.0.0.1:8545
- Conectamos Remix con Metamask desde Deploy & Run: Environment: Injected Web3
- Importar cuentas generadas en Ganache_cli a Metamask
- Compilar y hacer deploy del contrato Payable
- Transferir al contrato desde metamask
- Transfiero del contrato a la 2da cuenta


### 3 - Conectamos a GANACHE GUI

Modificamos truffle-config.js
Agregamos en networks: 

```
ganachegui: {
     host: "127.0.0.1",     // Localhost
     port: 7545,            // Ganache GUI port
     network_id: "5777",    // Ganache GUI network id
    }, 
```

```
$ truffle compile
$ truffle migrate --network ganachegui
$ truffle console --network ganachegui
```

```
truffle(ganachegui)> const box = await Storage.deployed();
truffle(ganachegui)> await box.store(25)
truffle(ganachegui)> await box.retrieve()
truffle(ganachegui)> (await box.retrieve()).toString()

truffle(develop)> const pay = await Payable.deployed();
truffle(develop)> (await pay.getBalance()).toString();
truffle(develop)> await pay.transfer ('0xfDFca3b3F5Ebd39ECDcabD71b31a15724F73BD93', '1000000000000000000')
```


## NOTAS:

### Call() envía ether a un address aunque no se definido como payable

Los tipos address y address payable, ambos almacenan una dirección Ethereum de 160 bits. El concepto de direcciones payable y non-payable solo existe en tiempo de compilación. La diferencia entre las direcciones payable y non-payable desaparece en el código del contrato compilado.

### Métodos 
Puede utilizar .transfer (..) y .send (..) en address payable, pero no en address.

Puede utilizar la llamada de bajo nivel .call(..) tanto en address como en address payable, incluso si envía  valor.

### Razón 

Los tipos de lenguajes de programación existen por dos razones:

1. Para proporcionar semántica especial de tipo específico (por ejemplo, + en números es una suma, pero + en cadenas puede ser una concatenación)

2. Para limitar la cantidad de programas incorrectos que acepta el compilador

La división de direcciones en address y address payable sirve para este último propósito. Obliga al programador de contratos inteligentes a pensar si una dirección debería recibir ether del contrato inteligente. Si nunca debe recibir Ether, se puede utilizar el tipo address. La compilación fallará con un error de tipo si el programador comete un error e intenta transferir Ether a esa dirección.


## assert() VS require()

Hay dos aspectos a considerar al elegir entre assert () y require ()


1. Eficiencia de gas

assert (false) se compila en 0xfe, que es un código de operación no válido, que usa todo el gas restante y revierte todos los cambios.
require (false) se compila en 0xfd, que es el código de operación REVERT, lo que significa que reembolsará el gas restante. 

2. Análisis de códigos de bytes

La función require se debe utilizar para garantizar que se cumplan las condiciones válidas, como las entradas o las variables de estado del contrato, o para validar los valores de retorno de las llamadas a los contratos externos. 
Si se utilizan correctamente, las herramientas de análisis pueden evaluar su contrato para identificar las condiciones y las llamadas a funciones que llegarán a una función assert() fallida. 
El código que funciona correctamente nunca debe llegar a una declaración de assert() fallida; si esto sucede, hay un error en su contrato que debe corregir.






