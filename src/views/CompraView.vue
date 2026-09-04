<script setup>
import { ref, computed } from 'vue'
import { pedidos } from '@/data/pedidos'

// Dados do pedido
const codigoPedido = ref('')
const nomeCliente = ref('')

// Dados do produto
const nomeProduto = ref('')
const precoUnitario = ref(0)
const quantidade = ref(1)

// Lista de produtos do pedido atual
const itens = ref([])

// Mensagem de erro
const mensagem = ref('')

// Adicionar produto
function adicionarProduto() {
  mensagem.value = ''

  if (
    nomeProduto.value === '' ||
    precoUnitario.value <= 0 ||
    quantidade.value < 1
  ) {
    mensagem.value = 'Preencha corretamente os dados do produto.'
    return
  }

  itens.value.push({
    id: Date.now(),
    produto: nomeProduto.value,
    precoUnitario: Number(precoUnitario.value),
    quantidade: Number(quantidade.value),
  })

  // Limpa os campos do produto
  nomeProduto.value = ''
  precoUnitario.value = 0
  quantidade.value = 1
}

// Excluir produto
function excluirProduto(id) {
  itens.value = itens.value.filter((item) => item.id !== id)
}

// Total da compra
const totalCompra = computed(() => {
  return itens.value.reduce((total, item) => {
    return total + item.precoUnitario * item.quantidade
  }, 0)
})

// Limpar pedido
function limpar() {
  codigoPedido.value = ''
  nomeCliente.value = ''
  nomeProduto.value = ''
  precoUnitario.value = 0
  quantidade.value = 1
  itens.value = []
  mensagem.value = ''
}

// Finalizar pedido
function finalizarPedido() {
  mensagem.value = ''

  if (codigoPedido.value === '' || nomeCliente.value === '') {
    mensagem.value = 'Preencha o código do pedido e o nome do cliente.'
    return
  }

  if (itens.value.length === 0) {
    mensagem.value = 'Adicione pelo menos um produto ao pedido.'
    return
  }

  pedidos.value.push({
    codigo: codigoPedido.value,
    cliente: nomeCliente.value,
    itens: itens.value,
  })

  mensagem.value = 'Pedido finalizado com sucesso!'

  // Limpa os dados depois de finalizar
  codigoPedido.value = ''
  nomeCliente.value = ''
  itens.value = []
}
</script>

<template>
  <main class="container page">
    <header class="page-header">
      <h1>Fazer compra</h1>
      <p>
        Cadastre o cliente e adicione os produtos do pedido.
      </p>
    </header>

    <section class="card" aria-labelledby="dados-pedido">
      <h2 id="dados-pedido">Dados do pedido</h2>

      <div class="form-grid form-grid-two-columns">
        <div class="form-group">
          <label for="codigoPedido">
            Código do pedido
          </label>

          <input
            id="codigoPedido"
            name="codigoPedido"
            type="text"
            placeholder="Ex.: PED-001"
            v-model="codigoPedido"
          />
        </div>

        <div class="form-group">
          <label for="nomeCliente">
            Nome do cliente
          </label>

          <input
            id="nomeCliente"
            name="nomeCliente"
            type="text"
            placeholder="Digite o nome do cliente"
            v-model="nomeCliente"
          />
        </div>
      </div>

      <p v-if="mensagem">
        {{ mensagem }}
      </p>
    </section>

    <section class="card" aria-labelledby="adicionar-produto">
      <h2 id="adicionar-produto">Adicionar produto</h2>

      <div class="form-grid form-grid-product">
        <div class="form-group">
          <label for="nomeProduto">
            Produto
          </label>

          <input
            id="nomeProduto"
            name="nomeProduto"
            type="text"
            placeholder="Ex.: Tomate"
            v-model="nomeProduto"
          />
        </div>

        <div class="form-group">
          <label for="precoUnitario">
            Preço unitário
          </label>

          <input
            id="precoUnitario"
            name="precoUnitario"
            type="number"
            min="0"
            step="0.01"
            placeholder="0,00"
            v-model="precoUnitario"
          />
        </div>

        <div class="form-group">
          <label for="quantidade">
            Quantidade
          </label>

          <input
            id="quantidade"
            name="quantidade"
            type="number"
            min="1"
            step="1"
            placeholder="0"
            v-model="quantidade"
          />
        </div>
      </div>

      <div class="form-actions">
        <button
          class="button button-primary"
          type="button"
          @click="adicionarProduto"
        >
          Adicionar produto
        </button>
      </div>
    </section>

    <section class="card" aria-labelledby="itens-pedido">
      <h2 id="itens-pedido">Itens do pedido</h2>

      <p v-if="itens.length === 0">
        Nenhum produto foi adicionado ao pedido.
      </p>

      <div v-else class="table-responsive">
        <table>
          <thead>
            <tr>
              <th scope="col">Produto</th>
              <th scope="col">Preço unitário</th>
              <th scope="col">Quantidade</th>
              <th scope="col">Total</th>
              <th scope="col">Ação</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="item in itens"
              :key="item.id"
            >
              <td>{{ item.produto }}</td>

              <td>
                R$ {{ item.precoUnitario.toFixed(2).replace('.', ',') }}
              </td>

              <td>{{ item.quantidade }}</td>

              <td>
                R$
                {{
                  (item.precoUnitario * item.quantidade)
                    .toFixed(2)
                    .replace('.', ',')
                }}
              </td>

              <td>
                <button
                  type="button"
                  @click="excluirProduto(item.id)"
                >
                  Excluir
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="order-total">
        <span>Total da compra</span>

        <strong>
          R$ {{ totalCompra.toFixed(2).replace('.', ',') }}
        </strong>
      </div>

      <div class="form-actions">
        <button
          class="button button-secondary"
          type="button"
          @click="limpar"
        >
          Limpar
        </button>

        <button
          class="button button-primary"
          type="button"
          @click="finalizarPedido"
        >
          Finalizar pedido
        </button>
      </div>
    </section>
  </main>
</template>