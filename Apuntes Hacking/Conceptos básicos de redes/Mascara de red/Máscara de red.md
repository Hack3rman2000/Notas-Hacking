___

- Tags: #redes #ipv4  #subnetting #network_id #broadcast #netmask

___
# Definición 

La **máscara de red** es un componente crucial para la segmentación de direcciones [[Dirección IP|IP]], dividiéndolas en partes de red y partes de host. Aquí se detallan sus características principales y su relación con [[CIDR]]:

La **máscara de red** se configura con una secuencia de unos seguidos de ceros, indicando la parte de red y la parte de host, respectivamente. Por ejemplo, "/24" en [[CIDR]] indica que los primeros 24 bits son la parte de red. Ayuda a los dispositivos de red a discernir entre la red y los dispositivos específicos en ella.

___

## Clases de Direcciones IP y Máscaras de Red

1. **Clase A:**
    
    - Máscara de subred predeterminada: 255.0.0.0
    - Primer octeto: 0 a 127
    - Ejemplo: 10.52.36.11
2. **Clase B:**
    
    - Máscara de subred predeterminada: 255.255.0.0
    - Primer octeto: 128 a 191
    - Ejemplo: 172.16.52.63
3. **Clase C:**
    
    - Máscara de subred predeterminada: 255.255.255.0
    - Primer octeto: 192 a 223
    - Ejemplo: 192.168.123.132

___
## Cálculo Binario para Máscara, Network ID y Broadcast

1. Convertir la [[Dirección IP|IP]] a binario.
2. Aplicar el [[CIDR]] en binario (22 bits en este ejemplo).
    - **Máscara de Red**: 11111111.11111111.11111100.0000000 (255.255.252.0)
    - [[Network ID]]: 11000000.10100111.00001000.00000000 (192.167.8.0)
    - [[Broadcast]] 11000000.10100111.00001011.11111111 (192.167.11.255)
3. Calcular Total de Hosts: 32 - 22 = 10 bits para hosts.
    - Total de Hosts: 2^10 = 1024

___

## Comandos Útiles

```bash
echo "obase=2;numero" | bc
```

Convierte un número a binario.

```bash
echo "ibase=2;binario" | bc
```

Convierte un binario a número.