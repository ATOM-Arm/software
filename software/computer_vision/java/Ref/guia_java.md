# Guia Completo de Visão Computacional com Java (2025)

> Este guia prático e opinativo foi pensado para você que deseja **identificar, analisar e tratar imagens em Java** — do zero ao avançado. Traz conceitos, decisões de arquitetura, setup, exemplos de código, boas práticas, desempenho, integração com web e um roteiro de estudos com exercícios.

---

## 1) Visão geral e quando usar Java para visão computacional

**Por que Java?**
- Ecossistema maduro, performance estável na JVM e tooling robusto (Maven/Gradle, IDEs, testes).
- Integra bem com stacks corporativas (Spring Boot, Quarkus, Micronaut) e devops (Docker/K8s).
- Opções sólidas de bibliotecas: **OpenCV (via JavaCV/bytedeco)**, **BoofCV** (100% Java), **DL4J/DJL** para redes neurais, além do **OpenCV DNN** para inferência com modelos ONNX.

**Cenários onde Java brilha**
- Processamento de imagens/vídeos em pipelines de backend.
- Sistemas corporativos que precisam de **serviços REST** para detecção, OCR, classificação.
- Aplicações desktop/embedded que exigem **portabilidade** e **manutenção a longo prazo**.

**Cuidado**
- Ecossistema de pesquisa de ponta costuma chegar primeiro no Python. Em Java você consome modelos prontos (ONNX, TorchScript) e foca na **engenharia de produção**.

---

## 2) Stack recomendada

- **JDK 17+** (LTS). Se puder, use 21 LTS ou superior.
- **Maven** ou **Gradle** (qual preferir).
- **IDE**: IntelliJ IDEA (recomendado) ou VS Code.
- **Bibliotecas** (você pode combinar):
  - **OpenCV** (via `org.bytedeco:opencv-platform`) – geral, vídeo, clássicos, DNN.
  - **BoofCV** – 100% Java, leve, ótimos utilitários.
  - **DJL (Deep Java Library)** – wrapper de engines (PyTorch, TensorFlow, ONNXRuntime).
  - **DL4J** – deep learning em Java, treinamento e inferência.
  - **Tesseract (via JavaCPP/Bytedeco)** – OCR quando necessário.

---

## 3) Instalação e Setup

### 3.1 Maven (exemplos de dependências)

> **Dica**: sempre checar a versão mais recente no Maven Central. Aqui usamos placeholders `x.y.z` quando aplicável.

**OpenCV (bytedeco, multiplataforma):**
```xml
<dependency>
  <groupId>org.bytedeco</groupId>
  <artifactId>opencv-platform</artifactId>
  <version>4.10.0-1</version> <!-- verifique a última -->
</dependency>
```

**BoofCV (tudo em um):**
```xml
<dependency>
  <groupId>org.boofcv</groupId>
  <artifactId>boofcv-all</artifactId>
  <version>x.y.z</version>
</dependency>
```

**DJL (engine ONNXRuntime como exemplo):**
```xml
<dependency>
  <groupId>ai.djl</groupId>
  <artifactId>api</artifactId>
  <version>x.y.z</version>
</dependency>
<dependency>
  <groupId>ai.djl.onnxruntime</groupId>
  <artifactId>onnxruntime-engine</artifactId>
  <version>x.y.z</version>
</dependency>
```

**DL4J (opcional):**
```xml
<dependency>
  <groupId>org.deeplearning4j</groupId>
  <artifactId>deeplearning4j-core</artifactId>
  <version>x.y.z</version>
</dependency>
<dependency>
  <groupId>org.nd4j</groupId>
  <artifactId>nd4j-native-platform</artifactId>
  <version>x.y.z</version>
</dependency>
```

### 3.2 Gradle (Kotlin DSL, exemplo OpenCV)
```kts
dependencies {
    implementation("org.bytedeco:opencv-platform:4.10.0-1")
}
```

### 3.3 Testando o OpenCV
```java
import org.opencv.core.Core;

public class CheckOpenCV {
  static { System.loadLibrary(Core.NATIVE_LIBRARY_NAME); }
  public static void main(String[] args) {
    System.out.println("OpenCV: " + Core.getVersionString());
  }
}
```

> Se ocorrer `UnsatisfiedLinkError`, veja a seção **Problemas comuns**.

---

## 4) Fundamentos de processamento de imagem

### 4.1 Estruturas e conversões
- **OpenCV** usa `Mat` (matriz n-dimensional, BGR por padrão).
- GUI nativa do OpenCV em Java é limitada; para visualizar rapidamente use `HighGui.imshow` ou converta para `BufferedImage`.

**Utilitário: Mat ⇄ BufferedImage**
```java
import org.opencv.core.*;
import org.opencv.imgproc.Imgproc;
import java.awt.image.*;

public class Mats {
  public static BufferedImage toBufferedImage(Mat mat) {
    int type = BufferedImage.TYPE_BYTE_GRAY;
    if (mat.channels() == 3) type = BufferedImage.TYPE_3BYTE_BGR; // BGR -> BGR
    byte[] b = new byte[(int) (mat.total() * mat.channels())];
    mat.get(0, 0, b);
    BufferedImage img = new BufferedImage(mat.cols(), mat.rows(), type);
    img.getRaster().setDataElements(0, 0, mat.cols(), mat.rows(), b);
    return img;
  }
}
```

### 4.2 Carregar, salvar e mostrar
```java
import org.opencv.core.*;
import org.opencv.imgcodecs.Imgcodecs;
import org.opencv.imgproc.Imgproc;
import org.opencv.highgui.HighGui;

public class IOExample {
  static { System.loadLibrary(Core.NATIVE_LIBRARY_NAME); }
  public static void main(String[] args) {
    Mat img = Imgcodecs.imread("entrada.jpg");
    if (img.empty()) throw new RuntimeException("Falha ao carregar imagem");
    HighGui.imshow("Entrada", img);
    HighGui.waitKey(0);
    Imgcodecs.imwrite("saida.png", img);
  }
}
```

### 4.3 Espaços de cor
```java
Mat gray = new Mat();
Imgproc.cvtColor(img, gray, Imgproc.COLOR_BGR2GRAY);
Mat hsv = new Mat();
Imgproc.cvtColor(img, hsv, Imgproc.COLOR_BGR2HSV);
```

### 4.4 Filtros e convolução
```java
Mat blurred = new Mat();
Imgproc.GaussianBlur(gray, blurred, new Size(5,5), 0);
Mat edges = new Mat();
Imgproc.Canny(blurred, edges, 100, 200);
```

### 4.5 Limiarização (Threshold)
```java
Mat th = new Mat();
Imgproc.threshold(gray, th, 0, 255, Imgproc.THRESH_BINARY | Imgproc.THRESH_OTSU);
Mat thAdaptive = new Mat();
Imgproc.adaptiveThreshold(gray, thAdaptive, 255, Imgproc.ADAPTIVE_THRESH_MEAN_C,
                          Imgproc.THRESH_BINARY, 11, 2);
```

### 4.6 Morfologia
```java
Mat kernel = Imgproc.getStructuringElement(Imgproc.MORPH_RECT, new Size(3,3));
Mat opened = new Mat();
Imgproc.morphologyEx(th, opened, Imgproc.MORPH_OPEN, kernel);
Mat closed = new Mat();
Imgproc.morphologyEx(th, closed, Imgproc.MORPH_CLOSE, kernel);
```

### 4.7 Contornos e segmentação simples
```java
import java.util.*;

List<MatOfPoint> contours = new ArrayList<>();
Mat hierarchy = new Mat();
Imgproc.findContours(th, contours, hierarchy, Imgproc.RETR_EXTERNAL, Imgproc.CHAIN_APPROX_SIMPLE);
for (MatOfPoint c : contours) {
  Rect box = Imgproc.boundingRect(c);
  Imgproc.rectangle(img, box, new Scalar(0,255,0), 2);
}
```

### 4.8 Histograma e equalização
```java
Mat eq = new Mat();
Imgproc.equalizeHist(gray, eq);
```

---

## 5) Recursos clássicos: detecção e descrição

### 5.1 Bordas e cantos
```java
Mat edges = new Mat();
Imgproc.Canny(gray, edges, 50, 150);
Mat corners = new Mat();
Imgproc.cornerHarris(gray, corners, 2, 3, 0.04);
```

### 5.2 Detectores/Descritores (ORB/SIFT)
> **Nota**: SIFT e SURF ficam em `opencv-contrib`. Com bytedeco, inclua o artefato que já agrega contribs. ORB é livre e rápido.
```java
import org.opencv.features2d.*;

Feature2D orb = ORB.create(1000);
MatOfKeyPoint kps = new MatOfKeyPoint();
Mat desc = new Mat();
orb.detectAndCompute(gray, new Mat(), kps, desc);

DescriptorMatcher matcher = DescriptorMatcher.create(DescriptorMatcher.BRUTEFORCE_HAMMING);
// matcher.match(desc1, desc2, matches);
```

### 5.3 Correspondência e homografia (alinhamento)
```java
// Após obter matches bons (ex.: via Lowe ratio),
// calcule homografia com RANSAC para registrar imagens
Mat H = Calib3d.findHomography(srcPoints, dstPoints, Calib3d.RANSAC, 3.0);
```

---

## 6) Detecção e rastreamento de objetos (clássico)

### 6.1 Haar Cascades (faces)
```java
import org.opencv.objdetect.CascadeClassifier;

CascadeClassifier face = new CascadeClassifier("haarcascade_frontalface_default.xml");
MatOfRect faces = new MatOfRect();
face.detectMultiScale(gray, faces, 1.1, 3, 0, new Size(30,30), new Size());
for (Rect r : faces.toArray()) {
  Imgproc.rectangle(img, r, new Scalar(255,0,0), 2);
}
```

### 6.2 HOG + SVM (pedestres)
```java
HOGDescriptor hog = new HOGDescriptor();
hog.setSVMDetector(HOGDescriptor.getDefaultPeopleDetector());
MatOfRect found = new MatOfRect();
MatOfDouble weights = new MatOfDouble();
hog.detectMultiScale(img, found, weights);
```

### 6.3 Rastreamento (KCF/CSRT)
```java
import org.opencv.tracking.*;

Tracker tracker = TrackerKCF.create();
Rect2d roi = new Rect2d(100,100,80,80);
tracker.init(img, roi);
// em loop: tracker.update(frame, roi);
```

---

## 7) Deep Learning com Java

### 7.1 OpenCV DNN (carregar ONNX)
Utilize modelos prontos (YOLO, SSD, MobileNet) exportados para **ONNX**.
```java
import org.opencv.dnn.*;

Net net = Dnn.readNetFromONNX("modelo.onnx");
Mat blob = Dnn.blobFromImage(img, 1/255.0, new Size(640,640), new Scalar(0,0,0), true, false);
net.setInput(blob);
Mat out = net.forward();
// Faça a pós-processamento (confiança, NMS, conversão de coordenadas, etc.)
```

**Pós-processamento YOLO (esqueleto):**
```java
float confThresh = 0.25f, nms = 0.45f;
List<Rect> boxes = new ArrayList<>();
List<Float> scores = new ArrayList<>();
List<Integer> classes = new ArrayList<>();
// parse 'out' conforme o layout do seu modelo (yoloX tem formatos diferentes)
// depois use Dnn.NMSBoxes para suprimir sobreposições
MatOfRect mBoxes = new MatOfRect(boxes.toArray(new Rect[0]));
MatOfFloat mScores = new MatOfFloat(toFloatArray(scores));
MatOfInt indices = new MatOfInt();
Dnn.NMSBoxes(mBoxes, mScores, confThresh, nms, indices);
```

### 7.2 DJL (engine ONNXRuntime/PyTorch)
```java
import ai.djl.Model;
import ai.djl.inference.Predictor;
import ai.djl.modality.cv.ImageFactory;
import ai.djl.translate.TranslateException;

try (Model model = Model.newInstance("yolo", "OnnxRuntime")) {
  model.load(Paths.get("/path/para/modelo"));
  var imgDJL = ImageFactory.getInstance().fromFile(Paths.get("entrada.jpg"));
  try (Predictor<ai.djl.modality.cv.Image, DetectedObjects> predictor = model.newPredictor(new SeuTranslator())) {
    DetectedObjects det = predictor.predict(imgDJL);
  }
}
```
> O DJL oferece **Model Zoo** e tradutores prontos para tarefas comuns.

### 7.3 DL4J (classificação customizada)
- Útil se você precisa **treinar** em Java ou integrar pipelines ND4J.
- Fluxo: preparar `RecordReaderDataSetIterator` → definir rede (ComputationalGraph/MultiLayerNetwork) → treinar → salvar `.zip` do modelo → fazer inferência.

---

## 8) Vídeo e tempo real

### 8.1 Captura da webcam
```java
import org.opencv.videoio.VideoCapture;

VideoCapture cap = new VideoCapture(0); // ou caminho de arquivo
if (!cap.isOpened()) throw new RuntimeException("Sem câmera");
Mat frame = new Mat();
while (cap.read(frame)) {
  // processar frame (detecção, rastreio, etc.)
  HighGui.imshow("Cam", frame);
  if (HighGui.waitKey(1) == 27) break; // ESC
}
cap.release();
```

### 8.2 Dicas
- **Threads**: capture e processamento em threads separadas.
- **Batching**: se a latência permitir, agrupe frames.
- **Frame skipping** sob carga alta.

---

## 9) Desempenho e produção

- **Reutilize `Mat`** e buffers; evite criar/descartar em loops.
- **Libere nativos**: em imagens grandes e laços prolongados, use `mat.release()` quando apropriado.
- **JNI overhead**: minimize idas/voltas Java↔nativo; prefira operações vetorizadas.
- **OpenCV com CUDA**: se precisar de GPU, use build com CUDA (bytedeco fornece variantes) e funções `cuda::*`.
- **JVM**: usar LTS recente, `-Xms/-Xmx` adequados, **G1/ ZGC** conforme perfil.
- **Vector API (JDK 21+)**: para seus kernels customizados em Java puro.
- **Profiling**: Java Flight Recorder, Async Profiler.

---

## 10) Integração com backend (REST)

### 10.1 Serviço Spring Boot para detecção
**Controller (esqueleto):**
```java
@RestController
@RequestMapping("/api")
public class DetectController {
  private final Net net; // OpenCV DNN carregado no startup

  public DetectController(Net net) { this.net = net; }

  @PostMapping(value = "/detect", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  public ResponseEntity<List<Detection>> detect(@RequestPart("file") MultipartFile file) throws Exception {
    Mat img = Imgcodecs.imdecode(new MatOfByte(file.getBytes()), Imgcodecs.IMREAD_COLOR);
    Mat out = infer(net, img);
    List<Detection> dets = postprocess(out, img.size());
    return ResponseEntity.ok(dets);
  }
}
```

**Boas práticas**
- Instancie modelos **uma única vez** (singleton/bean). Evite carregar por requisição.
- Limite de tamanho de upload, timeouts e **fila de trabalho**.
- Métricas (Micrometer/Prometheus) e logs estruturados.

---

## 11) Testes e avaliação

- **Unitários**: valide cada etapa (ex.: threshold gera % de pixels brancos esperado).
- **Integração**: compare saída com imagens de referência (regressão visual simples).
- **Métricas**: precisão/recall/F1, mAP (objetos), SSIM/PSNR (qualidade), tempo por frame/TPM.
- **Datasets**: divida em treino/validação/teste. Documente vieses.

---

## 12) Segurança, ética e LGPD (Brasil)

- **Privacidade**: minimize dados pessoais, descarte frames quando possível.
- **Consentimento**: sinalize captura em ambientes monitorados.
- **Vieses**: avalie desempenho por subgrupos quando aplicável.
- **Explainability**: registre versões de modelo, thresholds e critérios.

---

## 13) Roteiro de estudos (30 dias)

**Semana 1 – Fundamentos**
- Instalação (OpenCV/BoofCV). IO, cores, filtro, threshold, morfologia.
- Exercícios 1–4.

**Semana 2 – Recursos clássicos**
- Bordas, cantos, ORB/SIFT, homografia, contornos.
- Exercícios 5–8.

**Semana 3 – Vídeo e rastreamento**
- Webcam, KCF/CSRT, fluxo de trabalho em threads.
- Exercícios 9–11.

**Semana 4 – Deep Learning e backend**
- YOLO/SSD via DNN/DJL, pós-processamento e REST.
- Exercícios 12–15.

---

## 14) Exercícios práticos (com enunciados objetivos)

1. **Conversões e histograma**: carregue imagem, converta para cinza/HSV e equalize histograma. Salve saídas.
2. **Filtro e Canny**: compare Canny com e sem blur; meça proporção de pixels de borda.
3. **Threshold adaptativo vs Otsu**: escolha melhor para um lote de imagens com iluminação desigual.
4. **Morfologia**: use abertura/fechamento para remover ruído em documentos digitalizados.
5. **Contornos**: conte objetos em uma cena e calcule áreas médias/desvio padrão.
6. **ORB**: detecte keypoints e faça matching entre duas cenas do mesmo objeto.
7. **Homografia**: faça retificação de um pôster fotografado em perspectiva.
8. **Segmentação por cor (HSV)**: isole objetos vermelhos sob variação de luz.
9. **Rastreamento KCF**: rastreie um objeto em vídeo; reporte FPS.
10. **Detecção de pedestre (HOG)** em vídeo curto.
11. **Estabilização simples**: use recursos + homografia para reduzir tremor.
12. **Inferência YOLO (ONNX)**: rode detecção, compute mAP em mini-dataset.
13. **Classificação DJL**: use um modelo do Model Zoo para top-1/top-5.
14. **Serviço REST**: implemente `/detect` (multipart) com saída JSON.
15. **Benchmark**: compare latência de 3 pipelines (clássico vs DNN vs DJL) em 100 imagens.

---

## 15) Problemas comuns e soluções

**Erro `UnsatisfiedLinkError` com OpenCV**
- Garanta que `opencv-platform` (bytedeco) está no classpath; ele carrega nativos para seu SO automaticamente.
- Evite misturar builds de SO diferentes ou libs antigas no PATH.

**`VideoCapture` não abre webcam**
- Teste com índice 0/1/2. Feche apps que usam a câmera. Em Linux, cheque permissões de vídeo.

**Desempenho baixo no DNN**
- Use **tamanho de entrada** conforme o modelo (ex.: 640×640).
- Ative **CUDA** se disponível ou use DJL com ONNXRuntime GPU.
- Reaproveite blob, evite recodificar cores a cada frame.

**Resultados instáveis do YOLO**
- Ajuste **thresholds** (confiança/NMS). Normalize para o mesmo range usado no treino.
- Confirme o **layout** da saída (cada release de YOLO pode mudar formato).

**Diferenças de cores (BGR vs RGB)**
- OpenCV é **BGR** por padrão. Converta para RGB ao exibir com bibliotecas Java padrão.

---

## 16) Boas práticas de projeto

- **Separação**: camadas para IO, pré-processamento, inferência e pós-processamento.
- **Imutabilidade lógica**: evite mutar a imagem de entrada; trabalhe com cópias estratégicas.
- **Config centralizada**: thresholds, caminhos de modelo, tamanhos de entrada, em `application.yaml`.
- **Observabilidade**: métricas (FPS, latência p95/p99), tracing e logs de amostras (não sensíveis).
- **Versionamento**: carimbe a versão do modelo no output.

---

## 17) Snippets úteis

### 17.1 Desenho de caixas e labels
```java
void drawBox(Mat img, Rect box, String label) {
  Imgproc.rectangle(img, box, new Scalar(0,255,0), 2);
  int base[] = new int[1];
  Size t = Imgproc.getTextSize(label, Imgproc.FONT_HERSHEY_SIMPLEX, 0.5, 1, base);
  Imgproc.rectangle(img, new Point(box.x, box.y - t.height - 4), new Point(box.x + t.width, box.y), new Scalar(0,255,0), Imgproc.FILLED);
  Imgproc.putText(img, label, new Point(box.x, box.y - 2), Imgproc.FONT_HERSHEY_SIMPLEX, 0.5, new Scalar(0,0,0), 1);
}
```

### 17.2 NMS com OpenCV
```java
MatOfInt indices = new MatOfInt();
Dnn.NMSBoxes(mBoxes, mScores, confThresh, nms, indices);
for (int idx : indices.toArray()) {
  Rect b = boxes.get(idx);
  float s = scores.get(idx);
  int cls = classes.get(idx);
  // desenhar/registrar
}
```

### 17.3 Timer simples
```java
long t0 = System.nanoTime();
// process
double ms = (System.nanoTime() - t0)/1e6;
```

---

## 18) Modelo de `pom.xml` inicial (OpenCV + Spring Boot)

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.exemplo</groupId>
  <artifactId>cv-service</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <properties>
    <java.version>21</java.version>
    <spring-boot.version>3.3.0</spring-boot.version>
  </properties>
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>${spring-boot.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.bytedeco</groupId>
      <artifactId>opencv-platform</artifactId>
      <version>4.10.0-1</version>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

---

## 19) Checklist de produção

- [ ] Modelos carregados no startup (singleton) e warmup executado.
- [ ] Limites de upload (MB), timeouts e fila de processamento.
- [ ] Observabilidade (métricas, logs, traces) e dashboards.
- [ ] Testes de carga (picos e endurance) e orçamento de latência.
- [ ] Política de retenção/anonimização de imagens/vídeos.

---

## 20) Próximos passos e recursos

- Documentação oficial do OpenCV (Java), BoofCV, DJL e DL4J.
- Repositórios de modelos (ONNX Model Zoo, DJL Model Zoo).
- Papers/e-books sobre visão clássica (Gonzalez & Woods) e detecção moderna (YOLO, SSD, Faster R-CNN).

---

### Conclusão
Com Java você constrói **pipelines robustos** de visão computacional em produção: do pré-processamento clássico ao deep learning, expondo como serviços escaláveis. Use OpenCV para o grosso, DJL/ONNX quando precisar de modelos modernos, e BoofCV para utilidades em Java puro. O resto é engenharia e boas práticas.

