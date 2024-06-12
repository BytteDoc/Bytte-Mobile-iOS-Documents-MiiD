![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/LogoBytte.png)

# Bytte SDK
# Documento Integración SDK Nativo IOS Documentos

## CONFIDENCIALIDAD

La información contenida en el presente documento es **CONFIDENCIAL** y forma parte del secreto comercial e industrial de BYTTE SAS. Esta documentación implica la transmisión de información cuya propiedad corresponde exclusivamente a BYTTE SAS. En consecuencia, la divulgación o el uso inapropiado de la información aquí contenida por parte del receptor implicará la aplicación de las normas legales pertinentes para su debida protección. Cualquier uso no autorizado de este material puede resultar en acciones legales.

## Descripción

Este repositorio contiene una guía completa y detallada sobre cómo implementar y utilizar el sdk de captura de documentos en aplicaciones iOS. La solución está diseñada para manejar los siguientes documentos:

- **Cédula de Ciudadanía Colombiana** (en sus dos versiones: hologramas y digital)
- **Pasaporte**
- **Cédula de Extranjería**

El objetivo de este repositorio es proporcionar a los desarrolladores todas las herramientas y la información necesaria para integrar de manera eficiente y precisa la funcionalidad de captura de documentos en sus aplicaciones. Aquí encontrarás ejemplos de código, instrucciones de configuración, mejores prácticas y recursos adicionales para garantizar una implementación exitosa.

## Contenido

1. [Introducción](#introducción)
2. [Requisitos](#requisitos)
3. [Instalación](#instalación)
4. [Configuración](#configuración)
5. [Uso](#uso)
6. [Respuesta](#Respuesta)
7. [Soporte](#soporte)

## Introducción

El presente documento tiene como finalidad dar a conocer el paso a paso de la instalación del SDK provisto por Bytte en una aplicación nativa IOS desarrollada con el IDE XCode.

## Requisitos

* Se debe verificar la calidad de la cámara, es recomendable utilizar dispositivos con cámara que tengan la característica de “Auto Foco” habilitada.
* Se recomiendan cámaras con resolución mayores o iguales a 8 Mega Pixeles para un óptimo rendimiento.
* El SDK no funciona sobre dispositivos virtuales, únicamente sobre dispositivos físicos IPhone.

#### Deshabilitar BitCode

Para el correcto funcionamiento de la aplicación, se debe deshabilitar el BitCode:

![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/Bitcode.png)


## Instalación

* Para la compilación de aplicación en plataforma IOS, se requiere:
    > * XCode 15.3 o Superior con SDK IOS 15 o superior.

## Configuración

* La aplicación debe tener las siguientes librerías como recurso embebido en el folder “Frameworks” como lo indica la siguiente imagen:

 ![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/Documentos/Framework.png)

  > * BytteLibrarySDKComun.framework
  > * BytteLibrarySDKDocumentoV2.framework
  > * BlinkID.framework

## Uso

Para utilizar los métodos proporcionados por el SDK para la captura de documentos, siga estos pasos:

### 1. Importar el SDK

Primero, es necesario importar el SDK en su proyecto de iOS:

```swift
import BytteLibrarySDKDocumentoV2
```

### 2. Implementar el Delegado

El SDK expone un delegado que debe implementar para manejar los eventos de captura de documentos:

```swift
class SuClase: UIViewController, IDCaptureDocumentBytteDelegate {
    // Implementación de los métodos del delegado
}
```

### 3. Configurar el Método de Captura

Para el correcto funcionamiento del SDK, el método de captura requiere tres variables que le proporcionará BYTTE SAS, así como un cuarto parámetro que debe generar y proporcionar por cuenta propia:

- `url`: La URL del servidor de BYTTE.
- `clientId`: El ID de cliente proporcionado por BYTTE.
- `clientKey`: La clave de cliente proporcionada por BYTTE.
- `sessionID`: Un identificador de sesión único que genera usted mismo, utilizado para encriptar las imágenes capturadas.

### 4. Variables de Captura

Las variables `capture`, `captureType` e `imageTemplate` son enviadas por el cliente para especificar qué documento debe capturarse y que lado del documento.

- `capture`: Especifica el tipo de documento que se está capturando:
  - `Capture.COCC`: Cédula de ciudadanía colombiana (hologramas).
  - `Capture.COCCV2`: Cédula de ciudadanía colombiana (digital).
  - `Capture.COEXT`: Cédula de extranjería.
  - `Capture.PASSPORT`: Pasaporte.
- `captureType`: Define si se está capturando el frente o el reverso del documento:
  - `CaptureType.FRONT`: Captura del frente del documento.
  - `CaptureType.BACK`: Captura del reverso del documento.
- `imageTemplate`: Es una imagen de plantilla que se usa para guiar la captura del documento, mostrando al usuario cómo alinear el documento durante la captura.

### 5. Ejemplo de Implementación

Aquí tiene un ejemplo de cómo configurar y utilizar el SDK en su aplicación, incluyendo el evento y el delegado:

```swift
import UIKit
import BytteLibrarySDKDocumentoV2

class DocumentCaptureViewController: UIViewController, IDCaptureDocumentBytteDelegate {

    var sessionID: String = "unicoSessionIDGenerado"
    var url: String = "https://api.bytte.com"
    var clientId: String = "tuClientId"
    var clientKey: String = "tuClientKey"
    var vcID: IDCaptureDocumentBytte!
    var typeCapture: Int = 1 // Esta variable debería ser configurada según su lógica

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    @IBAction func btnReverso(_ sender: UIButton) {
        var back: UIImage? = nil
        var capture: Capture? = nil
        let captureType = CaptureType.BACK

        switch self.typeCapture {
        case 1:
            back = UIImage(named:"backDoc.png")
            capture = Capture.COCC
        case 2:
            back = UIImage(named:"backDocCV2.png")
            capture = Capture.COCCV2
        case 3:
            back = UIImage(named:"backDocCV2.png")
            capture = Capture.COEXT
        default:
            break
        }

        if let back = back, let capture = capture {
            self.goViewController(capture: capture, captureType: captureType, image: back)
        }
    }

    @IBAction func btnFrente(_ sender: UIButton) {
        var imFront: UIImage? = nil
        var capture: Capture? = nil
        let captureType = CaptureType.FRONT

        switch self.typeCapture {
        case 1:
            imFront = UIImage(named:"front_col_doc.png")
            capture = Capture.COCC
        case 2:
            imFront = UIImage(named:"frontDocCV2.png")
            capture = Capture.COCCV2
        case 3:
            imFront = UIImage(named:"frontDocCV2.png")
            capture = Capture.COEXT
        case 4:
            imFront = UIImage(named:"frontPass.png")
            capture = Capture.PASSPORT
        default:
            break
        }

        if let imFront = imFront, let capture = capture {
            self.goViewController(capture: capture, captureType: captureType, image: imFront)
        }
    }

    func goViewController(capture: Capture, captureType: CaptureType, image: UIImage) {
        self.vcID = IDCaptureDocumentBytte(
            sessionID: self.sessionID, 
            url: self.url, 
            capture: capture, 
            captureType: captureType, 
            imageTemplate: image, 
            clientID: self.clientId, 
            clientKey: self.clientKey
        )

        self.vcID.idCaptureDocumentBytteDelegate = self
        self.vcID.captureDocumentBytte(self)
    }

    // Delegado del documento
    func doIDCaptureDocumentBytte(_ responseDocument: FrenteReversoDocumento!, withPassword FilePassword: String!) {
        if responseDocument.statusOperacion {
            if let reverso = responseDocument.reverso {
                self.alert(titulo: "Resultado", mensaje: "Captura Exitosa PKI: \(reverso.pki ?? "Sin PKI")", pki: reverso.pki)
            } else {
                self.alert(titulo: "Resultado", mensaje: "Captura Exitosa PKI: \(responseDocument.frente.pki ?? "Sin PKI")", pki: responseDocument.frente.pki)
            }
        } else {
            self.alertFail(titulo: "Resultado", mensaje: "Captura no exitosa")
        }
    }

    // Métodos de alerta (debe implementar estos métodos según su lógica)
    func alert(titulo: String, mensaje: String, pki: String?) {
        // Código para mostrar una alerta de éxito
    }

    func alertFail(titulo: String, mensaje: String) {
        // Código para mostrar una alerta de fallo
    }
}
```

Siguiendo estos pasos, podrá integrar y utilizar correctamente el SDK de BYTTE para la captura de documentos en su aplicación iOS.

## Respuesta

Una vez capturado el insumo, el delegado de captura retorna un objeto `FrenteReversoDocumento`, el cual se divide en 3 secciones:

### BaseResponse

Esta sección contiene las siguientes variables:

- `statusOperacion`: Indica si la operación fue exitosa (`true`) o no (`false`).
- `mensajeOriginal` y `mensajeRetorno`: Mensajes que indican información sobre el resultado de la captura, incluyendo descripciones de errores y códigos de operación específicos para cada error.

### Sección de Frente

Si el usuario está capturando el frente del documento, esta sección contendrá el PKI de los insumos y la información recuperada del documento.

### Sección de Reverso

Si el usuario está capturando el reverso del documento, esta sección también contendrá el PKI de los insumos y la información recuperada del documento.

## Soporte

Para obtener más información o asistencia técnica, no dude en ponerse en contacto con nuestro equipo comercial o con el director de proyectos.

Estaremos encantados de ayudarle con cualquier pregunta o inquietud que pueda tener.


