<script setup>
import { ref, computed } from 'vue'
import { pedidos } from '@/data/pedidos'

const filtro = ref('')

const pedidosFiltrados = computed(() => {
  if (filtro.value === '') {
    return pedidos.value
  }

  return pedidos.value.filter((pedido) => {
    return (
      pedido.codigo.toLowerCase().includes(filtro.value.toLowerCase()) ||
      pedido.cliente.toLowerCase().includes(filtro.value.toLowerCase())
    )
  })
})

const totalItens = computed(() => {
  return pedidos.value.reduce((total, pedido) => {
    return (
      total +
      pedido.itens.reduce((soma, item) => {
        return soma + item.quantidade
      }, 0)
    )
  }, 0)
})

const totalVendido = computed(() => {
  return pedidos.value.reduce((total, pedido) => {
    return (
      total +
      pedido.itens.reduce((soma, item) => {
        return soma + item.precoUnitario * item.quantidade
      }, 0)
    )
  }, 0)
})

function filtrar() {
  // O filtro já é atualizado automaticamente pelo computed
}
</script>

<template>
  <main class="container page">
    <header class="page-header">
      <h1>Resumo dos pedidos</h1>
      <p>
        Consulte os pedidos finalizados e o total vendido.
      </p>
    </header>

    <section
      class="summary-grid"
      aria-label="Resumo geral das vendas"
    >
      <article class="summary-card">
        <span>Pedidos realizados</span>

        <strong>{{ pedidos.length }}</strong>
      </article>

      <article class="summary-card">
        <span>Itens vendidos</span>

        <strong>{{ totalItens }}</strong>
      </article>

      <article class="summary-card">
        <span>Total vendido</span>

        <strong>R$ {{ totalVendido.toFixed(2).replace('.', ',') }}</strong>
      </article>
    </section>

    <section class="card" aria-labelledby="filtro-pedidos">
      <h2 id="filtro-pedidos">Filtrar pedidos</h2>

      <div class="filter-container">
        <div class="form-group">
          <label for="filtro">
            Nome do cliente ou código do pedido
          </label>

          <input
            id="filtro"
            name="filtro"
            type="search"
            placeholder="Digite o cliente ou código"
            v-model="filtro"
          />
        </div>

        <button
          class="button button-primary"
          type="button"
          @click="filtrar"
        >
          Filtrar
        </button>
      </div>
    </section>

    <section class="card" aria-labelledby="pedidos-realizados">
      <h2 id="pedidos-realizados">Pedidos realizados</h2>

      <!-- Aparece quando não existe nenhum pedido -->
      <p v-if="pedidosFiltrados.length === 0">
        Nenhum pedido encontrado.
      </p>

      <div
        v-else
        class="table-responsive"
      >
        <table>
          <thead>
            <tr>
              <th scope="col">Código</th>
              <th scope="col">Cliente</th>
              <th scope="col">Produtos</th>
              <th scope="col">Itens</th>
              <th scope="col">Total</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="pedido in pedidosFiltrados"
              :key="pedido.codigo"
            >
              <td>{{ pedido.codigo }}</td>

              <td>{{ pedido.cliente }}</td>

              <td>{{ pedido.itens.length }}</td>

              <td>
                {{
                  pedido.itens.reduce(
                    (total, item) => total + item.quantidade,
                    0
                  )
                }}
              </td>

              <td>
                R$
                {{
                  pedido.itens
                    .reduce(
                      (total, item) =>
                        total + item.precoUnitario * item.quantidade,
                      0
                    )
                    .toFixed(2)
                    .replace('.', ',')
                }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
  </main>
</template>