# Convertendo certificado PKX para PEM

Este guia descreve como converter um certificado no formato PKCS#12 (extensão `.pfx` ou `.p12`) para o formato PEM utilizando o OpenSSL.

## O que são os formatos PKCS#12 e PEM?

- **PKCS#12**: Um formato binário que armazena certificados e chaves privadas, podendo ser protegido por senha. Usado principalmente no Windows.
- **PEM**: Um formato de texto base64, comumente utilizado em servidores Linux. Os certificados e chaves são armazenados em arquivos separados.

## Pré-requisitos

1. **OpenSSL instalado** no ambiente. 
   - No Linux, normalmente está pré-instalado. 
   - No Windows, você pode instalar pelo [site oficial](https://www.openssl.org/).
2. Certificado no formato `.pfx` ou `.p12` que será convertido.

---

## Passos para a conversão

### 1. Extraindo a chave privada

```bash
openssl pkcs12 -in certificado.pfx -nocerts -out privkey.pem
openssl pkcs12 -in certificado.pfx -nocerts -nodes -out privkey.pem
```

- `in` certificado.pfx: Especifica o arquivo de entrada.
- `nocerts`: Extrai apenas a chave privada.
- `nodes`: Remove a senha da chave privada.
- `out` privkey.pem: Salva a chave privada no formato PEM.
 
Será solicitado que você insira a senha do certificado .pfx e defina uma senha para proteger a chave privada exportada

### 2. Extraindo o certificado

```bash
openssl pkcs12 -in certificado.pfx -clcerts -nokeys -out cert.pem
```

- `in` certificado.pfx: Especifica o arquivo de entrada.
- `clcerts`: Inclui apenas o certificado, ignorando os certificados da cadeia (CA).
- `nokeys`: Não exporta a chave privada.
- `out` cert.pem: Salva o certificado no formato PEM. 

### 3. Extraindo a cadeia de certificados (CA)

```bash
openssl pkcs12 -in certificado.pfx -cacerts -nokeys -out chain.pem
```

- `in` certificado.pfx: Especifica o arquivo de entrada.
- `cacerts`: Extrai apenas os certificados da CA.
- `nokeys`: Não exporta a chave privada.
- `out` chain.pem: Salva o certificado no formato PEM. 

### 4. Gerando o arquivo full chain

Combine o certificado principal e a cadeia de certificados em um único arquivo para criar o full

```bash
cat cert.pem chain.pem > fullchain.pem
```

### 5. Resultado

Após a execução dos passos, você terá:

- `privkey.pem`: Contém a chave privada no formato PEM.
- `cert.pem`: Contém o certificado no formato PEM.
- `chain.pem`: Contém a cadeia de certificados.
- `fullchain.pem`: Contém o certificado principal combinado com a cadeia completa.

### 6. Notas

- **Certifique-se de armazenar a chave privada com segurança.**
- Os arquivos `fullchain.pem` e o `privkey.pem` são aqueles frequentemente adotados nos servidores Nginx ou Apache!