# O que é um certificado SSL-TLS

Um certificado SSL/TLS é um objeto digital que permite que os sistemas verifiquem a identidade e, posteriormente, estabeleçam uma conexão de rede criptografada com outro sistema usando o protocolo Secure Sockets Layer/Transport Layer Security (SSL/TLS).

Os certificados são usados em um sistema de criptografia conhecido como Public Key Infrastructure (PKI – Infraestrutura de chave pública). O sistema PKI permite que uma parte estabeleça a identidade de outra usando certificados, se ambas confiarem em um terceiro, conhecido como autoridade de certificado.

Os certificados SSL/TLS atuam, portanto, como cartões de identidade digital para proteger as comunicações de rede e estabelecer a identidade de sites na Internet e de recursos em redes privadas.
## Por que os certificados SSL/TLS são importantes?

Os certificados SSL/TLS estabelecem confiança entre os usuários do site. As empresas instalam certificados SSL/TLS em servidores Web para criar sites protegidos por SSL/TLS. As características de uma página da Web protegida por SSL/TLS são:

- Um ícone de cadeado e uma barra de endereço verde no navegador da Web
- Um prefixo https no endereço do site no navegador
- Um certificado SSL/TLS válido. Verifique se o certificado SSL/TLS é válido clicando e expandindo o ícone de cadeado na barra de endereço URL
- Depois que a conexão criptografada for estabelecida, somente o cliente e o servidor Web poderão ver os dados enviados.

Veja abaixo alguns benefícios dos certificados SSL/TLS.
### Protege dados privados

Os navegadores validam o certificado SSL/TLS de qualquer site para iniciar e manter conexões seguras com o servidor do site. A tecnologia SSL/TLS ajuda a garantir a criptografia de toda a comunicação entre seu navegador e o site.
### Reforce a confiança do cliente

Clientes com experiência na Internet entendem a importância da privacidade e querem confiar nos sites que estão visitando. Um site protegido por SSL/TLS tem o ícone de cadeado verde, que os clientes consideram seguro. A proteção SSL/TLS ajuda os clientes a saber que seus dados estão sendo protegidos quando eles os compartilham com sua empresa.
### Compatibilidade com a conformidade normativa

Algumas empresas devem estar em conformidade com as regulamentações do setor para confidencialidade e proteção de dados. Por exemplo, as empresas do setor de cartões de pagamento devem aderir ao PCI DSS. O PCI DSS é um requisito do setor para fornecer transações on-line seguras, incluindo a proteção do servidor Web com um certificado SSL/TLS. 
### Melhore o SEO

Os principais mecanismos de pesquisa tornaram a proteção SSL/TLS um fator de classificação para otimização de mecanismos de pesquisa. Um site protegido por SSL/TLS provavelmente terá uma classificação mais alta no mecanismo de pesquisa do que um site semelhante sem um certificado SSL/TLS. Isso aumenta os visitantes dos mecanismos de pesquisa para o site protegido por SSL/TLS.
## Quais são os princípios essenciais da tecnologia de certificados SSL/TLS?

SSL/TLS significa camada de soquetes seguros e segurança da camada de transporte. É um protocolo ou regra de comunicação que permite que os sistemas de computador conversem entre si na Internet com segurança. Os certificados SSL/TLS permitem que os navegadores da Web identifiquem e estabeleçam conexões de rede criptografadas para sites usando o protocolo SSL/TLS.
### Criptografia

Criptografia significa embaralhar a mensagem original para que ela só possa ser descriptografada pelo destinatário pretendido. Por exemplo, você altera a palavra _cat_ para _ecv_ alterando cada letra indo para frente duas vezes no alfabeto. O destinatário conhece a regra (ou chave) e retrocede cada letra duas vezes para ler a palavra real. A criptografia SSL/TLS baseia-se nesse conceito usando criptografia de chave pública, com duas chaves diferentes para criptografar e descriptografar uma mensagem. O sistema PKI permite que uma parte estabeleça a identidade de outra usando certificados, se ambas confiarem em um terceiro, conhecido como autoridade de certificado. A autoridade de certificação verifica o certificado e autentica ambas as partes antes do início da comunicação.

Os dois tipos de chaves são:
#### _Chave pública_

O navegador e o servidor Web se comunicam codificando e decodificando informações usando pares de chaves públicas e privadas. A chave pública é uma chave criptográfica que o servidor Web fornece ao navegador no certificado SSL/TLS. O navegador usa a chave para criptografar as informações antes de enviá-las ao servidor Web
#### _Chave privada_

Somente o servidor Web tem a chave privada. Um arquivo criptografado pela chave privada só pode ser descriptografado pela chave pública, e vice-versa. Se a chave pública puder descriptografar apenas o arquivo que foi criptografado pela chave privada, a capacidade de descriptografar esse arquivo garante que o destinatário e o remetente pretendidos sejam quem afirmam ser.
### Autenticação

O servidor envia a chave pública no certificado SSL/TLS ao navegador. O navegador verifica o certificado de um terceiro confiável. Consequentemente, ele consegue verificar se o servidor Web é quem afirma ser.
### Assinatura digital

A assinatura digital é um número exclusivo para cada certificado SSL/TLS. O destinatário gera uma nova assinatura digital e a compara à assinatura original para verificar se terceiros não adulteraram o certificado enquanto ele percorria a rede.
## Quem valida certificados SSL/TLS?

A autoridade de certificação (CA) é uma organização que vende certificados SSL/TLS para proprietários da Web, empresas de hospedagem na Web ou empresas. A CA valida os detalhes do domínio e do proprietário antes de emitir o certificado SSL/TLS. Para ser uma CA, a organização deve atender a requisitos específicos definidos pelo sistema operacional, pelos navegadores ou pela empresa de dispositivos móveis e se inscrever para ser listada como uma autoridade de certificação raiz. Isso é importante para estabelecer confiança entre os usuários da Internet. Por exemplo, o [Amazon Trust Services](https://www.amazontrust.com/) é uma autoridade de certificação e pode emitir certificados SSL/TLS para sites.

## Qual é o período de validade do certificado SSL/TLS?

Um certificado SSL/TLS tem um período de validade máximo de 13 meses. A validade do certificado SSL/TLS foi reduzida gradualmente ao longo dos anos. O objetivo dessa medida é reduzir os riscos de segurança que afetam empresas e usuários da Web. Por exemplo, terceiros não confiáveis podem usar um certificado SSL/TLS válido de um domínio expirado para criar um site não autorizado. 

Ao encurtar o período de validade, as chances de uso indevido de certificados SSL/TLS são reduzidas. Quando o certificado SSL/TLS expira, os visitantes da Web recebem um aviso no navegador informando que o site não é seguro. A organização revoga o certificado SSL/TLS antigo e o substitui por um renovado. Para evitar incidentes de segurança, o processo de renovação precisa acontecer antes que o certificado anterior expire.  
## O que está incluído em um certificado SSL/TLS?

Um certificado SSL/TLS contém as informações abaixo. 

- Nome de domínio
- Autoridade de certificação
- Assinatura digital da autoridade de certificação
- Data de emissão
- Data de validade
- Chave pública
- Versão SSL/TLS

TLS significa segurança da camada de transporte. É um sucessor e uma continuação do protocolo SSL/TLS versão 3.0. Existem apenas pequenas diferenças técnicas entre o SSL/TLS e o TLS. Como o SSL/TLS, o TLS fornece um canal de transmissão de dados criptografados entre um navegador e o servidor Web. Os certificados SSL/TLS modernos usam o protocolo TLS em vez de SSL/TLS, mas SSL/TLS continua sendo um acrônimo bastante usado pelos especialistas em segurança. Embora não seja exatamente o mesmo, os termos SSL e TLS são comumente usados para se referir à mesma coisa. Também podem se referir ao protocolo de criptografia criptográfica como SSL/TLS.
## Como funciona um certificado SSL/TLS?

Os navegadores usam o certificado SSL/TLS para iniciar uma conexão segura com o servidor Web por meio do handshake SSL/TLS. O handshake SSL/TLS faz parte da tecnologia de comunicação do protocolo seguro de transferência de hipertexto (HTTPS). É uma combinação de HTTP e SSL/TLS. O HTTP é um protocolo que os navegadores da Web usam para enviar informações em texto simples a um servidor Web. O HTTP transmite dados não criptografados, ou seja, as informações enviadas de um navegador podem ser interceptadas e lidas por terceiros. Os navegadores usam HTTP com SSL/TLS ou HTTPS para proporcionar uma comunicação totalmente segura.
### Aperto de mão SSL/TLS

O handshake SSL/TLS envolve as seguintes etapas:

1. O navegador abre um site seguro por SSL/TLS e se conecta ao servidor da web.
2. O navegador tenta verificar a autenticidade do servidor web solicitando informações identificáveis. 
3. O servidor Web envia o certificado SSL/TLS que contém uma chave pública como resposta.
4. O navegador verifica o certificado SSL/TLS, garantindo que ele seja válido e corresponda ao domínio do site. Quando o navegador estiver satisfeito com o certificado SSL/TLS, ele usará a chave pública para criptografar e enviar uma mensagem que contenha uma chave de sessão secreta.
5. O servidor web usa sua chave privada para descriptografar a mensagem e recuperar a chave de sessão. Em seguida, ele usa a chave de sessão para criptografar e enviar uma mensagem de confirmação para o navegador.
6. Agora, o navegador e o servidor da Web mudam para usar a mesma chave de sessão para trocar mensagens com segurança. 
### Chave de sessão 

Uma chave de sessão mantém a comunicação criptografada entre o navegador e o servidor Web após a conclusão da autenticação SSL/TLS inicial. A chave de sessão é uma chave de cifra para criptografia simétrica. A criptografia simétrica usa a mesma chave para criptografia e descriptografia. A criptografia assimétrica ocupa imenso poder de computação. Portanto, o servidor web alterna para criptografia simétrica que requer menos cálculos para manter uma conexão SSL/TLS.
## Quais são os tipos de certificados SSL/TLS?

Os certificados SSL/TLS diferem conforme a validação e o domínio. Certificados com diferentes níveis de validação são classificados como:

- Certificados de validação estendida
- Certificados validados pela organização
- Certificados validados por domínio

Os certificados SSL/TLS compatíveis com diferentes tipos de domínio são:

- Certificado de domínio único
- Certificado curinga
- Certificado de vários domínios
### Certificados de validação estendida 

O certificado de validação estendida (EV SSL/TLS) é um certificado digital que tem o mais alto nível de criptografia, validação e confiança. Ao solicitar um EV SSL/TLS, uma organização ou proprietário da Web é submetido a verificações rigorosas feitas por autoridades de certificação. Isso inclui a verificação do endereço comercial físico, o pedido de certificado adequado e os direitos exclusivos de uso do domínio. 

As empresas usam o EV SSL/TLS para proteger os usuários contra terceiros não autorizados. Isso é importante quando a empresa processa dados confidenciais no site, como transações financeiras e prontuários médicos. Um certificado EV SSL/TLS contém detalhes da organização comercial, que podem ser visualizados em um navegador.
### Certificados de validação da organização

Os certificados de validação da organização (OV SSL/TLS) são inferiores aos EV SSL/TLS em termos de validação e confiança. Como os EV SSL/TLSs, as empresas devem passar por um processo de verificação ao solicitar o OV SSL/TLS. Embora o processo de verificação seja menos rigoroso, os candidatos devem provar a propriedade do domínio às autoridades de certificação.

O certificado OV SSL/TLS contém informações comerciais validadas e pode ser inspecionado no navegador. Empresas comerciais e com atendimento direto ao público usam o certificado OV SSL/TLS para criar confiança entre os clientes. O OV SSL/TLS fornece criptografia robusta para proteger a privacidade dos clientes durante a navegação na Web. 
### Certificados de validação de domínio

Os certificados de validação de domínio (DV SSL/TLS) são certificados digitais que têm a validação mais baixa. Solicitá-los também custa menos. Diferentemente dos EV SLLs e dos OV SSL/TLSs, os solicitantes de certificados DV passam por um processo de verificação menos rigoroso. O solicitante comprova a propriedade do domínio respondendo a um e-mail de verificação ou telefonema.

Um certificado DV não contém informações completas da organização ou empresa do solicitante. Portanto, não oferece total garantia aos usuários. Os certificados DV são adequados para sites informativos, como blogs. Não são ideais para gateways de pagamento, empresas de assistência médica ou outros sites que lidam com dados sigilosos.
### Certificados SSL/TLS de domínio único

O certificado SSL/TLS de domínio único é um certificado SSL/TLS que protege apenas um domínio ou subdomínio. Um domínio é a URL ou endereço principal de um site, como [amazon.com](https://amazon.com/). Um subdomínio é um endereço da Web com uma extensão de texto que precede o domínio principal, como [aws.amazon.com](https://aws.amazon.com/pt/).

Por exemplo, você pode usar um certificado SSL/TLS de domínio único em [http://example.com](http://example.com/). No entanto, não é possível usar o certificado para [http://example.com](http://example.com/) e [sub.example.com](http://sub.example.com/) simultaneamente.
### Certificados SSL/TLS curinga

O certificado SSL/TLS curinga é um certificado SSL/TLS que protege um domínio e todos os seus subdomínios. Por exemplo, você pode usar um certificado SSL/TLS curinga para proteger [http://example.com](http://example.com/), [blog.example.com](http://blog.example.com/) e [shop.example.com](http://shop.example.com/)
### Certificados SSL/TLS de vários domínios

Os certificados de vários domínios também são conhecidos como certificados de comunicações unificadas. Um certificado SSL/TLS de vários domínios oferece proteção SSL/TLS para vários nomes de domínio hospedados no mesmo servidor ou em servidores diferentes com a mesma propriedade. Por exemplo, você compra um certificado de vários domínios para [http://example1.com](http://example1.com/), [domain2.co.uk](http://domain2.co.uk/),[shop.business3.com](http://shop.business3.com/) e [chat.message.au](http://chat.message.au/).