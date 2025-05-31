## Redes Neurais Convulacionais em visão Computacional.

![Screenshot_20250531-144015](https://github.com/user-attachments/assets/381bc180-6da3-4a83-89a8-c425a4ed8178)


As redes neurais convulucionais (CNNs) são uma classe de modelos de deep learning especialmente projetados para lidar com dados que apresentam uma estrutura em grade – como imagens – e são fundamentais para muitas aplicações em visão computacional.

 Elas exploram a propriedade espacial dos dados utilizando operações de convolução para extrair automaticamente características visuais como bordas, texturas e formas. 

A seguir, explico em detalhes como funcionam essas redes e dou exemplos práticos para ilustrar seu uso.


## 1. Fundamentos das CNNs

### a) Operação de Convolução

- **Filtro (Kernel):**  
  Em uma camada convolucional, um filtro (ou kernel) é uma pequena matriz de pesos, por exemplo 3×3 ou 5×5, que é aplicada à imagem de entrada. Esse filtro "desliza" sobre a imagem (operando com um determinado *stride*, ou passo) e, em cada posição, calcula uma multiplicação elemento a elemento seguida de uma soma. Essa operação gera um valor que faz parte do *feature map* (mapa de características) da camada.  
  *Exemplo prático:* Imagine uma imagem de 5×5 pixels e um filtro de 3×3. Ao aplicar o filtro, a rede calcula uma soma ponderada dos pixels sob o filtro para cada posição válida da imagem. Se o filtro estiver ajustado para detectar bordas (por exemplo, um filtro de Sobel), o resultado destacará os contornos presentes na imagem.

- **Parâmetros Importantes:**  
  - **Stride:** Determina de quantos pixels o filtro é deslocado a cada passo. Um stride maior reduz as dimensões do mapa de saída.  
  - **Padding:** Adiciona bordas (geralmente zeros) à imagem para controlar o tamanho do mapa resultante e preservar as bordas da imagem.

### b) Função de Ativação

Após a operação de convolução, é comum aplicar uma função de ativação como a ReLU (Rectified Linear Unit).  
- **ReLU:** Define que todos os valores negativos do mapa de características passam a ser zero, mantendo a não linearidade, o que ajuda o modelo a aprender funções mais complexas.

### c) Camada de Pooling

Para reduzir as dimensões espaciais (reduzir o tamanho do feature map) e, ao mesmo tempo, manter as características mais importantes, utiliza-se a operação de pooling:
- **Max Pooling:** Seleciona o valor máximo dentro de uma janela definida (por exemplo, 2×2) e desloca a janela com um determinado stride.
- **Average Pooling:** Calcula a média dos valores da janela.

*Exemplo prático:* Em uma imagem de 28×28 pixels, após algumas camadas convolucionais, um max pooling com janela 2×2 reduz o tamanho do mapa de características pela metade (14×14), mantendo as áreas com maior ativação, que geralmente correspondem a características relevantes como bordas marcadas ou texturas distintas.

### d) Camadas Totalmente Conectadas

Após empilhar várias camadas de convolução e pooling, os mapas de características são “achatados” em um vetor unidimensional e passados para camadas densas (totalmente conectadas). Essa etapa é responsável pela decisão final (por exemplo, classificação da imagem em categorias).  
- **Softmax:** Em uma camada de saída para tarefas de classificação, a função softmax converte os valores em probabilidades para cada classe.



## 2. Exemplo Prático: Construção de uma CNN com Python e Keras

Imagine que queremos construir uma CNN simples para classificar imagens do conjunto de dados MNIST (imagens de dígitos manuscritos, 28×28 pixels). A seguir, veja um exemplo de código:

```python
import tensorflow as tf
from tensorflow.keras import layers, models
import matplotlib.pyplot as plt

# Carrega os dados MNIST
(train_images, train_labels), (test_images, test_labels) = tf.keras.datasets.mnist.load_data()

# Pré-processamento: Normalizar e adicionar canal de cor (1 canal, pois são imagens em escala de cinza)
train_images = train_images.reshape((60000, 28, 28, 1)).astype('float32') / 255.0
test_images = test_images.reshape((10000, 28, 28, 1)).astype('float32') / 255.0

# Construindo o modelo CNN
model = models.Sequential([
    # Primeira camada convolucional: extrai características basilares
    layers.Conv2D(32, (3, 3), activation='relu', input_shape=(28, 28, 1)),
    layers.MaxPooling2D((2, 2)),
    
    # Segunda camada convolucional: extrai características mais complexas
    layers.Conv2D(64, (3, 3), activation='relu'),
    layers.MaxPooling2D((2, 2)),
    
    # Terceira camada convolucional para aprofundar a extração
    layers.Conv2D(64, (3, 3), activation='relu'),
    
    # Achatar os dados (flatten) para conectá-los a uma rede densamente conectada
    layers.Flatten(),
    layers.Dense(64, activation='relu'),
    layers.Dense(10, activation='softmax')  # 10 classes para os 10 dígitos
])

# Compilando o modelo
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

# Treinando o modelo
history = model.fit(train_images, train_labels, epochs=5, 
                    validation_data=(test_images, test_labels))

# Avaliando o desempenho no conjunto de teste
test_loss, test_acc = model.evaluate(test_images, test_labels)
print(f"Precisão no conjunto de teste: {test_acc}")
```

**Explicação do Código:**

- **Camadas Convolucionais e Pooling:**  
  As primeiras camadas do modelo aplicam filtros 3×3 e usam ReLU para introduzir não linearidade. O max pooling reduz as dimensões espaciais mantendo as características mais importantes.

- **Camada de Flatten e Camadas Densas:**  
  Após as operações convolucionais, os dados são achatados e passados por uma camada densa, permitindo a combinação das características extraídas para a classificação final.

- **Saída Softmax:**  
  A camada final utiliza softmax para produzir uma distribuição de probabilidade sobre as 10 classes de dígitos.

Esse exemplo demonstra como os passos de convolução, ativação, pooling e a etapa final de classificação se combinam para formar uma CNN eficaz para uma tarefa prática de visão computacional.



## 3. Diagramas e Conexões Visuais (Representação ASCII)

Um diagrama simplificado da arquitetura de uma CNN seria:

```
[Imagem de Entrada]
       |
       v
[Camada de Convolução] ---> [Função de Ativação (ReLU)]
       |
       v
[Camada de Pooling]
       |
       v
(repetir as operações de convolução e pooling conforme necessário)
       |
       v
[Flatten (Achatar)]
       |
       v
[Camada Densa (Fully Connected)]
       |
       v
[Camada de Saída (Softmax para classificação)]
```

Cada bloco representa uma etapa de processamento que extrai e compila características cada vez mais abstratas da imagem.



## 4. Conclusão

As redes neurais convolucionais transformaram a visão computacional ao permitir a extração automática e hierárquica de características visuais, facilitando tarefas como classificação, detecção e segmentação de objetos. 

Com a combinação de operações de convolução, funções de ativação, pooling e camadas densas, as CNNs aprendem abstrações de baixo nível (como bordas e texturas) e as combinam para formar representações de alto nível (por exemplo, objetos ou cenas inteiras).

 Esse processo é exemplificado em aplicações que vão desde o reconhecimento de dígitos manuscritos até a condução autônoma de veículos.





 
