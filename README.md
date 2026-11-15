# face-api.js

[![Build Status](https://travis-ci.org/justadudewhohacks/face-api.js.svg?branch=master)](https://travis-ci.org/justadudewhohacks/face-api.js)
[![Slack](https://slack.bri.im/badge.svg)](https://slack.bri.im)

**API JavaScript para reconhecimento facial no navegador e no Node.js, desenvolvida com base no núcleo do TensorFlow.js ([tensorflow/tfjs-core](https://github.com/tensorflow/tfjs))**

![faceapi](https://user-images.githubusercontent.com/31125521/57224752-ad3dc080-700a-11e9-85b9-1357b9f9bca4.gif)

## **[Clique aqui para acessar as demonstrações interativas!](https://justadudewhohacks.github.io/face-api.js/)**

## Tutoriais

* **[face-api.js — API JavaScript para reconhecimento facial no navegador com TensorFlow.js](https://itnext.io/face-api-js-javascript-api-for-face-recognition-in-the-browser-with-tensorflow-js-bcc2a6c4cf07)**
* **[Rastreamento e reconhecimento facial em tempo real com JavaScript usando o detector MTCNN do face-api.js](https://itnext.io/realtime-javascript-face-tracking-and-face-recognition-using-face-api-js-mtcnn-face-detector-d924dd8b5740)**
* **[Detecção facial e reconhecimento de emoções em tempo real pela webcam — vídeo](https://youtu.be/CVClHLwv-4I)**
* **[Tutorial simples de reconhecimento facial com JavaScript — vídeo](https://youtu.be/AZ4PdALMqx0)**
* **[Como usar face-api.js com Vue.js e Electron](https://medium.com/@andreas.schallwig/do-not-laugh-a-simple-ai-powered-game-3e22ad0f8166)**
* **[Como adicionar máscaras às pessoas — Gant Laborde no Learn with Jason](https://www.learnwithjason.dev/fun-with-machine-learning-pt-2)**

## Sumário

* **[Recursos](#features)**
* **[Executando os exemplos](#running-the-examples)**
* **[face-api.js no navegador](#face-api.js-for-the-browser)**
* **[face-api.js no Node.js](#face-api.js-for-nodejs)**
* **[Como usar](#getting-started)**
  * **[Carregando os modelos](#getting-started-loading-models)**
  * **[API de alto nível](#getting-high-level-api)**
  * **[Exibindo os resultados da detecção](#getting-started-displaying-detection-results)**
  * **[Opções de detecção facial](#getting-started-face-detection-options)**
  * **[Classes utilitárias](#getting-started-utility-classes)**
  * **[Outros recursos úteis](#getting-started-other-useful-utility)**
* **[Modelos disponíveis](#models)**
  * **[Detecção facial](#models-face-detection)**
  * **[Detecção de pontos de referência faciais](#models-face-landmark-detection)**
  * **[Reconhecimento facial](#models-face-recognition)**
  * **[Reconhecimento de expressões faciais](#models-face-expression-recognition)**
  * **[Estimativa de idade e classificação de gênero](#models-age-and-gender-recognition)**
* **[Documentação da API](https://justadudewhohacks.github.io/face-api.js/docs/globals.html)**

<a name="features"></a>

# Recursos

## Reconhecimento facial

![face-recognition](https://user-images.githubusercontent.com/31125521/57297377-bfcdfd80-70cf-11e9-8afa-2044cb167bff.gif)

## Detecção de pontos de referência faciais

![face_landmark_detection](https://user-images.githubusercontent.com/31125521/57297731-b1ccac80-70d0-11e9-9bd7-59d77f180322.jpg)

## Reconhecimento de expressões faciais

![preview_face-expression-recognition](https://user-images.githubusercontent.com/31125521/50575270-f501d080-0dfb-11e9-9676-8f419efdade4.png)

## Estimativa de idade e classificação de gênero

![age_gender_recognition](https://user-images.githubusercontent.com/31125521/57297736-b5603380-70d0-11e9-873d-8b6c7243eb64.jpg)

<a name="running-the-examples"></a>

# Executando os exemplos

Clone o repositório:

``` bash
git clone https://github.com/justadudewhohacks/face-api.js.git
```

## Executando os exemplos no navegador

``` bash
cd face-api.js/examples/examples-browser
npm i
npm start
```

Acesse http://localhost:3000/ no navegador.

## Executando os exemplos no Node.js

``` bash
cd face-api.js/examples/examples-nodejs
npm i
```

Agora, execute um dos exemplos usando o ts-node:

``` bash
ts-node faceDetection.ts
```

Ou compile e execute os exemplos com o Node.js:

``` bash
tsc faceDetection.ts
node faceDetection.js
```

<a name="face-api.js-for-the-browser"></a>

# face-api.js no navegador

Basta incluir a versão mais recente do script disponível em [dist/face-api.js](https://github.com/justadudewhohacks/face-api.js/tree/master/dist).

Ou instale a biblioteca pelo npm:

``` bash
npm i face-api.js
```

<a name="face-api.js-for-nodejs"></a>

# face-api.js no Node.js

É possível usar a mesma API no ambiente Node.js com implementações alternativas (*polyfills*) de recursos específicos do navegador, como HTMLImageElement, HTMLCanvasElement e ImageData. A maneira mais simples de fazer isso é instalar o pacote node-canvas.

Como alternativa, você pode criar seus próprios tensores a partir dos dados de imagem e fornecê-los diretamente à API. Um tensor é uma estrutura de dados numéricos usada em aprendizado de máquina.

Também é recomendável instalar @tensorflow/tfjs-node (não é obrigatório), que acelera consideravelmente o processamento ao utilizar a biblioteca nativa do TensorFlow em C++:

``` bash
npm i face-api.js canvas @tensorflow/tfjs-node
```

Em seguida, adaptamos o ambiente do Node.js para usar esses polyfills:

``` javascript
// importa a integração do Node.js com o TensorFlow nativo,
// não é obrigatória, mas acelera bastante o processamento (requer Python)
import '@tensorflow/tfjs-node';

// fornece implementações para HTMLCanvasElement, HTMLImageElement e ImageData no Node.js
import * as canvas from 'canvas';

import * as faceapi from 'face-api.js';

// adapta o ambiente Node.js, fornecendo implementações de
// HTMLCanvasElement e HTMLImageElement
const { Canvas, Image, ImageData } = canvas
faceapi.env.monkeyPatch({ Canvas, Image, ImageData })
```

<a name="getting-started"></a>

# Primeiros passos

<a name="getting-started-loading-models"></a>

## Carregando os modelos

Todas as instâncias globais das redes neurais são disponibilizadas por faceapi.nets:

``` javascript
console.log(faceapi.nets)
// ageGenderNet
// faceExpressionNet
// faceLandmark68Net
// faceLandmark68TinyNet
// faceRecognitionNet
// ssdMobilenetv1
// tinyFaceDetector
// tinyYolov2
```

Para carregar um modelo, disponibilize o arquivo manifest.json correspondente e os arquivos de pesos do modelo (divididos em partes, chamadas *shards*) como recursos do projeto. Basta copiá-los para a pasta pública ou de recursos. O manifest.json e os arquivos de pesos de cada modelo devem estar no mesmo diretório, acessíveis pelo mesmo caminho.

Considerando que os modelos estejam em **public/models**:

``` javascript
await faceapi.nets.ssdMobilenetv1.loadFromUri('/models')
// faça o mesmo para os demais modelos:
// await faceapi.nets.faceLandmark68Net.loadFromUri('/models')
// await faceapi.nets.faceRecognitionNet.loadFromUri('/models')
// ...
```

No Node.js, também é possível carregar os modelos diretamente do disco:

``` javascript
await faceapi.nets.ssdMobilenetv1.loadFromDisk('./models')
```

Também é possível carregar o modelo a partir de um tf.NamedTensorMap:

``` javascript
await faceapi.nets.ssdMobilenetv1.loadFromWeightMap(weightMap)
```

Como alternativa, você pode criar suas próprias instâncias das redes neurais:

``` javascript
const net = new faceapi.SsdMobilenetv1()
await net.loadFromUri('/models')
```

Você também pode carregar os pesos como um Float32Array, caso queira usar modelos não compactados:

``` javascript
// usando fetch
net.load(await faceapi.fetchNetWeights('/models/face_detection_model.weights'))

// usando axios
const res = await axios.get('/models/face_detection_model.weights', { responseType: 'arraybuffer' })
const weights = new Float32Array(res.data)
net.load(weights)
```

<a name="getting-high-level-api"></a>

## API de alto nível

Nos exemplos a seguir, **input** pode ser um elemento HTML img, video ou canvas, ou o identificador (id) desse elemento.

``` html
<img id="myImg" src="images/example.png" />
<video id="myVideo" src="media/example.mp4" />
<canvas id="myCanvas" />
```

``` javascript
const input = document.getElementById('myImg')
// const input = document.getElementById('myVideo')
// const input = document.getElementById('myCanvas')
// ou simplesmente:
// const input = 'myImg'
```

### Detectando rostos

Detecta todos os rostos em uma imagem. Retorna **Array<[FaceDetection](#interface-face-detection)>**:

``` javascript
const detections = await faceapi.detectAllFaces(input)
```

Detecta o rosto com a maior pontuação de confiança em uma imagem. Retorna **[FaceDetection](#interface-face-detection) | undefined**:

``` javascript
const detection = await faceapi.detectSingleFace(input)
```

Por padrão, **detectAllFaces** e **detectSingleFace** utilizam o detector facial SSD MobileNet V1. Você pode escolher outro detector fornecendo o objeto de opções correspondente:

``` javascript
const detections1 = await faceapi.detectAllFaces(input, new faceapi.SsdMobilenetv1Options())
const detections2 = await faceapi.detectAllFaces(input, new faceapi.TinyFaceDetectorOptions())
```

Você pode configurar as opções de cada detector facial conforme explicado [aqui](#getting-started-face-detection-options).

### Detectando 68 pontos de referência faciais

**Após detectar os rostos, podemos identificar os pontos de referência faciais de cada um da seguinte maneira:**

Detecta todos os rostos de uma imagem e calcula os 68 pontos de referência faciais de cada um. Retorna **Array<[WithFaceLandmarks<WithFaceDetection<{}>>](#getting-started-utility-classes)>**:

``` javascript
const detectionsWithLandmarks = await faceapi.detectAllFaces(input).withFaceLandmarks()
```

Detecta o rosto com a maior pontuação de confiança e calcula seus 68 pontos de referência faciais. Retorna **[WithFaceLandmarks<WithFaceDetection<{}>>](#getting-started-utility-classes) | undefined**:

``` javascript
const detectionWithLandmarks = await faceapi.detectSingleFace(input).withFaceLandmarks()
```

Também é possível usar o modelo leve (*tiny*) em vez do modelo padrão:

``` javascript
const useTinyModel = true
const detectionsWithLandmarks = await faceapi.detectAllFaces(input).withFaceLandmarks(useTinyModel)
```

### Calculando descritores faciais

**Após a detecção dos rostos e dos pontos de referência faciais, podemos calcular os descritores de cada rosto:**

Detecta todos os rostos de uma imagem, calcula os 68 pontos de referência e gera os descritores faciais. Retorna **Array<[WithFaceDescriptor<WithFaceLandmarks<WithFaceDetection<{}>>>](#getting-started-utility-classes)>**:

``` javascript
const results = await faceapi.detectAllFaces(input).withFaceLandmarks().withFaceDescriptors()
```

Detecta o rosto com a maior pontuação de confiança, calcula seus 68 pontos de referência e gera seu descritor facial. Retorna **[WithFaceDescriptor<WithFaceLandmarks<WithFaceDetection<{}>>>](#getting-started-utility-classes) | undefined**:

``` javascript
const result = await faceapi.detectSingleFace(input).withFaceLandmarks().withFaceDescriptor()
```

### Reconhecendo expressões faciais

**O reconhecimento de expressões faciais pode ser realizado nos rostos detectados da seguinte maneira:**

Detecta todos os rostos de uma imagem e identifica as expressões faciais de cada um. Retorna **Array<[WithFaceExpressions<WithFaceLandmarks<WithFaceDetection<{}>>>](#getting-started-utility-classes)>**:

``` javascript
const detectionsWithExpressions = await faceapi.detectAllFaces(input).withFaceLandmarks().withFaceExpressions()
```

Detecta o rosto com a maior pontuação de confiança e identifica suas expressões faciais. Retorna **[WithFaceExpressions<WithFaceLandmarks<WithFaceDetection<{}>>>](#getting-started-utility-classes) | undefined**:

``` javascript
const detectionWithExpressions = await faceapi.detectSingleFace(input).withFaceLandmarks().withFaceExpressions()
```

**Também é possível omitir .withFaceLandmarks(), dispensando a etapa de alinhamento facial (o que pode reduzir a precisão):**

Detecta todos os rostos sem alinhamento facial e identifica suas expressões. Retorna **Array<[WithFaceExpressions<WithFaceDetection<{}>>](#getting-started-utility-classes)>**:

``` javascript
const detectionsWithExpressions = await faceapi.detectAllFaces(input).withFaceExpressions()
```

Detecta o rosto com a maior pontuação de confiança, sem alinhamento facial, e identifica sua expressão. Retorna **[WithFaceExpressions<WithFaceDetection<{}>>](#getting-started-utility-classes) | undefined**:

``` javascript
const detectionWithExpressions = await faceapi.detectSingleFace(input).withFaceExpressions()
```

### Estimativa de idade e classificação de gênero

**A estimativa de idade e a classificação de gênero a partir dos rostos detectados podem ser feitas da seguinte maneira:**

Detecta todos os rostos de uma imagem, estima a idade e classifica o gênero de cada rosto. Retorna **Array<[WithAge<WithGender<WithFaceLandmarks<WithFaceDetection<{}>>>>](#getting-started-utility-classes)>**:

``` javascript
const detectionsWithAgeAndGender = await faceapi.detectAllFaces(input).withFaceLandmarks().withAgeAndGender()
```

Detecta o rosto com a maior pontuação de confiança, estima sua idade e classifica seu gênero. Retorna **[WithAge<WithGender<WithFaceLandmarks<WithFaceDetection<{}>>>>](#getting-started-utility-classes) | undefined**:

``` javascript
const detectionWithAgeAndGender = await faceapi.detectSingleFace(input).withFaceLandmarks().withAgeAndGender()
```

**Também é possível omitir .withFaceLandmarks(), dispensando a etapa de alinhamento facial (o que pode reduzir a precisão):**

Detecta todos os rostos sem alinhamento facial, estima a idade e classifica o gênero de cada um. Retorna **Array<[WithAge<WithGender<WithFaceDetection<{}>>>](#getting-started-utility-classes)>**:

``` javascript
const detectionsWithAgeAndGender = await faceapi.detectAllFaces(input).withAgeAndGender()
```

Detecta o rosto com a maior pontuação de confiança, sem alinhamento facial, estima sua idade e classifica seu gênero. Retorna **[WithAge<WithGender<WithFaceDetection<{}>>>](#getting-started-utility-classes) | undefined**:

``` javascript
const detectionWithAgeAndGender = await faceapi.detectSingleFace(input).withAgeAndGender()
```

### Combinação de tarefas

**As tarefas podem ser combinadas da seguinte maneira:**

``` javascript
// todos os rostos
await faceapi.detectAllFaces(input)
await faceapi.detectAllFaces(input).withFaceExpressions()
await faceapi.detectAllFaces(input).withFaceLandmarks()
await faceapi.detectAllFaces(input).withFaceLandmarks().withFaceExpressions()
await faceapi.detectAllFaces(input).withFaceLandmarks().withFaceExpressions().withFaceDescriptors()
await faceapi.detectAllFaces(input).withFaceLandmarks().withAgeAndGender().withFaceDescriptors()
await faceapi.detectAllFaces(input).withFaceLandmarks().withFaceExpressions().withAgeAndGender().withFaceDescriptors()

// um único rosto
await faceapi.detectSingleFace(input)
await faceapi.detectSingleFace(input).withFaceExpressions()
await faceapi.detectSingleFace(input).withFaceLandmarks()
await faceapi.detectSingleFace(input).withFaceLandmarks().withFaceExpressions()
await faceapi.detectSingleFace(input).withFaceLandmarks().withFaceExpressions().withFaceDescriptor()
await faceapi.detectSingleFace(input).withFaceLandmarks().withAgeAndGender().withFaceDescriptor()
await faceapi.detectSingleFace(input).withFaceLandmarks().withFaceExpressions().withAgeAndGender().withFaceDescriptor()
```

### Reconhecimento facial por comparação de descritores

Para realizar o reconhecimento facial, é possível usar faceapi.FaceMatcher para comparar os descritores faciais de referência com os descritores de um rosto a identificar.

Primeiro, inicializamos o FaceMatcher com os dados de referência. Por exemplo, podemos detectar os rostos em uma **referenceImage** e comparar seus descritores com os rostos de outras imagens:

``` javascript
const results = await faceapi
  .detectAllFaces(referenceImage)
  .withFaceLandmarks()
  .withFaceDescriptors()

if (!results.length) {
  return
}

// cria o FaceMatcher com rótulos atribuídos automaticamente
// com base nos resultados da detecção da imagem de referência
const faceMatcher = new faceapi.FaceMatcher(results)
```

Agora, podemos reconhecer o rosto de uma pessoa presente em **queryImage1**:

``` javascript
const singleResult = await faceapi
  .detectSingleFace(queryImage1)
  .withFaceLandmarks()
  .withFaceDescriptor()

if (singleResult) {
  const bestMatch = faceMatcher.findBestMatch(singleResult.descriptor)
  console.log(bestMatch.toString())
}
```

Ou podemos reconhecer todos os rostos presentes em **queryImage2**:

``` javascript
const results = await faceapi
  .detectAllFaces(queryImage2)
  .withFaceLandmarks()
  .withFaceDescriptors()

results.forEach(fd => {
  const bestMatch = faceMatcher.findBestMatch(fd.descriptor)
  console.log(bestMatch.toString())
})
```

Também é possível criar descritores de referência identificados por rótulos, como neste exemplo:

``` javascript
const labeledDescriptors = [
  new faceapi.LabeledFaceDescriptors(
    'obama',
    [descriptorObama1, descriptorObama2]
  ),
  new faceapi.LabeledFaceDescriptors(
    'trump',
    [descriptorTrump]
  )
]

const faceMatcher = new faceapi.FaceMatcher(labeledDescriptors)
```

<a name="getting-started-displaying-detection-results"></a>

## Exibindo os resultados da detecção

Preparando um elemento canvas sobreposto à imagem:

``` javascript
const displaySize = { width: input.width, height: input.height }
// ajusta o tamanho do canvas sobreposto às dimensões da entrada
const canvas = document.getElementById('overlay')
faceapi.matchDimensions(canvas, displaySize)
```

O face-api.js oferece algumas funções de alto nível prontas para desenhar os resultados:

``` javascript
/* Exibe os retângulos delimitadores dos rostos detectados */
const detections = await faceapi.detectAllFaces(input)
// ajusta os retângulos se a imagem exibida tiver tamanho diferente da original
const resizedDetections = faceapi.resizeResults(detections, displaySize)
// desenha as detecções no canvas
faceapi.draw.drawDetections(canvas, resizedDetections)

/* Exibe os pontos de referência faciais */
const detectionsWithLandmarks = await faceapi
  .detectAllFaces(input)
  .withFaceLandmarks()
// ajusta os retângulos e pontos de referência se a imagem exibida tiver tamanho diferente da original
const resizedResults = faceapi.resizeResults(detectionsWithLandmarks, displaySize)
// desenha as detecções no canvas
faceapi.draw.drawDetections(canvas, resizedResults)
// desenha os pontos de referência no canvas
faceapi.draw.drawFaceLandmarks(canvas, resizedResults)


/* Exibe os resultados do reconhecimento de expressões faciais */
const detectionsWithExpressions = await faceapi
  .detectAllFaces(input)
  .withFaceLandmarks()
  .withFaceExpressions()
// ajusta os retângulos e pontos de referência se a imagem exibida tiver tamanho diferente da original
const resizedResults = faceapi.resizeResults(detectionsWithExpressions, displaySize)
// desenha as detecções no canvas
faceapi.draw.drawDetections(canvas, resizedResults)
// desenha no canvas uma caixa de texto com as expressões faciais acima da probabilidade mínima
const minProbability = 0.05
faceapi.draw.drawFaceExpressions(canvas, resizedResults, minProbability)
```

Você também pode desenhar retângulos com textos personalizados ([DrawBox](https://github.com/justadudewhohacks/tfjs-image-recognition-base/blob/master/src/draw/DrawBox.ts)):

``` javascript
const box = { x: 50, y: 50, width: 100, height: 100 }
// veja as opções de DrawBox abaixo
const drawOptions = {
  label: 'Hello I am a box!',
  lineWidth: 2
}
const drawBox = new faceapi.draw.DrawBox(box, drawOptions)
drawBox.draw(document.getElementById('myCanvas'))
```

Opções de desenho do DrawBox:

``` javascript
export interface IDrawBoxOptions {
  boxColor?: string
  lineWidth?: number
  drawLabelOptions?: IDrawTextFieldOptions
  label?: string
}
```

Por fim, você pode desenhar campos de texto personalizados ([DrawTextField](https://github.com/justadudewhohacks/tfjs-image-recognition-base/blob/master/src/draw/DrawTextField.ts)):

``` javascript
const text = [
  'This is a textline!',
  'This is another textline!'
]
const anchor = { x: 200, y: 200 }
// veja as opções de DrawTextField abaixo
const drawOptions = {
  anchorPosition: 'TOP_LEFT',
  backgroundColor: 'rgba(0, 0, 0, 0.5)'
}
const drawBox = new faceapi.draw.DrawTextField(text, anchor, drawOptions)
drawBox.draw(document.getElementById('myCanvas'))
```

Opções de desenho do DrawTextField:

``` javascript
export interface IDrawTextFieldOptions {
  anchorPosition?: AnchorPosition
  backgroundColor?: string
  fontColor?: string
  fontSize?: number
  fontStyle?: string
  padding?: number
}

export enum AnchorPosition {
  TOP_LEFT = 'TOP_LEFT',
  TOP_RIGHT = 'TOP_RIGHT',
  BOTTOM_LEFT = 'BOTTOM_LEFT',
  BOTTOM_RIGHT = 'BOTTOM_RIGHT'
}
```

<a name="getting-started-face-detection-options"></a>

## Opções de detecção facial

### SsdMobilenetv1Options

``` javascript
export interface ISsdMobilenetv1Options {
  // limiar mínimo de confiança
  // padrão: 0.5
  minConfidence?: number

  // número máximo de rostos retornados
  // padrão: 100
  maxResults?: number
}

// exemplo
const options = new faceapi.SsdMobilenetv1Options({ minConfidence: 0.8 })
```

### TinyFaceDetectorOptions

``` javascript
export interface ITinyFaceDetectorOptions {
  // tamanho usado para processar a imagem: quanto menor, mais rápido,
  // porém menos preciso para detectar rostos pequenos; deve ser divisível
  // por 32. Tamanhos comuns: 128, 160, 224, 320, 416, 512, 608.
  // para rastreamento facial pela webcam, recomendo tamanhos menores,
  // como 128 ou 160; para detectar rostos menores, use 512 ou 608
  // padrão: 416
  inputSize?: number

  // limiar mínimo de confiança
  // padrão: 0.5
  scoreThreshold?: number
}

// exemplo
const options = new faceapi.TinyFaceDetectorOptions({ inputSize: 320 })
```

<a name="getting-started-utility-classes"></a>

## Classes utilitárias

### IBox

``` javascript
export interface IBox {
  x: number
  y: number
  width: number
  height: number
}
```

### IFaceDetection

``` javascript
export interface IFaceDetection {
  score: number
  box: Box
}
```

### IFaceLandmarks

``` javascript
export interface IFaceLandmarks {
  positions: Point[]
  shift: Point
}
```

### WithFaceDetection

``` javascript
export type WithFaceDetection<TSource> = TSource & {
  detection: FaceDetection
}
```

### WithFaceLandmarks

``` javascript
export type WithFaceLandmarks<TSource> = TSource & {
  unshiftedLandmarks: FaceLandmarks
  landmarks: FaceLandmarks
  alignedRect: FaceDetection
}
```

### WithFaceDescriptor

``` javascript
export type WithFaceDescriptor<TSource> = TSource & {
  descriptor: Float32Array
}
```

### WithFaceExpressions

``` javascript
export type WithFaceExpressions<TSource> = TSource & {
  expressions: FaceExpressions
}
```

### WithAge

``` javascript
export type WithAge<TSource> = TSource & {
  age: number
}
```

### WithGender

``` javascript
export type WithGender<TSource> = TSource & {
  gender: Gender
  genderProbability: number
}

export enum Gender {
  FEMALE = 'female',
  MALE = 'male'
}
```

<a name="getting-started-other-useful-utility"></a>

## Outros recursos úteis

### Usando a API de baixo nível

Em vez de usar a API de alto nível, você pode chamar diretamente os métodos de inferência (*forward*) de cada rede neural:

``` javascript
const detections1 = await faceapi.ssdMobilenetv1(input, options)
const detections2 = await faceapi.tinyFaceDetector(input, options)
const landmarks1 = await faceapi.detectFaceLandmarks(faceImage)
const landmarks2 = await faceapi.detectFaceLandmarksTiny(faceImage)
const descriptor = await faceapi.computeFaceDescriptor(alignedFaceImage)
```

### Extraindo uma região de imagem para um canvas

``` javascript
const regionsToExtract = [
  new faceapi.Rect(0, 0, 100, 100)
]
// extractFaces foi criado para extrair regiões faciais a partir de retângulos delimitadores,
// mas também pode ser usado para extrair qualquer outra região
const canvases = await faceapi.extractFaces(input, regionsToExtract)
```

### Distância euclidiana

``` javascript
// calcula a distância euclidiana entre dois descritores faciais
const dist = faceapi.euclideanDistance([0, 0], [0, 10])
console.log(dist) // 10
```

### Obtendo os pontos de referência e os contornos faciais

``` javascript
const landmarkPositions = landmarks.positions

// ou obtém as posições de contornos específicos,
// disponíveis apenas para o modelo de 68 pontos (FaceLandmarks68)
const jawOutline = landmarks.getJawOutline()
const nose = landmarks.getNose()
const mouth = landmarks.getMouth()
const leftEye = landmarks.getLeftEye()
const rightEye = landmarks.getRightEye()
const leftEyeBbrow = landmarks.getLeftEyeBrow()
const rightEyeBrow = landmarks.getRightEyeBrow()
```

### Buscando e exibindo imagens a partir de uma URL

``` html
<img id="myImg" src="">
```

``` javascript
const image = await faceapi.fetchImage('/images/example.png')

console.log(image instanceof HTMLImageElement) // true

// exibe a imagem obtida
const myImg = document.getElementById('myImg')
myImg.src = image.src
```

### Buscando dados JSON

``` javascript
const json = await faceapi.fetchJson('/files/example.json')
```

### Criando um seletor de imagens

``` html
<img id="myImg" src="">
<input id="myFileUpload" type="file" onchange="uploadImage()" accept=".jpg, .jpeg, .png">
```

``` javascript
async function uploadImage() {
  const imgFile = document.getElementById('myFileUpload').files[0]
  // cria um HTMLImageElement a partir de um Blob
  const img = await faceapi.bufferToImage(imgFile)
  document.getElementById('myImg').src = img.src
}
```

### Criando um canvas a partir de uma imagem ou de um vídeo

``` html
<img id="myImg" src="images/example.png" />
<video id="myVideo" src="media/example.mp4" />
```

``` javascript
const canvas1 = faceapi.createCanvasFromMedia(document.getElementById('myImg'))
const canvas2 = faceapi.createCanvasFromMedia(document.getElementById('myVideo'))
```

<a name="models"></a>

# Available Models

<a name="models-face-detection"></a>

## Face Detection Models

### SSD Mobilenet V1

For face detection, this project implements a SSD (Single Shot Multibox Detector) based on MobileNetV1. The neural net will compute the locations of each face in an image and will return the bounding boxes together with it's probability for each face. This face detector is aiming towards obtaining high accuracy in detecting face bounding boxes instead of low inference time. The size of the quantized model is about 5.4 MB (**ssd_mobilenetv1_model**).

The face detection model has been trained on the [WIDERFACE dataset](http://mmlab.ie.cuhk.edu.hk/projects/WIDERFace/) and the weights are provided by [yeephycho](https://github.com/yeephycho) in [this](https://github.com/yeephycho/tensorflow-face-detection) repo.

### Tiny Face Detector

The Tiny Face Detector is a very performant, realtime face detector, which is much faster, smaller and less resource consuming compared to the SSD Mobilenet V1 face detector, in return it performs slightly less well on detecting small faces. This model is extremely mobile and web friendly, thus it should be your GO-TO face detector on mobile devices and resource limited clients. The size of the quantized model is only 190 KB (**tiny_face_detector_model**).

The face detector has been trained on a custom dataset of ~14K images labeled with bounding boxes. Furthermore the model has been trained to predict bounding boxes, which entirely cover facial feature points, thus it in general produces better results in combination with subsequent face landmark detection than SSD Mobilenet V1.

This model is basically an even tinier version of Tiny Yolo V2, replacing the regular convolutions of Yolo with depthwise separable convolutions. Yolo is fully convolutional, thus can easily adapt to different input image sizes to trade off accuracy for performance (inference time).

<a name="models-face-landmark-detection"></a>

## 68 Point Face Landmark Detection Models

This package implements a very lightweight and fast, yet accurate 68 point face landmark detector. The default model has a size of only 350kb (**face_landmark_68_model**) and the tiny model is only 80kb (**face_landmark_68_tiny_model**). Both models employ the ideas of depthwise separable convolutions as well as densely connected blocks. The models have been trained on a dataset of ~35k face images labeled with 68 face landmark points.

<a name="models-face-recognition"></a>

## Face Recognition Model

For face recognition, a ResNet-34 like architecture is implemented to compute a face descriptor (a feature vector with 128 values) from any given face image, which is used to describe the characteristics of a persons face. The model is **not** limited to the set of faces used for training, meaning you can use it for face recognition of any person, for example yourself. You can determine the similarity of two arbitrary faces by comparing their face descriptors, for example by computing the euclidean distance or using any other classifier of your choice.

The neural net is equivalent to the **FaceRecognizerNet** used in [face-recognition.js](https://github.com/justadudewhohacks/face-recognition.js) and the net used in the [dlib](https://github.com/davisking/dlib/blob/master/examples/dnn_face_recognition_ex.cpp) face recognition example. The weights have been trained by [davisking](https://github.com/davisking) and the model achieves a prediction accuracy of 99.38% on the LFW (Labeled Faces in the Wild) benchmark for face recognition.

The size of the quantized model is roughly 6.2 MB (**face_recognition_model**).

<a name="models-face-expression-recognition"></a>

## Face Expression Recognition Model

The face expression recognition model is lightweight, fast and provides reasonable accuracy. The model has a size of roughly 310kb and it employs depthwise separable convolutions and densely connected blocks. It has been trained on a variety of images from publicly available datasets as well as images scraped from the web. Note, that wearing glasses might decrease the accuracy of the prediction results.

<a name="models-age-and-gender-recognition"></a>

## Age and Gender Recognition Model

The age and gender recognition model is a multitask network, which employs a feature extraction layer, an age regression layer and a gender classifier. The model has a size of roughly 420kb and the feature extractor employs a tinier but very similar architecture to Xception.

This model has been trained and tested on the following databases with an 80/20 train/test split each: UTK, FGNET, Chalearn, Wiki, IMDB*, CACD*, MegaAge, MegaAge-Asian. The `*` indicates, that these databases have been algorithmically cleaned up, since the initial databases are very noisy.

### Total Test Results

Total MAE (Mean Age Error): **4.54**

Total Gender Accuracy: **95%**

### Test results for each database

The `-` indicates, that there are no gender labels available for these databases.

Database        | UTK    | FGNET | Chalearn | Wiki | IMDB* | CACD* | MegaAge | MegaAge-Asian |
----------------|-------:|------:|---------:|-----:|------:|------:|--------:|--------------:|
MAE             | 5.25   | 4.23  | 6.24     | 6.54 | 3.63  | 3.20  | 6.23    | 4.21          |
Gender Accuracy | 0.93   | -     | 0.94     | 0.95 | -     | 0.97  | -       | -             |

### Test results for different age category groups

Age Range       | 0 - 3  | 4 - 8 | 9 - 18 | 19 - 28 | 29 - 40 | 41 - 60 | 60 - 80 | 80+     |
----------------|-------:|------:|-------:|--------:|--------:|--------:|--------:|--------:|
MAE             | 1.52   | 3.06  | 4.82   | 4.99    | 5.43    | 4.94    | 6.17    | 9.91    |
Gender Accuracy | 0.69   | 0.80  | 0.88   | 0.96    | 0.97    | 0.97    | 0.96    | 0.9     |
