<script setup>
import { ref, computed } from 'vue'
import { livros } from '@/_data/livros.js'

const carrinho = ref({
  itens: [],
  total: 0
})

const adicionarAoCarrinho = (livro) => {
  const index = carrinho.value.itens.findIndex((item) => item.id === livro.id)
  if (index === -1) {
    const novoItem = {
      ...livro,
      quantidade: 1,
      total: livro.price
    }
    carrinho.value.itens.push(novoItem)
  } else {
    const itemExistente = carrinho.value.itens[index]
    itemExistente.quantidade++
    itemExistente.total += livro.price
  }
  carrinho.value.total += livro.price
}

const removerItemCarrinho = (item) => {
  const index = carrinho.value.itens.findIndex((i) => i.id === item.id)
  carrinho.value.total -= item.total
  carrinho.value.itens.splice(index, 1)
}

const atualizarQuantidadeItem = (item) => {
  carrinho.value.total -= item.total
  item.total = item.price * item.quantidade
  carrinho.value.total += item.total
}

const formatarPreco = (preco) => {
  return 'R$ ' + preco.toFixed(2).replace('.', ',')
}

const mostrarFiltros = ref(false)

const alternarFiltros = () => {
  mostrarFiltros.value = !mostrarFiltros.value
}

const categorias = ref({
  fantasia: false,
  violencia: false,
  infantojuvenil: false,
  medieval: false,
})

const livrosFiltrados = computed(() => {
  return livros.filter((livro) => {
    if (
      (categorias.value.fantasia && livro.class === "fantasia") ||
      (categorias.value.violencia && livro.class === "violencia") ||
      (categorias.value.infantojuvenil && livro.class === "infantojuvenil") ||
      (categorias.value.medieval && livro.class === "medieval")
    ) {
      return true;
    }
    return false;
  });
});

const aplicarFiltro = () => {
  mostrarFiltros.value = false;
}

</script>

<template>
  <main>
    <div class="header-container">
      <div class="header-bar"></div>
      <h1 class="titulo-pagina">Minha Livraria</h1>
      <button class="filtrar-button" @click="alternarFiltros">Filtrar</button>
      <div v-if="mostrarFiltros" class="filtros-container">
        <div class="filtro">
          <input type="checkbox" v-model="categorias.fantasia" />
          <label class="filtros">Fantasia</label>
        </div>
        <div class="filtro">
          <input type="checkbox" v-model="categorias.violencia" />
          <label class="filtros">Violência</label>
        </div>
        <div class="filtro">
          <input type="checkbox" v-model="categorias.infantojuvenil" />
          <label class="filtros">Infantojuvenil</label>
        </div>
        <div class="filtro">
          <input type="checkbox" v-model="categorias.medieval" />
          <label class="filtros">Medieval</label>
        </div>
      </div>
    </div>
    <div class="container-geral">
    <div class="listagem-livros">
      <div class="card-livro" v-for="livro in livrosFiltrados" :key="livro.id">
          <div class="wrap-livro">
            <img :src="livro.img" alt="Capa do livro" class="capa-livro" />
          </div>
          <p class="titulo-livro">{{ livro.title }}</p>
          <p class="autor-livro">{{ livro.author }}</p>
          <p class="preco-livro">{{ formatarPreco(livro.price) }}</p>
          <button @click="adicionarAoCarrinho(livro)">Adicionar ao carrinho</button>
        </div>
      </div>
      <div class="carrinho">
        <h2>Meu carrinho</h2>
        <p v-if="carrinho.itens.length === 0">Seu carrinho está vazio</p>
        <div v-else>
          <div class="item-carrinho" v-for="(item, index) in carrinho.itens" :key="index">
            <div class="info-livro">
              <div class="imagem-livro">
                <img :src="item.img" class="icon-capa-livro" />
              </div>
              <div class="detalhes-livro">
                <div>
                  <p>{{ item.title }}</p>
                  <p class="info-livro-preco">{{ formatarPreco(item.price) }}/un</p>
                </div>
                <div>
                  <label for="quantidade">Quantidade:</label>
                  <input
                    type="number"
                    v-model="item.quantidade"
                    @input="atualizarQuantidadeItem(item)"
                    min="1"
                  />
                </div>
              </div>
            </div>
            <button @click="removerItemCarrinho(item)">&#128465;</button>
          </div>
        </div>
        <p>Total: {{ formatarPreco(carrinho.total) }}</p>
      </div>
    </div>
  </main>
</template>

<style scoped>

.filtrar-button {
  background-color: #73ac31;
  color: black;
  border: none;
  border-radius: 5px;
  padding: 5px 10px;
  cursor: pointer;
  margin-left: 10px;
}

.filtros-container {
  margin-top: 10px;
  border: 1px solid #73ac31;
  border-radius: 5px;
  padding: 10px;
}

.filtro {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
}

.filtros {
  color: black;
}
.header-container {
  position: relative;
}
.header-bar {
  background-color: #73ac31;
  height: 100px;
  width: 100%;
  
}

.titulo-pagina {
  text-align: center;
  color: #184a4a;
  margin: 20px auto 0;
  font-size: 28px;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1;
}

.info-livro {
  display: flex;
  margin-bottom: 10px;
}

.detalhes-livro {
  display: flex;
  flex-direction: column;
  width: 100%;
}

.detalhes-livro p {
  margin: 0;
}

.detalhes-livro div {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.detalhes-livro input[type="number"] {
  width: 50px;
  text-align: center;
  border: none;
  border-bottom: 1px solid black;
  background-color: transparent;
  margin-left: 10px;
}

.detalhes-livro button {
  background-color: transparent;
  border: none;
  cursor: pointer;
  font-size: 1.5rem;
  color: black;
  padding: 0;
  margin: 0;
}

.info-livro-preco {
  margin-left: auto;
}

.icon-capa-livro {
  width: 30px;
  margin-right: 10px;
}

.container-geral {
  display: grid;
  grid-template-columns: 3fr 1fr;
  gap: 20px;
}

.carrinho {
  background-color: #83eec5;
  padding: 20px;
  border-radius: 10px;
}

.listagem-livros {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 20px;
}

.card-livro {
  padding: 10px;
  background-color: #62d5b4;
  border-radius: 10px;
  width: 100%;
}

.wrap-livro {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: white;
  border-radius: 10px;
  width: 100%;
  height: 270px;
}

.capa-livro {
  width: 90%;
  max-height: 100%;
}

.card-livro p {
  margin: 0;
}

.card-livro .titulo-livro {
  font-weight: bold;
  margin-bottom: 5px;
}

h1 {
  color: #73ac31;
}

h2 {
  color: #73ac31;
  margin-bottom: 10px;
}

button {
  background-color: #73ac31;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 5px 10px;
  cursor: pointer;
}

@media screen and (max-width: 768px) {
  .container-geral {
    grid-template-columns: 1fr;
  }

  .listagem-livros {
    grid-template-columns:1fr;
  }
}
</style>
