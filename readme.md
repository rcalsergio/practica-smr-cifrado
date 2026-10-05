# Práctica de Seguridad Informática: Criptografía Simétrica, Asimétrica y Cracking de Hashes

**Módulo:** SMR - Seguridad Informática
**Entorno:** Kali Linux
**Duración estimada:** 2-3 horas

## Objetivos

- Comprender y aplicar cifrado simétrico (AES) con OpenSSL.
- Comprender y aplicar cifrado asimétrico (RSA/GPG) para cifrado e intercambio de claves.
- Generar y verificar firmas digitales.
- Calcular hashes MD5 y practicar su crackeo con John the Ripper y el diccionario rockyou.

## Requisitos previos

- Máquina Kali Linux actualizada (`sudo apt update`).
- Herramientas necesarias: `openssl`, `gpg`, `john`, `md5sum`.
- Diccionario rockyou disponible en `/usr/share/wordlists/rockyou.txt.gz` (viene preinstalado en Kali).

---

## Parte 1: Criptografía Simétrica (AES)

### 1.1 Crear un archivo de prueba

```bash
mkdir ~/practica_cripto && cd ~/practica_cripto
echo "Este es un mensaje secreto para la práctica de SMR" > mensaje.txt
```

### 1.2 Cifrar con AES-256 (CBC)

```bash
openssl enc -aes-256-cbc -pbkdf2 -salt -in mensaje.txt -out mensaje.enc
```

Se os pedirá una contraseña (usad `claveSMR2024` para todo el grupo, o una propia si trabajáis solos).

### 1.3 Descifrar

```bash
openssl enc -aes-256-cbc -pbkdf2 -d -in mensaje.enc -out mensaje_descifrado.txt
cat mensaje_descifrado.txt
```

### Preguntas (responder en el informe)

1. ¿Qué significa CBC y para qué sirve el salt?
2. ¿Qué pasa si intentáis descifrar con una contraseña incorrecta?
3. ¿Qué ventaja e inconveniente tiene el cifrado simétrico frente al asimétrico?

---

## Parte 2: Criptografía Asimétrica (GPG/RSA)

### 2.1 Generar un par de claves RSA

```bash
gpg --full-generate-key
```

Elegid: RSA y RSA, 4096 bits, sin fecha de caducidad (o 1 año), y rellenad nombre/email ficticios (ej. `alumno1@smr.local`).

### 2.2 Listar vuestras claves

```bash
gpg --list-keys
```

### 2.3 Exportar la clave pública (para compartirla con un compañero)

```bash
gpg --export -a "alumno1@smr.local" > clave_publica_alumno1.asc
```

### 2.4 Intercambio de claves por parejas

- Intercambiad el archivo `clave_publica_X.asc` con un compañero (por USB, red compartida, etc.).
- Importad la clave pública recibida:

```bash
gpg --import clave_publica_companero.asc
```

### 2.5 Cifrar un mensaje para el compañero

```bash
echo "Mensaje secreto de alumno1 para alumno2" > secreto.txt
gpg --encrypt --recipient alumno2@smr.local secreto.txt
```

Esto genera `secreto.txt.gpg`. Enviádselo a vuestro compañero.

### 2.6 Descifrar el mensaje recibido

```bash
gpg --decrypt secreto.txt.gpg
```

(Se os pedirá la contraseña de vuestra clave privada.)

### 2.7 Firma digital

Firmad un documento para garantizar su autenticidad:

```bash
gpg --sign documento.txt
gpg --verify documento.txt.gpg
```

### Preguntas

1. ¿Por qué se usa la clave pública del destinatario para cifrar y no la propia?
2. ¿Qué garantiza una firma digital que no garantiza el cifrado por sí solo?
3. ¿Qué pasaría si perdierais vuestra clave privada?

---

## Parte 3: Hashing y Cracking con John the Ripper

### 3.1 Generar hashes MD5

Cread 3-4 contraseñas "débiles" típicas (que probablemente estén en rockyou) y generad su hash MD5:

```bash
echo -n "123456" | md5sum
echo -n "password" | md5sum
echo -n "iloveyou" | md5sum
```

Anotad los hashes resultantes.

### 3.2 Crear un archivo de hashes en formato John

```bash
nano hashes.txt
```

Contenido (formato `usuario:hash`):

```
usuario1:e10adc3949ba59abbe56e057f20f883e
usuario2:5f4dcc3b5aa765d61d8327deb882cf99
usuario3:f25a2fc72690b780b2a14e140ef6a9e0
```

(Sustituid por los hashes que hayáis generado vosotros.)

### 3.3 Descomprimir rockyou (si no está ya)

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

### 3.4 Lanzar John the Ripper

```bash
john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

### 3.5 Ver los resultados

```bash
john --show --format=Raw-MD5 hashes.txt
```

### 3.6 Reto final

El profesor proporcionará un hash MD5 adicional (más complejo, por ejemplo una contraseña con variaciones tipo `P@ssw0rd!`). Intentad crackearlo con rockyou y, si no funciona, aplicad reglas de John:

```bash
john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes_reto.txt
```

### Preguntas

1. ¿Por qué MD5 se considera inseguro para almacenar contraseñas hoy en día?
2. ¿Qué es un ataque de diccionario y en qué se diferencia de uno de fuerza bruta?
3. ¿Qué medidas (salt, algoritmos lentos como bcrypt/Argon2) mitigan este tipo de ataques?
4. ¿Cuánto ha tardado John en crackear cada contraseña? ¿Por qué creéis que hay diferencias de tiempo?

---

## Entrega

Entregar un informe (PDF o Word) que incluya:

- Capturas de pantalla de cada paso realizado.
- Respuestas a todas las preguntas planteadas.
- Una reflexión final (mínimo 10 líneas) comparando los tres mecanismos trabajados: cifrado simétrico, asimétrico y hashing, y cuándo se debe usar cada uno en un sistema real.

## Criterios de evaluación

| Criterio | Puntos |
|---|---|
| Cifrado simétrico completado correctamente | 2 |
| Cifrado asimétrico e intercambio de claves | 3 |
| Firma digital | 1 |
| Cracking con John the Ripper | 2 |
| Respuestas a preguntas teóricas | 1.5 |
| Reflexión final | 0.5 |
