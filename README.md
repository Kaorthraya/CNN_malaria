# CNN_malaria
CNN de classificação binária com dataset de imagens de células (malária)

Projeto desenvolvido para ser apresentado para apreciação da disciplina de Processamento Digital de Sinais do curso de Engenharia Elétrica do IFPB.

## Dataset utilizado

O dataset utilizado foi encontrado no [Kaggle](https://www.kaggle.com/iarunava/cell-images-for-detecting-malaria), contém 27.558 imagens, das quais foram divididas da seguinte forma:

	dataset1
	|
	├── valid		#Usado para validação
	|  |
	|  ├── Infectado	#3000 imagens
	|  |
	|  └── Desinfectado	#3000 imagens
	|  
	├── train
	|  |
	|  ├── Infectado	#10.779 imagens
	|  |
	|  └── Desinfectado	#10.778 imagens
	|
	└──test
	   └── test_folder	#Quantas imagens quiser

## Bibliotecas usadas

Para gerenciar os pacotes, foi usado o gerenciador Anaconda 3, com Python versão 3.5. Os seguintes pacotes foram utilizados:

	Numpy 1.15.4
	Keras 2.2.2
	Tensorflow 1.10.0
	Pandas 0.23.4
	Pillow 5.3.0

## Descrição do projeto

A rede neural utilizada neste projeto é classificada como CNN (Convolutional Neural Network). O modelo foi implementado via Keras com backend do TensorFlow, o modelo de ligações (inputs, hidden layers e neurons) é o modelo mais conhecido no tocante à CNN's em geral. O procedimento geral utilizado tanto para treino e para predição dos resultados está relatado abaixo.

### Treinamento

a) Foram importadas as bibliotecas principais do Keras

b) Definição dos caminhos das pastas de treino, validação e teste

c) Definição do tamanho do batch (máximo possível, a depender de quanta memória há disponível

d) Uso do keras.preprocessing.image.ImageDataGenerator() para fazer o processo conhecido como "data augmentation" para aumentar a variabilidade das imagens do dataset. As imagens para treino sofreram os seguintes desvios:

	d.1) 'reescale' para transformar os valores RGB de cada pixel num range de 0 a 1.
	d.2) 'shear_range' para um deslocamento vertical na imagem, de até 0.2 (escolhido aleatoriamente o angulo de deslizamento)
	d.3) 'zoom_range' para dar zoom na imagem de forma aleatória, variando de 1.2 a 0.8 da imagem original
	d.4) 'horizontal_flip'no caso, é falso. Para True, a imagem é rotacionada horizontalmente, de forma aleatória

e) Para a validação, o único desvio feito para a imagem é o 'reescale' pois otimiza o trabalho do TensorFlow, trabalhar com um range de 0 a 1.

f) O método flow_from_directory foi utilizada no ImageDataGenerator(), identificando as imagens em suas devidas classes e atribuindo-as a variável correspondente, que será usada posteriormente no treinamento. Também são definidos: shape (width, height) da imagem, o tamanho do batch que será usado na pasta, e o tipo de classe.

g) Definição do modelo a ser usado na rede neural. Usamos o modelo padrão para CNN's, que é convoluções seguidas de subsampling (pooling 2D) por 3 vezes e depois, uso de Dropout intercalados para evitar overfitting do aprendizado, e por último o Flatten, tornando tudo em um array de uma dimensão. O Dense faz a conexão homogênea entre os layers e os neurons. Função de ativação usada será sigmoid.

h) Para finalizar o modelo, é usado o método model.compile(), onde é indicado o tipo de loss que será calculado, o otimizador e a métrica de avaliação. Para este caso, o loss é do tipo 'binary_crossentropy' que é a indicada pela documentação do Keras para classificadores binários. O otimizador é o Adam, com learning rate (lr) de 0.001 (obtido através de testes) e a métrica é a acurácia, que reflete a quantidade de acertos que a rede neural está produzindo.

i) Definição de dois callbacks essenciais: EarlyStopping e ModelCheckpoint. 

j) O primeiro callback serve ao propósito de economia de tempo e processamento. Definindo a variável 'monitor' para o 'val_loss', este callback avaliará a cada epoch se o 'val_loss' está descrescendo (comportamento indicado pela variável 'mode=min'). A variável 'patience' indica quantas epochs será dadas de prazo até o callback agir e interromper o	treinamento

k) O segundo callback estabelece uma segurança adicional ao usuário, pois a cada epoch cria um checkpoint e salva o modelo (ou 	os pesos) para cada um. A variável 'monitor' indica ao callback qual métrica será usada para avaliar sua ação, nesse 		caso foi o valor do 'val_loss' e sempre observando sua diminuição ('mode = min'). 'save_best_only' se verdadeiro salva 		apenas o de melhor configuração, caso contrário: salva todos os checkpoints, diante o valor do 'monitor'. 			'save_weights_only' se verdadeiro salva apenas os pesos do modelo, caso contrário salva o modelo inteiro.

l) Por último, é chamado o método model.fit_generator(). A ele é passado o generator onde está os arquivos de treinamento, a quantidade de epochs a serem realizadas, os callbacks a serem incluídos, o generator que estão os arquivos de validação, quantidade de steps por cada epoch e a quantidade de steps para a validação, estes dois últimos foram obtidos experimentalmente, até um valor que o 'val_acc' e 'val_loss' ficassem razoáveis para a aplicação.

## Carregamento do modelo salvo e predição/conclusão

a) Carregamento das bibliotecas

b) Definição do caminho da pasta onde estão os arquivos da predição

c) Carregamento do modelo através do método model_load() e o modelo é atribuído à uma varíavel, também é impresso em tela o sumário do modelo, que também consta abaixo:

	_________________________________________________________________
	Layer (type)                 Output Shape              Param #   
	=================================================================
	conv2d_10 (Conv2D)           (None, 148, 148, 32)      896       
	_________________________________________________________________
	activation_16 (Activation)   (None, 148, 148, 32)      0         
	_________________________________________________________________
	max_pooling2d_10 (MaxPooling (None, 74, 74, 32)        0         
	_________________________________________________________________
	dropout_13 (Dropout)         (None, 74, 74, 32)        0         
	_________________________________________________________________
	conv2d_11 (Conv2D)           (None, 72, 72, 32)        9248      
	_________________________________________________________________
	activation_17 (Activation)   (None, 72, 72, 32)        0         
	_________________________________________________________________
	max_pooling2d_11 (MaxPooling (None, 36, 36, 32)        0         
	_________________________________________________________________
	dropout_14 (Dropout)         (None, 36, 36, 32)        0         
	_________________________________________________________________
	conv2d_12 (Conv2D)           (None, 34, 34, 32)        9248      
	_________________________________________________________________
	activation_18 (Activation)   (None, 34, 34, 32)        0         
	_________________________________________________________________
	max_pooling2d_12 (MaxPooling (None, 17, 17, 32)        0         
	_________________________________________________________________
	dropout_15 (Dropout)         (None, 17, 17, 32)        0         
	_________________________________________________________________
	flatten_4 (Flatten)          (None, 9248)              0         
	_________________________________________________________________
	dense_7 (Dense)              (None, 96)                887904    
	_________________________________________________________________
	activation_19 (Activation)   (None, 96)                0         
	_________________________________________________________________
	dropout_16 (Dropout)         (None, 96)                0         
	_________________________________________________________________
	dense_8 (Dense)              (None, 1)                 97        
	_________________________________________________________________
	activation_20 (Activation)   (None, 1)                 0         
	=================================================================
	Total params: 907,393
	Trainable params: 907,393
	Non-trainable params: 0
	_________________________________________________________________
	
d) É criado um gerador para a pasta de testes e respectivamente um flow_from_directory para reconhecer as imagens que estão lá e  ordená-las.

e) Antes da predição, é comum utilizar o método reset() para não obter valores errados durante a predição. Após isto, é chamado o método model.predict_generator(), onde é passado o gerador que contém as imagens para o teste.

f) A varíavel 'pred' recebe os valores de todas as predições em ordem alfabética de cada arquivo. Os valores recebidos em cada indíce do array é a probabilidade da imagem pertencer à classe tipo 1. A classe tipo 1 pode ser verificada diante o seguinte teste feito no arquivo de treinamento:

	In[ ]: train_generator.class_indices
	Out[ ]: {'Desparasitado': 0, 'Parasitado': 1}
	
Ou seja, quanto mais próxima a probabilidade é 1, indica que essa imagem é de uma célula com o parasita. Quanto mais próxima de 0, maior a probabilidade que não haja o parasita. Tendo essas condições em vista, um pequeno código é gerado para delimitar as probabilidades:
	
```python
name = np.empty([len(pred)],  dtype=object)
for i in range(len(name)) :
    if(pred[i] > 0.5):
        name[i] = 'Infectado'
    else:
        name[i] = 'Desinfectado'
```
Esse código gera um array com a classe pertencente à cada predição.

g) Então, fazemos a saída de uma forma mais amigável ao usuário utilizando um arquivo .xlsx, que depende da biblioteca openpyxl (lembre-se de instalar). Como as predições estão seguindo a ordem alfabética dos nomes dos arquivos e as conclusões feitas anteriormente seguem a ordem das predições, podemos imprimir o nome dos arquivos e suas respectivas predições sem nenhum tratamento anterior, apenas usando comandos da biblioteca pandas. A saída será feita no arquivo resultados.xlsx.
