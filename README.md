# CNN_malaria
CNN de classificação binária com dataset de imagens de células (malária)

Projeto desenvolvido para ser apresentado para apreciação da disciplina de Processamento Digital de Sinais do curso de Engenharia Elétrica do IFPB.

# Dataset utilizado

O dataset utilizado foi encontrado no [Kaggle](https://www.kaggle.com/iarunava/cell-images-for-detecting-malaria), contém 27.558 imagens, das quais foram divididas da seguinte forma:

	dataset1
	|
	├── valid		#Usado para validação
	|  |
	|  ├── Infectado	#2985 imagens
	|  |
	|  └── Desinfectado	#2761 imagens
	|  
	├── train
	|  |
	|  ├── Infectado	#10.789 imagens
	|  |
	|  └── Desinfectado	#12.372 imagens
	|
	└──test
	   └── test_folder	#Quantas imagens quiser

# Bibliotecas usadas

Para gerenciar os pacotes, foi usado o gerenciador Anaconda 3, com Python versão 3.5. Os seguintes pacotes foram utilizados:

	Numpy v.1.15.4
	
