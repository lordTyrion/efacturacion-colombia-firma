# eFacturacionColombia.Firma (C#)

[![version](https://img.shields.io/badge/version-0.9.0-blue.svg)](#) [![status](https://img.shields.io/badge/status-working-brightgreen.svg)](#)

**eFacturacionColombia.Firma** es una librería *no oficial* desarrollada en C#, que se encarga de firmar los documentos XML (facturas y notas de débito/crédito) que se presentan a la DIAN de Colombia para el proceso de facturación electrónica.



### Motivación

Desde el 1 de enero del 2019 será obligatoria la facturación electrónica para los contribuyentes en Colombia, y en la actualidad muchas empresas y personas obligadas no la han implementado. 

Aunque la información técnica proveída por la DIAN debería ser suficiente, encontrar recursos en .NET para este proceso es bastante difícil. La generación de estructuras XML y consumo de servicios web puede ser laborioso pero se puede hacer sin inconvenientes, en cambio la firma requerida por la DIAN puede ser extremadamente difícil de hacer, hasta el punto de ser necesario adquirir soluciones costosas.

Se libera esta librería con el fin de ayudar a implementar la facturación electrónica en ambientes .NET, sin necesidad de comprar soluciones por no poder realizar la firma electrónica de manera correcta.



### Requisitos

Para poder generar, firmar y emitir documentos electrónicos (facturas y notas de débito/crédito) ante la DIAN, se deben cumplir los siguientes requisitos:

* Estar registrado como facturador electrónico
* Disponer de un certificado digital para la facturación electrónica

Para mayor información ver [DIAN - Facturación Electrónica](https://www.dian.gov.co/fizcalizacioncontrol/herramienconsulta/FacturaElectronica/).



### Uso

La clase `FirmaElectronica` contiene un sencillo método (en tres variantes) para firmar los documentos:

```csharp
using eFacturacionColombia;
using eFacturacionColombia.Firma;

// crear instancia
var firma = new FirmaElectronica
{
	RolFirmante = RolFirmante.FACTURANTE,
	RutaCertificado = "path/to/certificate.p12",
	ClaveCertificado = "password here"
};

// usar horario colombiano
var fecha = DateTimeHelper.GetColombianDate();

// firmar archivo
var archivoXml = new FileInfo("path/to/unsigned-factura.xml");
var archivoFirmado = firma.Firmar(archivoXml, TipoDocumento.FACTURA, fecha);

// firmar contenido
var contenidoXml = "<?xml ...";
var contenidoFirmado = firma.Firmar(contenidoXml, TipoDocumento.FACTURA, fecha);

// firmar bytes
var bytesXml = new byte[13042];
var bytesFirmados = firma.Firmar(bytesXml, TipoDocumento.FACTURA, fecha);

// guardar xml (opcional)
File.WriteAllBytes("path/to/signed-factura.xml", bytesFirmados);
```

Si la validación técnica de la firma genera el siguiente error (desde el servicio web):

```
com.indra.mmdd.signature.exceptions.ValidateException: xades4j.verification.CertRefUtils$1: Verification failed for property 'SigningCertificate': Invalid issue name:
```

Significa que el *IssuerName* ‒ generado por la librería ‒ de la firma no es válido, en este caso solicitar al proveedor del certificado la cadena válida, y asignarla *manualmente* usando la propiedad `EmisorCertificado` de la clase `FirmaElectronica`:

```csharp
var firma = new FirmaElectronica
{
	RolFirmante = RolFirmante.FACTURANTE,
	RutaCertificado = "path/to/certificate.p12",
	ClaveCertificado = "password here",
	EmisorCertificado = "C=CO,L=Bogota D.C.,O=..."
};
```



### Reconocimientos

Este proyecto utiliza las siguientes librerías:

- [Microsoft.Xades](#) por *Microsoft France*
- [BouncyCastle.Crypto](https://www.bouncycastle.org/csharp/) por *The Legion Of The Bouncy Castle*
- [FirmaXadesNet](https://github.com/ctt-gob-es/FirmaXadesNet) por *el Dpto. de Nuevas Tecnologías de la Dirección General de Urbanismo del Ayto. de Cartagena*



### Contribuir

Aunque este repositorio no es de libre contribución, se puede aportar:

- Haciendo un fork y enviando las correcciones o actualizaciones necesarias
- Haciendo una donación económica al autor de este proyecto



### Autor

Miguel Huertas <contacto@miguel-huertas.net> - Freelancer



### Licencia

Para mayor información revisar el archivo [LICENCE](LICENCE).





> El autor de este proyecto, por cuestiones de tiempo, no brindará soporte para la implementación al menos que se trate de algo simple (sin estar obligado).
>
> Por otra parte, el autor de este proyecto, pone a su disposición una solución .NET completa para la facturación electrónica (generación/firma/emisión/consulta de documentos, creación de PDF's con QR, envío de correos, etc.) así como asesoría para su implementación; estos servicios son de paga.