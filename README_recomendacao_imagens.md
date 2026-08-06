# Sistema de Recomendação por Imagens (Visual Similarity Recommender)

## 📒 Descrição
Sistema de recomendação que sugere produtos com base na **similaridade visual**
(formato, cor, textura) e não em dados textuais como preço, marca ou modelo.
O objetivo é simular um cenário de e-commerce em que, ao buscar um produto,
o sistema recomenda outros itens visualmente parecidos — mesmo que pertençam
a lojas, marcas ou faixas de preço diferentes.

## 🤖 Tecnologias Utilizadas
- **Python 3**
- **TensorFlow / Keras** — rede ResNet50 pré-treinada (ImageNet) usada como extratora de características (feature extractor)
- **scikit-learn** — `NearestNeighbors` com métrica de cosseno para o motor de similaridade
- **Matplotlib / Pillow** — visualização dos resultados
- **Google Colab** — ambiente de execução recomendado

## 🧐 Processo de Criação
1. **Extração de características**: cada imagem do catálogo é processada pela
   ResNet50 sem a camada de classificação final (`include_top=False`), gerando
   um vetor (embedding) de 2048 dimensões que representa sua aparência visual.
2. **Indexação**: todos os embeddings do catálogo são armazenados em uma matriz
   e indexados com `NearestNeighbors` (métrica de cosseno).
3. **Consulta (query)**: ao receber uma nova imagem, o sistema extrai seu
   embedding da mesma forma e busca os *k* vizinhos mais próximos no espaço
   vetorial — ou seja, as imagens do catálogo mais parecidas visualmente.
4. **Avaliação**: como uma checagem de sanidade, medi a proporção de
   recomendações que caem na mesma classe/categoria da imagem de consulta
   (Precisão@5), assumindo que produtos da mesma categoria tendem a ser
   visualmente mais parecidos entre si.

O notebook completo com todo o código está em [`sistema_recomendacao_imagens.ipynb`](./sistema_recomendacao_imagens.ipynb).

## 🚀 Resultados
- O sistema consegue agrupar corretamente produtos visualmente parecidos
  mesmo sem nenhuma informação textual (preço, marca, categoria explícita).
- Em muitos casos, itens da mesma classe (ex: tênis com tênis) aparecem entre
  os primeiros resultados, mas o sistema também recomenda itens de classes
  diferentes quando o formato/cor é muito parecido (ex: um relógio redondo
  recomendando um objeto redondo de outra categoria) — o que é esperado, já
  que o modelo não enxerga rótulos, apenas pixels.
- A abordagem generaliza bem sem necessidade de re-treinamento, graças ao
  uso de *transfer learning* com a ResNet50 pré-treinada no ImageNet.

## 💭 Reflexão
Esse tipo de sistema é a base de funcionalidades como "produtos parecidos" ou
"busca por imagem" em grandes e-commerces (ex: Pinterest Lens, Amazon "shop
similar looks"). A grande vantagem é não depender de metadados bem
cadastrados — algo raro na prática, onde descrições de produto são
inconsistentes entre lojas e vendedores. Em contrapartida, o sistema é
sensível à qualidade e enquadramento das imagens, e não entende contexto de
uso (por exemplo, não diferencia um sapato social de aparência parecida com
um tênis casual se a silhueta for muito próxima). Para produção, a evolução
natural seria combinar essa similaridade visual com dados textuais/categóricos
em um sistema híbrido, e trocar a busca por força bruta por um índice vetorial
como FAISS para escalar a milhões de produtos.

---
*Baseado na estrutura conceitual de:*
https://colab.research.google.com/github/sparsh-ai/rec-tutorials/blob/master/_notebooks/2021-04-27-image-similarity-recommendations.ipynb
